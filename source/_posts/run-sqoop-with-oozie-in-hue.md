title: CDH中sqoop无法在 hue 中正确执行的问题
author: Jiezhi.G
email: Jiezhi.G@gmail.com
categories:
  - BigData
premalink: fix_gravatar
metaAlignment: center
comments: true
date: 2020-01-17 17:38:36
url:
tags:
  - CDH
  - Sqoop
  - Hue
  - Ooozie
photos:
---

JDBC的配置给你带来多少的麻烦？

<!--more-->

安装好 CDH 后，添加完 sqoop 1服务后，去 hue 中执行直接报错。

```Java
No child hadoop job is executed.
java.lang.reflect.InvocationTargetException
  ...
	at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1875)
	at org.apache.oozie.action.hadoop.LauncherAM.main(LauncherAM.java:141)
Caused by: java.lang.SecurityException: Intercepted System.exit(1)
	at org.apache.oozie.action.hadoop.security.LauncherSecurityManager.checkExit(LauncherSecurityManager.java:57)
	at java.lang.Runtime.exit(Runtime.java:107)
	at java.lang.System.exit(System.java:971)
	at org.apache.sqoop.Sqoop.main(Sqoop.java:252)
	at org.apache.oozie.action.hadoop.SqoopMain.runSqoopJob(SqoopMain.java:214)
	at org.apache.oozie.action.hadoop.SqoopMain.run(SqoopMain.java:199)
	at org.apache.oozie.action.hadoop.LauncherMain.run(LauncherMain.java:104)
	at org.apache.oozie.action.hadoop.SqoopMain.main(SqoopMain.java:51)
	... 16 more
Intercepting System.exit(1)
Failing Oozie Launcher, Main Class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]
```

围绕这个问题找了很久的都无法找到对应的问题，之前都是直接去 YARN 里看的 Log。这两天去看了 stderr 里的错误才发现是 JDBC 驱动造成的问题（吐槽下某骨文公司）很多程序本可以开箱即用或者一键安装的，结果老是因为 JDBC 驱动要去手动下载而浪费很多时间。
再看下真正的错误：
```java
java.lang.RuntimeException: Could not load db driver class: com.mysql.jdbc.Driver
	at org.apache.sqoop.manager.SqlManager.makeConnection(SqlManager.java:874)
	at org.apache.sqoop.manager.GenericJdbcManager.getConnection(GenericJdbcManager.java:59)
	at org.apache.sqoop.manager.CatalogQueryManager.listTables(CatalogQueryManager.java:102)
	at org.apache.sqoop.tool.ListTablesTool.run(ListTablesTool.java:49)
	at org.apache.sqoop.Sqoop.run(Sqoop.java:146)
```

然后以为是 sqoop JDBC 驱动没安装好，不过在节点上却可以直接用 sqoop 命令来导入导出数据。那说明 sqoop 的驱动没问题呀，我们知道 sqoop 最终是落在 oozie 上执行的，那么是不是有可能 oozie 找不到对应的驱动造成的呢。

我们去 oozie 所在的节点上执行命令：

```shell
$ oozie admin -shareliblist
[Available ShareLib]
hive
distcp
git
mapreduce-streaming
spark
oozie
hcatalog
hive2
sqoop
pig
```

看一下 sqoop 相关的 jar 包：
```shell
$ oozie admin -shareliblist sqoop
[Available ShareLib]
sqoop
	hdfs://yournodename:8020/user/oozie/share/lib/lib_20200108192149/sqoop/HikariCP-2.6.1.jar
	hdfs://yournodename:8020/user/oozie/share/lib/lib_20200108192149/sqoop/HikariCP-java7-2.4.12.jar
	hdfs://yournodename:8020/user/oozie/share/lib/lib_20200108192149/sqoop/ST4-4.0.4.jar
	hdfs://yournodename:8020/user/oozie/share/lib/lib_20200108192149/sqoop/aggdesigner-algorithm-6.0.jar
	hdfs://yournodename:8020/user/oozie/share/lib/lib_20200108192149/sqoop/ant-1.9.1.jar
	hdfs://yournodename:8020/user/oozie/share/lib/lib_20200108192149/sqoop/ant-launcher-1.9.1.jar
	hdfs://yournodename:8020/user/oozie/share/lib/lib_20200108192149/sqoop/antlr-2.7.7.jar
	hdfs://yournodename:8020/user/oozie/share/lib/lib_20200108192149/sqoop/antlr-runtime-3.4.jar
	hdfs://yournodename:8020/user/oozie/share/lib/lib_20200108192149/sqoop/aopalliance-1.0.jar
	hdfs://yournodename:8020/user/oozie/share/lib/lib_20200108192149/sqoop/aopalliance-repackaged-2.5.0-b32.jar
  ...
 ```
 grep 一下，发现并没有 JDBC 驱动，那么需要将 sqoop 目录下（一般为/var/lib/sqoop/）的 jdbc 驱动放到 oozie 目录下（具体在 HDFS 上的目录看上面的输出结果就知道了）。如果 sqoop 的 jdbc 还没配好，直接按照[CDH 文档](https://docs.cloudera.com/documentation/enterprise/latest/topics/cm_mc_sqoop1_client.html#topic_13_7)安装即可。

最后执行下更新即可：
```shell
$ oozie admin -sharelibupdate
[ShareLib update status]
	sharelibDirOld = hdfs://yournodename:8020/user/oozie/share/lib/lib_20200108192149
	host = http://yournodename:11000/oozie
	sharelibDirNew = hdfs://yournodename:8020/user/oozie/share/lib/lib_20200108192149
	status = Successful
```

