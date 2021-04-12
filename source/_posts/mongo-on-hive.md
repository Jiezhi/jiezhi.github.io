title: mongo-on-hive
author: Jiezhi.G
email: Jiezhi.G@gmail.com
categories:
  - default
premalink: fix_gravatar
metaAlignment: center
comments: true
date: 2020-08-28 17:00:18
url:
tags:
photos:
---



<!--more-->





```java
18:59:20,376 ERROR [main] org.apache.hadoop.mapred.YarnChild: Error running child : java.lang.NoSuchMethodError: org.apache.hadoop.hive.ql.exec.Utilities.deserializeExpression(Ljava/lang/String;)Lorg/apache/hadoop/hive/ql/plan/ExprNodeGenericFuncDesc;
	at com.mongodb.hadoop.hive.input.HiveMongoInputFormat.getFilter(HiveMongoInputFormat.java:134)
	at com.mongodb.hadoop.hive.input.HiveMongoInputFormat.getRecordReader(HiveMongoInputFormat.java:103)
	at org.apache.hadoop.hive.ql.io.HiveInputFormat.getRecordReader(HiveInputFormat.java:295)
	at org.apache.hadoop.hive.ql.io.CombineHiveInputFormat.getRecordReader(CombineHiveInputFormat.java:685)
	at org.apache.hadoop.mapred.MapTask$TrackedRecordReader.<init>(MapTask.java:175)
	at org.apache.hadoop.mapred.MapTask.runOldMapper(MapTask.java:444)
	at org.apache.hadoop.mapred.MapTask.run(MapTask.java:349)
	at org.apache.hadoop.mapred.YarnChild$2.run(YarnChild.java:174)
	at java.security.AccessController.doPrivileged(Native Method)
	at javax.security.auth.Subject.doAs(Subject.java:422)
	at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1875)
	at org.apache.hadoop.mapred.YarnChild.main(YarnChild.java:168)

18:59:20,478 INFO [main] org.apache.hadoop.metrics2.impl.MetricsSystemImpl: Stopping MapTask metrics system...
18:59:20,478 INFO [main] org.apache.hadoop.metrics2.impl.MetricsSystemImpl: MapTask metrics system stopped.
18:59:20,478 INFO [main] org.apache.hadoop.metrics2.impl.MetricsSystemImpl: MapTask metrics system shutdown complete.
```



https://github.com/mongodb/mongo-hadoop/pull/147/files