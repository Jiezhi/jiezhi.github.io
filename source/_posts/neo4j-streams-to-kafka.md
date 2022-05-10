---
title: Neo4j CDC 至 Kafka
date: 2022-05-10 16:58:19
categories:
  - BigData
tags:
  - Neo4j
  - Kafka
  - CDC
  - Stream
---

本文介绍如何监听 Neo4j 变更数据并发送到 kafka 。

<!--more-->

# 背景
使用 Neo4j 构建商品知识图谱，同时通过 [spaCy](https://spacy.io/) 导入图谱中的实体词对用户搜索词汇进行 [NER](https://en.wikipedia.org/wiki/Named-entity_recognition)（命名实体识别）。

# 问题
初始化时还可以直接读取全量的实体词，但是增量就比较麻烦了，毕竟不知道什么时候图谱里数据变动了。当然可以通过更新 Neo4j 的程序同时更新 spaCy 里的实体词，但是遇到复杂场景还是束手无策了。

目前在关系型数据库场景下，像 MySQL 这种 CDC 之前都是用 Canal 监听 binlog 然后发送到 Kafka 来解决，近期也可以通过 Flink CDC 来处理数据。

所以在 Neo4j 场景下，更倾向于使用 CDC 技术来解决这个问题，后期的扩展性也较高。

# 解决
官方提供了[neo4j-streams](https://neo4j.com/labs/kafka/4.0/)项目，提供了两种解决方案：
1. Neo4j 插件
2. Kafka 插件

本文介绍如何使用`方案1`来解决上述问题。

---

软件环境（截止2022年05月10日）：
```
neo4j on docker, version 4.4.5
neo4j-streams, version 4.1.2
```
因为使用 Docker 版，里面会隐藏一些坑。

## Docker 启动脚本

```bash
docker run \
    --name testneo4j \
    -p7474:7474 -p7687:7687 \ 
    -d \
    -v $HOME/neo4j/data:/data \
    -v $HOME/neo4j/logs:/logs \
    -v $HOME/neo4j/import:/var/lib/neo4j/import \
    -v $HOME/neo4j/plugins:/plugins \
    -v $HOME/neo4j/conf:/conf \
    --env NEO4J_AUTH=neo4j/yourpasswd \
    neo4j:4.4.5
```
挂载目录默认为`$HOME/neo4j`,有需要可以自己修改。
这里重点注意把容器里`/conf`目录挂载出来，待会要加自定义配置。

此外因为 docker 默认的网络模式为 `bridge`，这时候如果在局域网内配置了 kafka，从容器内是无法访问类似192.168.x.x 的服务地址的。这里我选择改变容器运行模式：

```bash
docker run \
    --name testneo4j \
    --network="host" \ 
    -d \
    -v $HOME/neo4j/data:/data \
    -v $HOME/neo4j/logs:/logs \
    -v $HOME/neo4j/import:/var/lib/neo4j/import \
    -v $HOME/neo4j/plugins:/plugins \
    -v $HOME/neo4j/conf:/conf \
    --env NEO4J_AUTH=neo4j/yourpasswd \
    neo4j:4.4.5
```
这时候容器以 `host` 模式运行，可以访问局域网内的 kafka 服务。

> 该问题可以参见：https://stackoverflow.com/a/24326540/5425709

## 配置 streams 插件
启动容器后，在目录`$HOME/neo4j`下会生成对应的挂载目录。
从 [github](https://github.com/neo4j-contrib/neo4j-streams/releases) 上下载neo4j-streams-4.1.2.jar，放置在`$HOME/neo4j/plugins`目录下。

同时在 `$HOME/neo4j/conf` 目录下新建`streams.conf`文件，并加入如下内容：
```bash
kafka.bootstrap.servers=192.168.3.111:9092,192.168.3.112:9092,192.168.3.113:9092
streams.source.topic.nodes.neo4j-label-brand=Brand{*}
streams.source.enabled=true
streams.source.schema.polling.interval=10000
```
上述配置文件可以做到如果 `Brand` 实体发生变化，将发送 CDC 数据至 Kafka 名为`neo4j-label-brand` 的 topic。
实体和关系的监听语法如下，更多配置可参见[文档](https://neo4j.com/labs/kafka/4.0/producer/)：
```bash
streams.source.topic.nodes.<TOPIC_NAME>=<PATTERN>
streams.source.topic.relationships.<TOPIC_NAME>=<PATTERN>
```
然后重启容器，`docker restart testneo4j`。

## 监听变更
### Create

```sql
CREATE (b:Brand {name: '123'}) RETURN b;
```
此时Kafka收到的 消息内容为：
```json
{
    "meta": {
        "timestamp": 1652172619302,
        "username": "neo4j",
        "txId": 438650,
        "txEventId": 0,
        "txEventsCount": 1,
        "operation": "created",
        "source": {
            "hostname": "test01.xxx.cn"
        }
    },
    "payload": {
        "id": "63330",
        "before": null,
        "after": {
            "properties": {
                "name": "123"
            },
            "labels": [
                "Brand"
            ]
        },
        "type": "node"
    },
    "schema": {
        "properties": {
            "name": "String"
        },
        "constraints": [
            {
                "label": "Brand",
                "properties": [
                    "name"
                ],
                "type": "UNIQUE"
            }
        ]
    }
}
```

### Update
```sql
MATCH (n:Brand) WHERE n.name = '123' SET n.tableId = 1 RETURN n;
```
此时Kafka收到的 消息内容为：
```json
{
    "meta": {
        "timestamp": 1652172703813,
        "username": "neo4j",
        "txId": 438651,
        "txEventId": 0,
        "txEventsCount": 1,
        "operation": "updated",
        "source": {
            "hostname": "test01.xxx.cn"
        }
    },
    "payload": {
        "id": "63330",
        "before": {
            "properties": {
                "name": "123"
            },
            "labels": [
                "Brand"
            ]
        },
        "after": {
            "properties": {
                "name": "123",
                "tableId": 1
            },
            "labels": [
                "Brand"
            ]
        },
        "type": "node"
    },
    "schema": {
        "properties": {
            "name": "String",
            "tableId": "Long"
        },
        "constraints": [
            {
                "label": "Brand",
                "properties": [
                    "name"
                ],
                "type": "UNIQUE"
            }
        ]
    }
}
```

### Delete
```sql
MATCH (n:Brand) WHERE n.name = '123' DELETE n;
```
此时Kafka收到的 消息内容为：
```json
{
    "meta": {
        "timestamp": 1652172749584,
        "username": "neo4j",
        "txId": 438652,
        "txEventId": 0,
        "txEventsCount": 1,
        "operation": "deleted",
        "source": {
            "hostname": "test01.xxx.cn"
        }
    },
    "payload": {
        "id": "63330",
        "before": {
            "properties": {
                "name": "123",
                "tableId": 1
            },
            "labels": [
                "Brand"
            ]
        },
        "after": null,
        "type": "node"
    },
    "schema": {
        "properties": {
            "name": "String",
            "tableId": "Long"
        },
        "constraints": [
            {
                "label": "Brand",
                "properties": [
                    "name"
                ],
                "type": "UNIQUE"
            }
        ]
    }
}
```

接下来再写个 kafka 消费者即可，至此数据流程打通。