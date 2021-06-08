title: CDH6配置 Atlas，及 Hive Hook
author: Jiezhi.G
email: Jiezhi.G@gmail.com
categories:
  - BigData
premalink: fix_gravatar
metaAlignment: center
comments: true
date: 2020-03-31 15:03:57
url:
tags:
  - Hive
  - CDH
  - Atlas
photos:
---

因为 CDH 社区版不能使用 [Navigator](https://docs.cloudera.com/documentation/enterprise/6/6.3/topics/datamgmt_intro.html#intro_to_cloudera_navigator_data_mgmt)，所以需要自己集成一个[Apache Atlas](https://docs.cloudera.com/documentation/enterprise/6/6.3/topics/datamgmt_intro.html#intro_to_cloudera_navigator_data_mgmt)。

<!--more-->

# 版本说明
20200818 Updated: 目前最新版是2.1.0，如果 Hive 版本是3.1一下的，可能存在不兼容问题。
在编译前修改下源码，参考[这篇](/2020/08/18/atlas2-hive-hook-error/)。

Atlas: 2.0 [Download](https://atlas.apache.org/#/Downloads)


CDH: 6.3.1 (Parcels)

## 其它

Atlas 依赖 Solr（或 ES）、HBase和 Kafka 来工作，先确保 CDH 这3个已经开启，或者编译 Atlas代码时集成进去（生产不建议）。

# 编译

从官网下载Atlas 2.0的代码，然后进行编译，详见官网（基本没啥坑，除非 maven 版本太低，去下一个最新的即可）
```bash
    tar xvfz apache-atlas-2.0.0-sources.tar.gz
    cd apache-atlas-sources-2.0.0/
    export MAVEN_OPTS="-Xms2g -Xmx2g"
    mvn clean -DskipTests install
    mvn clean -DskipTests package -Pdist
```

编译好的位置在`distro/target/`下
```bash
    [root@node1 target]# ll distro/target
    total 738M
    drwxr-xr-x. 3 root root   32 Mar 31 10:08 apache-atlas-2.0.0-bin
    -rw-r--r--. 1 root root 360M Mar 31 10:08 apache-atlas-2.0.0-bin.tar.gz
    drwxr-xr-x. 3 root root   44 Mar 31 10:08 apache-atlas-2.0.0-falcon-hook
    -rw-r--r--. 1 root root 8.8M Mar 31 10:08 apache-atlas-2.0.0-falcon-hook.tar.gz
    drwxr-xr-x. 3 root root   43 Mar 31 10:08 apache-atlas-2.0.0-hbase-hook
    -rw-r--r--. 1 root root  11M Mar 31 10:08 apache-atlas-2.0.0-hbase-hook.tar.gz
    drwxr-xr-x. 3 root root   42 Mar 31 10:08 apache-atlas-2.0.0-hive-hook
    -rw-r--r--. 1 root root  16M Mar 31 10:08 apache-atlas-2.0.0-hive-hook.tar.gz
    drwxr-xr-x. 3 root root   43 Mar 31 10:08 apache-atlas-2.0.0-kafka-hook
    -rw-r--r--. 1 root root 8.8M Mar 31 10:08 apache-atlas-2.0.0-kafka-hook.tar.gz
    drwxr-xr-x. 3 root root   32 Mar 31 10:08 apache-atlas-2.0.0-server
    -rw-r--r--. 1 root root 260M Mar 31 10:08 apache-atlas-2.0.0-server.tar.gz
    -rw-r--r--. 1 root root  11M Mar 31 10:08 apache-atlas-2.0.0-sources.tar.gz
    drwxr-xr-x. 3 root root   43 Mar 31 10:08 apache-atlas-2.0.0-sqoop-hook
    -rw-r--r--. 1 root root 8.8M Mar 31 10:08 apache-atlas-2.0.0-sqoop-hook.tar.gz
    drwxr-xr-x. 3 root root   43 Mar 31 10:08 apache-atlas-2.0.0-storm-hook
    -rw-r--r--. 1 root root  57M Mar 31 10:08 apache-atlas-2.0.0-storm-hook.tar.gz
    drwxr-xr-x. 2 root root    6 Mar 31 10:08 archive-tmp
    -rw-r--r--. 1 root root  94K Mar 31 10:08 atlas-distro-2.0.0.jar
    drwxr-xr-x. 2 root root 4.0K Mar 31 10:08 bin
    drwxr-xr-x. 5 root root  231 Mar 31 10:08 conf
    drwxr-xr-x. 2 root root   28 Mar 31 10:08 maven-archiver
    drwxr-xr-x. 3 root root   22 Mar 31 10:08 maven-shared-archive-resources
    drwxr-xr-x. 2 root root   55 Mar 31 10:08 META-INF
    -rw-r--r--. 1 root root 3.9K Mar 31 10:08 rat.txt
    drwxr-xr-x. 3 root root   22 Mar 31 10:08 test-classes
```
我们直接使用`apache-atlas-2.0.0-bin.tar.gz`和需要 hook 的组件如：`apache-atlas-2.0.0-hive-hook.tar.gz` 、`apache-atlas-2.0.0-sqoop-hook.tar.gz`

## 配置

将需要的 tar.gz文件解压到指定目录如`/opt/atlas`

改几个关键的配置`vim conf/atlas-application.properties`

```bash
    atlas.graph.storage.hostname=node1:2181,node2:2181,node3:2181
    
    #Solr
    #Solr cloud mode properties
    atlas.graph.index.search.solr.mode=cloud
    atlas.graph.index.search.solr.zookeeper-url=node1:2181/solr,node2:2181/solr,node3:2181/solr
    atlas.graph.index.search.solr.zookeeper-connect-timeout=60000
    atlas.graph.index.search.solr.zookeeper-session-timeout=60000
    atlas.graph.index.search.solr.wait-searcher=true
    
    #########  Notification Configs  #########
    atlas.notification.embedded=false
    atlas.kafka.data=${sys:atlas.home}/data/kafka
    atlas.kafka.zookeeper.connect=node1:2181,node2:2181,node3:2181
    atlas.kafka.bootstrap.servers=node1:9092,node2:9092,node3:9092
    atlas.kafka.zookeeper.session.timeout.ms=4000
    atlas.kafka.zookeeper.connection.timeout.ms=2000
    atlas.kafka.zookeeper.sync.time.ms=20
    atlas.kafka.auto.commit.interval.ms=1000
    atlas.kafka.hook.group.id=atlas
    
    #########  Server Properties  #########
    atlas.rest.address=http://0.0.0.0:21001
    
    #########  Entity Audit Configs  #########
    atlas.audit.hbase.tablename=apache_atlas_entity_audit
    atlas.audit.zookeeper.session.timeout.ms=1000
    atlas.audit.hbase.zookeeper.quorum=node1:2181,node2:2181,node3:2181
    
    ## Server port configuration （这边使用210001，避免端口和 Impala 的冲突）
    atlas.server.http.port=21001
    #atlas.server.https.port=21443
    
    ### 以下为新增配置，按需添加
    ######### Hive Hook Configs #######
    
    atlas.hook.hive.synchronous=false
    
    atlas.hook.hive.numRetries=3
    
    atlas.hook.hive.queueSize=10000
    
    atlas.cluster.name=primary
    
    ######### Sqoop Hook Configs #######
    atlas.hook.sqoop.synchronous=false # whether to run the hook synchronously. false recommended to avoid delays in Sqoop operation completion. Default: false
    atlas.hook.sqoop.numRetries=3      # number of retries for notification failure. Default: 3
    atlas.hook.sqoop.queueSize=10000   # queue size for the threadpool. Default: 10000
```
这时候可以尝试启动下`./bin/atlas_start.py`并看一下 log `tail -f logs/application.log`，我这边遇到了问题：
```bash
    2020-03-31 11:42:57,437 INFO  - [main:] ~ Creating indexes for graph. (GraphBackedS
    earchIndexer:248)
    2020-03-31 11:42:58,605 INFO  - [main:] ~ Created index : vertex_index (GraphBacked
    SearchIndexer:253)
    2020-03-31 11:42:58,692 INFO  - [main:] ~ Created index : edge_index (GraphBackedSe
    archIndexer:259)
    2020-03-31 11:42:58,700 INFO  - [main:] ~ Created index : fulltext_index (GraphBackedSearchIndexer:265)
    2020-03-31 11:42:58,824 ERROR - [main:] ~ GraphBackedSearchIndexer.initialize() failed (GraphBackedSearchIndexer:307)
    org.apache.solr.client.solrj.impl.HttpSolrClient$RemoteSolrException: Error from server at http://BD-Cal-Pro-02:8983/solr: Can not find the specified config set: vertex_index
            at org.apache.solr.client.solrj.impl.HttpSolrClient.executeMethod(HttpSolrClient.java:627)
            at org.apache.solr.client.solrj.impl.HttpSolrClient.request(HttpSolrClient.java:253)
```
不知道为何 atlas 没能在 solr 创建需要的数据，只能手动创建了
```bash
    solrctl instancedir --create atlas conf/solr/
    
    solrctl collection --create vertex_index -s 1 -c atlas -r 1
    
    solrctl collection --create edge_index -s 1 -c atlas -r 1
    
    solrctl collection --create fulltext_index -s 1 -c atlas -r 1
```

至此 atlas 就可以正常启动了。

## Jar 包配置

把配置复制到 hook/hive 下，然后打包进atlas-plugin-classloader-2.0.0.jar

`zip -u atlas-plugin-classloader-2.0.0.jar atlas-application.properties`

有的教程是直接跨目录压缩进去了，但实际执行会出错。

# Hive Hook

这边需要去CM 中 Hive 配置页面修改：

### Hive Auxiliary JARs Directory

> ${ATLAS_HOME}/hook/hive

### Gateway Client Environment Advanced Configuration Snippet (Safety Valve) for [hive-env.sh](http://hive-env.sh/)

> HIVE_AUX_JARS_PATH=${ATLAS_HOME}/hook/hive

### HiveServer2 Advanced Configuration Snippet (Safety Valve) for hive-site.xml
```bash
    <property>
    <name>hive.exec.post.hooks</name>
    <value>org.apache.atlas.hive.hook.HiveHook</value>
    </property>
    
    <property>
    <name>hive.reloadable.aux.jars.path</name>
    <value>${ATLAS_HOME}/hook/hive/</value>
    </property>
    
    <property>
    <name>atlas.cluster.name</name>
    <value>primary</value>
    </property>
```
配置好后重启 Hive，创建一张测试表可以看到Atlas 中会出现该表的记录。

## 导入历史数据

如果需要历史数据，则可以通过 hook-bin下面的 import_hive.sh 导入即可。
```bash
export HIVE_HOME=/opt/cloudera/parcels/CDH/lib/hive/
cp conf/atlas-application.properties /opt/cloudera/parcels/CDH/lib/hive/conf/
./hook-bin/import-hive.sh
```


