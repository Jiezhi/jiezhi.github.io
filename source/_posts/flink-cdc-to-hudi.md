---
title: Flink CDC 同步数据至 Hudi
date: 2022-06-24 13:55:24
categories:
  - BigData
tags:
  - Flink
  - CDC
  - Hudi
  - Stream
  - Datalake
---

本文介绍通过 Flink CDC 方式实时同步 MySQL 数据至 Hudi（或 Hive）的过程。

<!--more-->
# 环境准备
## 软件环境
* [Apache Hudi 0.11.1](https://hudi.apache.org/releases/release-0.11.1)
* [Flink CDC 2.2.1](https://github.com/ververica/flink-cdc-connectors/releases)，支持的数据库可见[官方文档](https://ververica.github.io/flink-cdc-connectors/master/content/about.html)。
* [Apache Flink 1.14.4](https://nightlies.apache.org/flink/flink-docs-release-1.14/)
(因为目前 Hudi 和 CDC 插件最高支持 Flink 1.14，所以这里取最高版本1.14.4)

## 数据库
* MySQL 5.6 
如果需要自己准备，可以使用 docker 创建一个
```shell
docker run -it --rm --name mysql -p 3306:3306
 -e MYSQL_ROOT_PASSWORD=debezium -e MYSQL_USER=mysqluser
  -e MYSQL_PASSWORD=mysqlpw debezium/example-mysql:1.6
```
* Hive 2.2.1-cdh6.3.1

# 配置
## MySQL
单独创建一个可以读取 binlog 的用户：
```sql
CREATE USER 'debezium'@'%' IDENTIFIED BY 'xxx';
GRANT SELECT, RELOAD, SHOW DATABASES, REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'debezium' IDENTIFIED BY 'xxx';
FLUSH PRIVILEGES;
```

## Flink
主要是开启 checkpoint，以及运行在 YARN 的配置，修改的地方可参考：

```yaml

jobmanager.rpc.address: localhost
jobmanager.rpc.port: 6123

jobmanager.memory.process.size: 4g
taskmanager.memory.process.size: 16g
taskmanager.numberOfTaskSlots: 4
parallelism.default: 1
#
execution.checkpointing.interval: 3min
# execution.checkpointing.externalized-checkpoint-retention: [DELETE_ON_CANCELLATION, RETAIN_ON_CANCELLATION]
execution.checkpointing.externalized-checkpoint-retention: DELETE_ON_CANCELLATION
execution.checkpointing.max-concurrent-checkpoints: 1
execution.checkpointing.min-pause: 0
execution.checkpointing.mode: EXACTLY_ONCE
execution.checkpointing.timeout: 20min
execution.checkpointing.tolerable-failed-checkpoints: 10
# execution.checkpointing.unaligned: false

execution.checkpointing.unaligned: true
state.backend: rocksdb
state.backend.incremental: true
state.backend.rocksdb.localdir: /data/flink/rocksdb,/data1/flink/rocksdb

state.checkpoints.dir: hdfs://xxx:8020/flink/1_14_4/flink-checkpoints
state.savepoints.dir: hdfs://xxx:8020/flink/1_14_4/flink-savepoints

execution.target: yarn-per-job

jobmanager.execution.failover-strategy: region
```

## Flink Catalog 配置
因为我是用 Flink sql-client 来执行的，所以配置如下：
init.sql

```sql
CREATE CATALOG HiveCatalog WITH (
  'type' = 'hive',
  'default-database' = 'default',
  'hive-conf-dir' = '/etc/hive/conf'
);

USE CATALOG HiveCatalog;
```

这样，启动 sql-client可以指定该配置文件，进入交互终端：
`./bin/sql-client.sh -i conf/init.sql`

如果后面 CDC 流程保存为 sql 文件或，再通过`-f`指定执行即可：
`./bin/sql-client.sh -i conf/init.sql -f sql/test.sql`

# 执行 SQL 模板
```sql
set yarn.application.name=FlinkCDC-yourappname;

set pipeline.name=FlinkCDC-pipelinename;

set yarn.application.queue=users.cdc;

SET execution.checkpointing.interval=5min;

SET parallelism.default = 4;

DROP TABLE IF EXISTS test_flink;

CREATE TABLE IF NOT EXISTS `test_flink` (
  `id` bigint,
  `db_id` STRING,
    ...
  `gmtupdated` STRING,
  PRIMARY KEY (id) NOT ENFORCED
 ) WITH (
   'connector' = 'mysql-cdc',
   'hostname' = 'xxx.mysql.polardb.rds.aliyuncs.com',
   'port' = '3306',
   'username' = 'debezium',
   'password' = 'xxx',
   'server-id' = '5500-5508', -- 默认为随机 id，但为了避免与其他任务冲突，最好指定，范围需大于 source 的并行度，即要大于 parallelism.default 的值
   'server-time-zone' = 'Asia/Shanghai',
   'database-name' = 'db_name',
   'table-name' = 'table'
 );


DROP TABLE IF EXISTS test_hudi;

CREATE TABLE IF NOT EXISTS `test_hudi` (
  `id` bigint PRIMARY KEY,
  ...
  `db_id` STRING,
 )  PARTITIONED BY (`db_id`)
WITH (
  'connector' = 'hudi',
  'write.operation' = 'upsert',
  'write.precombine' = 'true',
  'write.precombine.field' = 'gmtupdated',
  'read.streaming.enabled' = 'true',
  'write.rate.limit' = '8000', -- 如果数据量较大，需要控制写入速度的话
  'path' = 'hdfs://xxx:8020/hudi/flink/xxx',
  'hive_sync.enable' = 'true',     -- 同步至 Hive 库中
  'hive_sync.mode' = 'hms',         -- Required. Setting hive sync mode to hms, default jdbc
  'hive_sync.table'='xxx',                          -- required, hive table name
  'hive_sync.db'='ods',
  'hive_sync.metastore.uris' = 'thrift://xxx:9083', --
  'table.type' = 'MERGE_ON_READ' -- this creates a MERGE_ON_READ table, by default is COPY_ON_WRITE
);

INSERT INTO xxx_hudi SELECT * FROM xxx_flink;
```

在任务运行后，Flink checkpoint 之后，可以看到 hive 指定的库中多了从 hudi 同步过来的表，COW 为一张，MOR 为两张。

## Hive 读取
Hive 直接读取 Hudi 同步过来的表，有可能会有重复数据。此时需要设置一下：

`set hive.input.format=org.apache.hudi.hadoop.hive.HoodieCombineHiveInputFormat;`
或者

`set hive.input.format= org.apache.hadoop.hive.ql.io.HiveInputFormat;`

下面老版本 hudi 文档里提到的设置应该已经失效：
`set hive.input.format=org.apache.hudi.hadoop.HoodieParquetInputFormat;`