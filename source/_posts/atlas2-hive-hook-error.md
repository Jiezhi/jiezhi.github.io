title: Atlas升级2.1.0出错修复（CDH 6.3.1）
author: Jiezhi.G
email: Jiezhi.G@gmail.com
categories:
  - BigData
premalink: fix_gravatar
metaAlignment: center
comments: true
date: 2020-08-18 11:40:40
url:
tags:
  - Atlas
  - CDH
  - BigData
photos:
---
『旧山松竹老，阻归程。』—— 岳飞 《小重山》
<!--more-->

升级后发现 Hive 调用 Atlas Hook 报如下的错误：
```java
[HiveServer2-Background-Pool: Thread-52]: HiveHook.run(): failed to process operation CREATETABLE
java.lang.NoSuchMethodError: org.apache.hadoop.hive.metastore.api.Database.getCatalogName()Ljava/lang/String;
	at org.apache.atlas.hive.bridge.HiveMetaStoreBridge.getDatabaseName(HiveMetaStoreBridge.java:577) ~[hive-bridge-2.1.0.jar:2.1.0]
	at org.apache.atlas.hive.hook.AtlasHiveHookContext.getQualifiedName(AtlasHiveHookContext.java:201) ~[hive-bridge-2.1.0.jar:2.1.0]
	at org.apache.atlas.hive.hook.AtlasHiveHookContext.init(AtlasHiveHookContext.java:293) ~[hive-bridge-2.1.0.jar:2.1.0]
	at org.apache.atlas.hive.hook.AtlasHiveHookContext.<init>(AtlasHiveHookContext.java:83) ~[hive-bridge-2.1.0.jar:2.1.0]
	at org.apache.atlas.hive.hook.AtlasHiveHookContext.<init>(AtlasHiveHookContext.java:64) ~[hive-bridge-2.1.0.jar:2.1.0]
	at org.apache.atlas.hive.hook.HiveHook.run(HiveHook.java:175) [hive-bridge-2.1.0.jar:2.1.0]
	at org.apache.atlas.hive.hook.HiveHook.run(HiveHook.java:52) [hive-bridge-shim-2.1.0.jar:2.1.0]
	at org.apache.hadoop.hive.ql.Driver.execute(Driver.java:1960) [hive-exec-2.1.1-cdh6.3.1.jar:2.1.1-cdh6.3.1]
	at org.apache.hadoop.hive.ql.Driver.runInternal(Driver.java:1563) [hive-exec-2.1.1-cdh6.3.1.jar:2.1.1-cdh6.3.1]
	at org.apache.hadoop.hive.ql.Driver.run(Driver.java:1339) [hive-exec-2.1.1-cdh6.3.1.jar:2.1.1-cdh6.3.1]
	at org.apache.hadoop.hive.ql.Driver.run(Driver.java:1334) [hive-exec-2.1.1-cdh6.3.1.jar:2.1.1-cdh6.3.1]
	at org.apache.hive.service.cli.operation.SQLOperation.runQuery(SQLOperation.java:256) [hive-service-2.1.1-cdh6.3.1.jar:2.1.1-cdh6.3.1]
	at org.apache.hive.service.cli.operation.SQLOperation.access$600(SQLOperation.java:92) [hive-service-2.1.1-cdh6.3.1.jar:2.1.1-cdh6.3.1]
	at org.apache.hive.service.cli.operation.SQLOperation$BackgroundWork$1.run(SQLOperation.java:345) [hive-service-2.1.1-cdh6.3.1.jar:2.1.1-cdh6.3.1]
	at java.security.AccessController.doPrivileged(Native Method) ~[?:1.8.0_181]
	at javax.security.auth.Subject.doAs(Subject.java:422) [?:1.8.0_181]
	at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1875) [hadoop-common-3.0.0-cdh6.3.1.jar:?]
	at org.apache.hive.service.cli.operation.SQLOperation$BackgroundWork.run(SQLOperation.java:357) [hive-service-2.1.1-cdh6.3.1.jar:2.1.1-cdh6.3.1]
	at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:511) [?:1.8.0_181]
	at java.util.concurrent.FutureTask.run(FutureTask.java:266) [?:1.8.0_181]
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149) [?:1.8.0_181]
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624) [?:1.8.0_181]
	at java.lang.Thread.run(Thread.java:748) [?:1.8.0_181]

```

排查后发现 Atlas 代码中用的 Hive 版本为3.1.0，而目前 CDH 6.3.1的 Hive 是 2.1.1。hive-bridge 模块中调用了2.1.1版本中不存在的方法报错导致。

所以修改`addons/hive-bridge/src/main/java/org/apache/atlas/hive/bridge/HiveMetaStoreBridge.java`中的`getDatabaseName()`方法即可。

修改前：
```java
    public static String getDatabaseName(Database hiveDB) {
        String dbName      = hiveDB.getName().toLowerCase();
        String catalogName = hiveDB.getCatalogName() != null ? hiveDB.getCatalogName().toLowerCase() : null;

        if (StringUtils.isNotEmpty(catalogName) && !StringUtils.equals(catalogName, DEFAULT_METASTORE_CATALOG)) {
            dbName = catalogName + SEP + dbName;
        }

        return dbName;
    }
```

修改后：
```java
    public static String getDatabaseName(Database hiveDB) {
        String dbName      = hiveDB.getName().toLowerCase();
        /*
        String catalogName = hiveDB.getCatalogName() != null ? hiveDB.getCatalogName().toLowerCase() : null;

        if (StringUtils.isNotEmpty(catalogName) && !StringUtils.equals(catalogName, DEFAULT_METASTORE_CATALOG)) {
            dbName = catalogName + SEP + dbName;
        }
        */

        return dbName;
    }
```

然后重新进`addons/hive-bridge`目录下打包出 hive-bridge-2.1.0.jar。

最后用这个新的包替换`atlas-hive-plugin-impl`目录下的文件，重启 HiveServer2 即可。

