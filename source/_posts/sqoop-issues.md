title: sqoop 常见问题
author: Jiezhi.G
email: Jiezhi.G@gmail.com
categories:
  - BigData
premalink: fix_gravatar
metaAlignment: center
comments: true
date: 2019-12-12 10:48:05
url:
tags:
  - Sqoop
  - HDFS
  - Hive
  - MySQ
photos:
---
记录 Sqoop 使用过程中常见问题及解决方案。
<!--more-->


## 1.表名为数据库的保留字段
```bash
INFO manager.SqlManager: Executing SQL statement: SELECT t.* FROM order AS t WHERE 1=0
ERROR manager.SqlManager: Error executing statement: com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'order AS t WHERE 1=0' at line 1
com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'order AS t WHERE 1=0' at line 1
```
Sqoop 在执行任务前，会执行类似的语句来判断源表是否有问题：
`SELECT t.* FROM TABLE_NAME AS t WHERE 1=0`
然而这里报的错表示这句语句有错误，无法执行。仔细一看原来业务库那边的表名为 **order**，显然这个订单表名使用了 MySQL 中的保留关键字了。在 MySQL 中，使用 *``* 将含关键字的名称引起来即可正常执行，如：
```sql
SELECT t.* FROM `order` AS t WHERE 1=0。
```

如果在 Sqoop 命令中直接使用 *`order`* 却无法正常执行，**order** 会被 shell 当成独立的命令来执行，也会直接报错。（经过测试使用单引号`''`、双引号`""`及转义字符都无法执行 Sqoop 任务。)

Updated: 可以试下方式
```
--table \`order\`
```

但 sqoop 在找主键时似乎会出错，所以最好通过`--split-by`指定主键）

那可以考虑
1.用 `--query`  来解决

可参考[Free-form Query Imports](http://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html#_free_form_query_imports)

2.将 sqoop 语句放到文件中，然后使用`sqoop --options-file`来执行对应的文件
可参考[Using Options Files to Pass Arguments](http://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html#_using_options_files_to_pass_arguments)


## 2.MySQL 中 Time 类型错误
在数据库中，比如门店营业时间有的会填写`24:00:00`，但是在 sqoop 导入过程中会出现如下错误：

```bash
Error: java.io.IOException: SQLException in nextKeyValue
        at org.apache.sqoop.mapreduce.db.DBRecordReader.nextKeyValue(DBRecordReader.java:275)
        at org.apache.hadoop.mapred.MapTask$NewTrackingRecordReader.nextKeyValue(MapTask.java:568)
        at org.apache.hadoop.mapreduce.task.MapContextImpl.nextKeyValue(MapContextImpl.java:80)
        at org.apache.hadoop.mapreduce.lib.map.WrappedMapper$Context.nextKeyValue(WrappedMapper.java:91)
        at org.apache.hadoop.mapreduce.Mapper.run(Mapper.java:145)
        at org.apache.sqoop.mapreduce.AutoProgressMapper.run(AutoProgressMapper.java:64)
        at org.apache.hadoop.mapred.MapTask.runNewMapper(MapTask.java:799)
        at org.apache.hadoop.mapred.MapTask.run(MapTask.java:347)
        at org.apache.hadoop.mapred.YarnChild$2.run(YarnChild.java:174)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:422)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1875)
        at org.apache.hadoop.mapred.YarnChild.main(YarnChild.java:168)
Caused by: java.sql.SQLException: java.sql.SQLException: Illegal hour value '24' for java.sql.Time type in value '24:00:00.
        at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:1056)
        at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:957)
        at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:927)
        at com.mysql.jdbc.ResultSet.getTimeFromString(ResultSet.java:6096)
        at com.mysql.jdbc.ResultSet.getStringInternal(ResultSet.java:5819)
        at com.mysql.jdbc.ResultSet.getString(ResultSet.java:5645)
        at org.apache.sqoop.lib.JdbcWritableBridge.readString(JdbcWritableBridge.java:68)
        at partner_stores.readFields(partner_stores.java:505)
        at org.apache.sqoop.mapreduce.db.DBRecordReader.nextKeyValue(DBRecordReader.java:242)
        ... 12 more
```
这是因为 sqoop 导入 MySQL 用的是 JDBC，然而 JDBC 的驱动中对 Time 类型最大支持到"23:59:59"，所以当 Time 类型数据出现超过"23:59:59"数据就会报这个错。

尝试过使用`--map-column-java`，但依然会报错
```bash
--map-column-java
endTime=String
```

解决办法：
1.让业务人员修改数据库，要么把类型改成 Varchar 类型，要么把数据刷成最大"23:59:59"
2.自己动手, 使用`--query`，在 select 操作中进行类型转换：
```sql
SELECT id, CAST(startTime AS CHAR) startTime, CAST(endTime AS CHAR) endTime
FROM shop.stores
```
