---
title: 海豚调度器告警优化
author: Jiezhi.G
email: Jiezhi.G@gmail.com
categories:
  -  BigData
premalink: fix_gravatar
metaAlignment: center
comments: true
date: 2021-04-13 15:42:56
url:
tags:
  - 海豚调度器
  - DolphinScheduler
  - Alert
  - 告警
photos:
---

目前海豚调度器(1.3.6)的告警只支持邮件和短信，对于大部分在夜间执行的任务来说，很难做到在任务出错时及时通知到相应人员。
所以基于当前1.3.6的代码做了一些优化。

<!--more-->

# 背景
目前使用 Oozie 将任务调度信息发到 ActiveMQ，然后使用 Python 来解析对应的任务消息，将任务出错的告警发到钉钉群，如果是非工作时间（比如夜间和周末）则会触发电话告警。此外，还支持值班功能，比如大部分任务出错由值班人员处理即可。

目前已经打算将 Oozie 切换到海豚调度器，但是现有的告警流程还不支持钉钉告警乃至电话告警（已经初步支持企业微信告警，但不符合我们现有的流程）。虽然官方此时已经在重构告警模块（插件化，目前可见会支持钉钉、HTTP等），但在更复杂的告警需求下，还是决定自己来实现。

# 解决方案
## 监控数据库
海豚调度器目前的告警信息都会记录到 `t_ds_alert` 这表里，所以监听该表即可。当然可以通过 binlog 也可以用脚本定时扫描来完成，这里仅提供一个思路。

## 改写代码
我选择了改写代码来实现，在告警模块中增加了将告警信息发送至 Kafka，然后复用之前写的代码来解析 MQ 消息即可。

0. 将代码分支切换到 `1.3.6-release`(这里代码只保证在此分支有效，其他分支自行测试)，告警模块为 `dolphinscheduler-alert`。
1. 新增 Kafka 依赖，该版本由目前使用的 CDH 带的 Kafka 版本决定：
```xml
        <dependency>
            <groupId>org.apache.kafka</groupId>
            <artifactId>kafka-clients</artifactId>
            <version>2.2.1</version>
            <scope>compile</scope>
        </dependency>
```

2. 在 `org/apache/dolphinscheduler/alert/utils/Constants.java` 中新增 Kafka 相关常量名：
```java
    public static final String KAFKA_BOOTSTRAP = "kafka.bootstrap";

    public static final String KAFKA_TOPIC = "kafka.topic";
```

3. 修改 `org/apache/dolphinscheduler/alert/runner/AlertSender.java` 逻辑：
```java
        Properties kafkaProperties = new Properties();
        kafkaProperties.setProperty(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, PropertyUtils.getString(Constants.KAFKA_BOOTSTRAP));
        kafkaProperties.setProperty(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        kafkaProperties.setProperty(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        kafkaProperties.setProperty(ProducerConfig.ACKS_CONFIG, "1");
        producer = new KafkaProducer<>(kafkaProperties);

        ...


        try {
            String alertDataString = JSONUtils.toJsonString(alertInfo);
            logger.info(alertDataString);
            producer.send(new ProducerRecord<>(TOPIC_NAME, alertDataString));
        } catch (Exception e) {
            logger.error(e.getMessage());
            e.printStackTrace();
        }
```

可参考截图

{% asset_img ds_alert_code.png code %}


4. 打包后替换已部署目录下的 `lib/dolphinscheduler-alert-1.3.6.jar` 文件，此外需要把 `kafka-clients` jar 包也复制到 lib 下，可[在此](https://mvnrepository.com/artifact/org.apache.kafka/kafka-clients/2.2.1)下载。

5. 配置 `conf/alert.properties` ，新增：
```java
kafka.bootstrap=192.168.x.x:9092,192.168.x.x:9092,192.168.x.x:9092
kafka.topic=test
```

建议同时修改下 `conf/logback-alert.xml` 文件，过滤下 kafka 一些 log：
```xml
<logger name="org.apache.kafka" level="WARN" />
```

6. 重启告警模块即可
`sh bin/dolphinscheduler-daemon.sh stop alert-server && sh bin/dolphinscheduler-daemon.sh start alert-server`