title: CDH 中 Flume 配置及拦截器的实现
author: Jiezhi.G
email: Jiezhi.G@gmail.com
categories:
  - BigData
premalink: fix_gravatar
metaAlignment: center
comments: true
date: 2020-06-03 17:05:34
url:
tags:
    - BigData
    - Flume
photos:
---
记录配置 Flume 以及自定义 Flume 拦截器（Interceptor）的过程。
<!--more-->
# Flume 配置
## 堆内存设置

 CDH 中添加 flume 后好像默认给了50M 堆内存，测试用用可以，实际使用还是改成1G 或以上吧。

{% asset_img flume-heap.png %}

## Hive 相关配置

如果需要把数据 sink 到 hive 的话，flume 可能会因为找不到 Hive 相关依赖报错：

```java
Failed to start agent because dependencies were not found in classpath. Error follows.
java.lang.NoClassDefFoundError: org/apache/hive/hcatalog/streaming/RecordWriter
	at org.apache.flume.sink.hive.HiveSink.createSerializer(HiveSink.java:223)
	at org.apache.flume.sink.hive.HiveSink.configure(HiveSink.java:203)
	at org.apache.flume.conf.Configurables.configure(Configurables.java:41)
	at org.apache.flume.node.AbstractConfigurationProvider.loadSinks(AbstractConfigurationProvider.java:453)
	at org.apache.flume.node.AbstractConfigurationProvider.getConfiguration(AbstractConfigurationProvider.java:106)
	at org.apache.flume.node.PollingPropertiesFileConfigurationProvider$FileWatcherRunnable.run(PollingPropertiesFileConfigurationProvider.java:145)
	at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:511)
	at java.util.concurrent.FutureTask.runAndReset(FutureTask.java:308)
	at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.access$301(ScheduledThreadPoolExecutor.java:180)
	at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(ScheduledThreadPoolExecutor.java:294)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
	at java.lang.Thread.run(Thread.java:748)
Caused by: java.lang.ClassNotFoundException: org.apache.hive.hcatalog.streaming.RecordWriter
	at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
	at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:349)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
	... 13 more
```

那么需要配置下 Flume env 的配置，HIVE_HOME 和 HCAT_HOME 具体位置根据情况填写
{% asset_img flume-env.png %}


---

# Flume 从 Kafka 读取数据解析至 Hive 中

## Flume 配置文件

```java
# For Data Flow
tier1.sources=source1
tier1.channels=channel1
tier1.sinks=sink1

# Source
tier1.sources.source1.interceptors=i1
tier1.sources.source1.interceptors.i1.type=io.jiezhi.dw.flume.interceptor.EventsInterceptor$Builder

tier1.sources.source1.type=org.apache.flume.source.kafka.KafkaSource
tier1.sources.source1.channels = channel1
tier1.sources.source1.batchSize = 15000
tier1.sources.source1.batchDurationMillis = 2000
tier1.sources.source1.kafka.bootstrap.servers = node1:9092,node2:9092,node3:9092
tier1.sources.source1.kafka.topics = test_topic
tier1.sources.source1.kafka.consumer.group.id = dw

# Channel
tier1.channels.channel1.type=memory
tier1.channels.channel1.capacity=15000
tier1.channels.channel1.transactionCapacity=15000

# Sink
tier1.sinks.sink1.channel=channel1
tier1.sinks.sink1.type=hive
tier1.sinks.sink1.hive.metastore=thrift://node1:9083
tier1.sinks.sink1.hive.batchSize=15000
tier1.sinks.sink1.hive.database=test
tier1.sinks.sink1.hive.table=test_tbl
tier1.sinks.sink1.serializer=JSON
```

这里面用了自定义拦截器（interceptor）因为从 Kafka 中获取的数据不能直接插入到 Hive 表中，所以需要从中转换下格式。

## Hive 建表

必须是`事务表`且需要`分桶`：

```sql
CREATE TABLE test.test_tbl (
    `id`           BIGINT,
    `time`                BIGINT,
    `event`               STRING,
    `type`                STRING,
    ...
    `platform`            STRING,
    `params`              STRING
)
    CLUSTERED BY (`id`) INTO 11 BUCKETS 
		STORED AS ORC TBLPROPERTIES ('transactional' = 'true');
```

## 读取Hive数据

报错：

`This command is not allowed on an ACID table test.test_tbl with a non-ACID transaction manager.Failed command: SELECT * from test.test_tbl LIMIT 10.`

要设置一下后查询：

`SET hive.txn.manager=org.apache.hadoop.hive.ql.lockmgr.DbTxnManager;`

可参见[这里](https://stackoverflow.com/questions/42669171/semanticexception-error-10265-while-running-simple-hive-select-query-on-a-tran)。

## 实现Flume拦截器

如果从 Source 获得的数据不能直接使用，那么一种方式是先落到 HDFS，然后用 Spark 等程序处理后再落到 Hive 中，另一种方式是实现 Flume 拦截器。这边采用后一种方式。

其实很简单：

### 配置依赖：

```xml
<dependency>
    <groupId>org.apache.flume</groupId>
    <artifactId>flume-ng-core</artifactId>
    <version>1.9.0</version>
</dependency>
```

### 实现Interceptor的接口

重点是在intercept()方法中实现对应的逻辑

```java
public class EventsInterceptor implements Interceptor {

    private ObjectMapper mapper = new ObjectMapper();

    public static class Builder implements Interceptor.Builder {

        public Interceptor build() {
            return new UserEventsInterceptor();
        }

        public void configure(Context context) {

        }
    }

    public void initialize() {

    }
    
    public byte[] convertUserEvent(byte[] body) {
        // Do something
    }

    public Event intercept(Event event) {
        event.setBody(convertJson(event.getBody()));
        return event;
    }

    public List<Event> intercept(List<Event> events) {
        for (Event event : events) {
            intercept(event);
        }
        return events;
    }

    public void close() {

    }
}

```

然后 `mvn clean package` 后把jar 包放到 flume agent 的节点上，当然放的位置根据`ps aux | grep flume-ng`来决定，比如我这边就是`/data/opt/cloudera/parcels/CDH-6.3.1-1.cdh6.3.1.p0.1470567/lib/flume-ng/lib/`

然后重启 Flume 即可。


