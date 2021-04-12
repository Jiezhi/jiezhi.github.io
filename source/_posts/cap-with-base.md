title: CAP定理与BASE理论
author: Jiezhi.G
email: Jiezhi.G@gmail.com
categories:
  - NoSQL
premalink: fix_gravatar
metaAlignment: center
comments: true
date: 2019-02-25 10:29:25
url:
tags:
  - NoSQL
  - CAP
  - BASE
  - ACID
photos:
---
CAP: 是指 Consistency(一致性), Availability(可用性) 和 Partition Tolerance(可用性).
BASE: **B**asically **A**vailable（基本可用）, **S**oft state（软状态）和 **E**ventually consistent（最终一致性）
<!--more-->
## CAP 定理
{% asset_img cap.png cap %}
- **Consistency** **一致性**（节点或系统中所有可用数据）

    在分布式环境下，一致性是指数据在多个副本之间能否保持一致的特性。在一致性的需求下，当一个系统在数据一致的状态下执行更新操作后，应该保证系统的数据仍然处于一直的状态。

- **Availability** **可用性**（每个请求会得到一个回应）

    可用性是指系统提供的服务必须一直处于可用的状态，对于用户的每一个操作请求总是能够在有限的时间内返回结果。这里的重点是"有限时间内"和"返回结果"。

- **Partition Tolerance 分区容错性**（系统运作将不管可用性、分区、数据或通信的丢失）

    分区容错性约束了一个分布式系统具有如下特性：分布式系统在遇到任何网络分区故障的时候，仍然需要能够保证对外提供满足一致性和可用性的服务，除非是整个网络环境都发生了故障。

## BASE

- **B**asically **A**vailable（基本可用）

    分布式系统在出现不可预知故障的时候，允许损失部分可用性

- **S**oft state（软状态）

    软状态也称为弱状态，和硬状态相对，是指允许系统中的数据存在中间状态，并认为该中间状态的存在不会影响系统的整体可用性，即允许系统在不同节点的数据副本之间进行数据听不的过程存在延时。

- **E**ventually consistent（最终一致性）

    最终一致性强调的是系统中所有的数据副本，在经过一段时间的同步后，最终能够达到一个一致的状态。因此，最终一致性的本质是需要系统保证最终数据能够达到一致，而不需要实时保证系统数据的强一致性

CAP 与 BASE 关系为：

> 在分布式的数据系统中，你能保证下面三个要求中的两个：一致性，可用性，以及分区容错性。在此模型上构建的系统将称作 BASE(基本上可用软状态最终一致)架构，不满足 ACID 性质。

---

## ACID

- Atomicity 原子性

    一个事务必须被视为一个不可分割的最小工作单元。

- Consistency 一致性

    数据库总是从一个一致性的状态转换到另外一个一致性的状态。

- Isolation 隔离性

    通常来说，一个事务所做的修改在最终提交之前，对其他事物是不可见的。

- Durability 持久性

    一旦事务提交，则其所做的修改就会永久保存到数据库中。

---

## 举例

- [HBase](https://hbase.apache.org/)、[HyperTable](http://www.hypertable.org/) 和 [BigTable](https://www.wikiwand.com/en/Bigtable)，满足 CP 性质。
- [Cassandra](http://cassandra.apache.org/)、[Dynamo](https://aws.amazon.com/dynamodb/) 和 [Voldemort](https://www.project-voldemort.com/voldemort/)，满足 AP性质。

---

Reference：

《高性能MySQL》

《大数据与数据仓库》

[CAP原则(CAP定理)、BASE理论 - duanxz - 博客园](https://www.cnblogs.com/duanxz/p/5229352.html)

[CAP和BASE理论的关系 - leooooo的个人空间 - 开源中国](https://my.oschina.net/u/1175305/blog/1998062)
