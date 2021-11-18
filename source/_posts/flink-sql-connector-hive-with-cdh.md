title: Flink SQL Client with CDH Hive
author: Jiezhi.G
email: Jiezhi.G@gmail.com
categories:
  - Flink
premalink: fix_gravatar
metaAlignment: center
comments: true
date: 2021-11-18 18:35:26
url:
tags:
  - Flink
  - CDH
  - Hive

---
解决`Unrecognized Hadoop major version number: 3.0.0-cdh6.3.1`问题。
<!--more-->
# 背景
在集成 Flink CDC 至 Hudi 并同步至 Hive 过程中，通过Flink yarn session 到 CDH 集群上开启 Session：
`./bin/yarn-session.sh --detached -tm 16GB -s 32 --name flink-yarn-session --queue root.users.flink`
然后启动 SQL Client：
`./bin/sql-client.sh embedded -i init_sql.sql -s yarn-session`
执行任务过程中会遇到这个问题：
`Unrecognized Hadoop major version number: 3.0.0-cdh6.3.1`

# 原因
flink 默认的 Hive connector的里不能识别`3.0.0-cdh6.3.1`这个版本号，所以需要自己编译一下 flink-sql-connecor-hive。

# 解决
flink-connectors/flink-sql-connector-hive-2.2.0/pom.xml
```xml
<groupId>org.apache.hive</groupId>
<artifactId>hive-exec</artifactId>
<version>2.1.1-cdh6.3.1</version>
```
同时加一下 cdh 的 repo：
```xml
<repositories>
    <repository>
      <id>cloudera</id>
      <url>https://repository.cloudera.com/artifactory/cloudera-repos/</url>
    </repository>
  </repositories>
```

`mvn clean package -DskipTests`

或者

`mvn clean package -DskipTests -rf :flink-sql-connector-hive-2.2.0_2.11`
