title: Flink 分布式运行时环境（Distributed Runtime Environment）
author: Jiezhi.G
email: Jiezhi.G@gmail.com
categories:
  - Flink
premalink: fix_gravatar
metaAlignment: center
comments: true
date: 2019-03-08 14:26:26
url:
tags:
  - Flink
  - Translate
photos:
  - images/flink-header-logo.svg
display_in_post: false
---
本篇文章介绍 Flink 的分布式运行时环境相关的概念。
<!--more-->

本文译自[Flink官网](https://ci.apache.org/projects/flink/flink-docs-release-1.7/concepts/runtime.html)

## 任务和操作链

对于分布式的执行，Flink将操作子任务*链接(chain)*到*任务(task)*中。每个任务由一个线程执行。将操作链接到任务是一项有用的优化：它可以减少线程到线程切换和缓冲的开销，并在降低延迟的同时提高整体吞吐量。链接行为可以配置;有关详细信息，请参阅[链接文档](https://ci.apache.org/projects/flink/flink-docs-release-1.7/dev/stream/operators/#task-chaining-and-resource-groups)。

下图中的示例数据流由五个子任务执行，因此具有五个并行线程。

{% asset_img tasks_chains.svg %}
操作链接导任务

## 作业管理器，任务管理器和客户端

Flink运行时包含两种类型的进程：

- 作业管理器（**JobManagers，**也称为*master*）协调分布式执行。他们安排任务，协调检查点，协调故障恢复等。

    至少有一个Job Manager。设置高可用（HA, High-Availability）的话将具有多个JobManagers，其中一个始终是*领导者(leader)*，其它的处于*待机状态(standby)*。

- 任务管理器（**TaskManagers，**也称为*worker*）执行数据流的任务（或更具体地说，子任务），并缓冲和交换数据流。

    必须始终至少有一个TaskManager。

JobManagers和TaskManagers可以通过多种方式启动：[独立集群](https://ci.apache.org/projects/flink/flink-docs-release-1.7/ops/deployment/cluster_setup.html)直接在机器里启动，或在容器中，或由[YARN](https://ci.apache.org/projects/flink/flink-docs-release-1.7/ops/deployment/yarn_setup.html)或[Mesos](https://ci.apache.org/projects/flink/flink-docs-release-1.7/ops/deployment/mesos.html)等资源框架来管理。 TaskManagers连接到JobManagers，通知自己可用，然后被分配任务。

**客户端**不是运行时和程序执行的一部分，而是被用于准备数据流并将数据流发送到JobManager。之后，客户端可以断开连接或保持连接以接收进度报告。客户端要么作为触发执行的Java / Scala程序的一部分运行，要么是在命令行进程中运行 ./bin/flink run ....

{% asset_img processes.svg %}
执行Flink数据流所涉及的过程

## 任务槽和资源

每个worker（TaskManager）都是一个*JVM进程*，可以在不同的线程中执行一个或多个子任务。为了控制worker接受的任务数量，worker中有**任务槽（task slots）**（至少一个）。

每个*任务槽*代表TaskManager的固定资源子集。例如，具有三个任务槽的TaskManager会将其1/3的托管内存分配到每个插槽。切换资源意味着子任务不会与来自其他作业的子任务竞争托管内存，而是竞争具有一定量的保留托管内存。请注意，这里没有CPU隔离;当前任务槽只分离任务的托管内存。

通过调整任务槽的数量，用户可以定义子任务之间如何相互隔离。每个TaskManager有一个插槽意味着每个任务组在一个单独的JVM中运行（例如，可以在一个单独的容器中启动）。拥有多个插槽意味着更多子任务共享同一个JVM。同一JVM中的任务共享TCP连接（通过多路复用）和心跳消息。它们还可以共享数据集和数据结构，从而减少每任务开销。

{% asset_img tasks_slots.svg %}
具有任务槽和任务的TaskManager

默认情况下，Flink允许子任务共享插槽，即使它们是不同任务的子任务，只要它们来自同一个作业。结果就是，一个槽可以保存作业的整个管道。允许此插槽共享有两个主要好处：

- Flink集群需要的任务槽数量与作业中使用的最高并行度的任务槽一样多。无需计算程序总共包含多少任务（具有不同的并行性）。
- 更容易获得更好的资源利用率。没有插槽共享，非密集的*source/ map()*子任务将占用与资源密集型*窗口*子任务一样多的资源。通过插槽共享，将示例中的基本并行性从2增加到6可以充分利用时隙资源，同时确保繁重的子任务在TaskManagers之间公平分配。

    {% asset_img slot_sharing.svg %}
    具有共享任务槽的TaskManagers

API还包括[资源组](https://ci.apache.org/projects/flink/flink-docs-release-1.7/dev/stream/operators/#task-chaining-and-resource-groups)机制，可用于防止意外的插槽共享。

根据经验，一个很好的默认任务槽数就是CPU核心数。使用超线程，每个插槽然后需要2个或更多硬件线程。

## State Backends(状态后端?)

存储键/值索引的确切数据结构取决于所选的[state backend](https://ci.apache.org/projects/flink/flink-docs-release-1.7/ops/state/state_backends.html)。一个state backend将数据存储在内存中的哈希映射中，另一个state backend使用[RocksDB](http://rocksdb.org/)作为键/值存储。除了定义保存状态的数据结构之外，state backend还实现了获取键/值状态的时间点快照，并将该快照存储为检查点的一部分的逻辑。

{% asset_img checkpoints.svg %}
检查点和快照

## 保存点（Savepoints）

用Data Stream API编写的程序可以从**保存点**中恢复执行。保存点允许更新程序和Flink群集，而不会丢失任何状态。

[保存点](https://ci.apache.org/projects/flink/flink-docs-release-1.7/ops/state/savepoints.html)是**手动触发的检查点**，它可以生成一个程序的快照并把它写到state backend。 他们依靠常规的检查点机制。 在执行期间，程序会定期在工作节点上创建快照并生成检查点。 对于程序恢复，仅需要最后完成的检查点，并且一旦完成新检查点，就可以安全地丢弃旧检查点。

保存点与这些定期检查点类似，不同之处在于它们由**用户触发**，并且在较新的检查点完成时**不会自动过期**。 可以从[命令行](https://ci.apache.org/projects/flink/flink-docs-release-1.7/ops/cli.html#savepoints)创建保存点，也可以通过**[REST API](https://ci.apache.org/projects/flink/flink-docs-release-1.7/monitoring/rest_api.html#cancel-job-with-savepoint)**取消作业。
