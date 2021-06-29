---
title: Ranger Admin 配置
date: 2021-06-29 16:58:29
tags:
  - Ranger
  - CDH
categories:
  - BigData
---
记录下配置历程。
<!--more-->

详细的配置步骤参见[官网](https://cwiki.apache.org/confluence/display/RANGER/Apache+Ranger+0.5.0+Installation)。

# 数据库配置
生产环境需要对权限进行进一步的控制。
```sql
CREATE USER 'rangeradmin'@'localhost' IDENTIFIED BY 'xxxxx';
GRANT ALL PRIVILEGES ON *.* TO 'rangeradmin'@'localhost';
CREATE USER 'rangeradmin'@'%' IDENTIFIED BY 'xxxxxx';
GRANT ALL PRIVILEGES ON *.* TO 'rangeradmin'@'%';
GRANT ALL PRIVILEGES ON *.* TO 'rangeradmin'@'localhost' WITH GRANT OPTION;
GRANT ALL PRIVILEGES ON *.* TO 'rangeradmin'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

# 配置文件
```bash
[test@test ranger-2.1.0-admin]$ diff install.properties install.properties.bak
53,54c53,54
< db_root_password=xxxxxx
< db_host=172.x.x.170
---
> db_root_password=
> db_host=localhost
70c70
< db_password=xxxxxx
---
> db_password=
74,77c74,77
< rangerAdmin_password=xxxx
< rangerTagsync_password=xxxxx
< rangerUsersync_password=xxxxxx
< keyadmin_password=xxxxx
---
> rangerAdmin_password=
> rangerTagsync_password=
> rangerUsersync_password=
> keyadmin_password=
95,98c95,98
< audit_solr_urls=http://solr.example.com:8983/solr/ranger_audits
< audit_solr_user=None
< audit_solr_password=None
< audit_solr_zookeepers=pro06.vpc.example.com:2181/solr,pro07.vpc.example.com:2181/solr,pro08.vpc.example.com:2181/solr,pro09.vpc.example.com:2181/solr,pro10.vpc.example.com:2181/solr
---
> audit_solr_urls=
> audit_solr_user=
> audit_solr_password=
> audit_solr_zookeepers=
115c115
< policymgr_external_url=http://0.0.0.0:6080
---
> policymgr_external_url=http://localhost:6080
```

# 启动
```bash
export JAVA_HOME=/usr/java/jdk1.8.0_181-cloudera
./setup.sh
```

## 错误
```bash
SQLException : SQL state: HY000 java.sql.SQLException: This function has none of DETERMINISTIC, NO SQL, or READS SQL DATA in its declaration and binary logging is enabled (you *might* want to use the less safe log_bin_trust_function_creators variable) ErrorCode: 1418
2021-06-29 17:26:51,715  [E] ranger_core_db_mysql.sql file import failed!
2021-06-29 17:26:51,716  [I] Unable to create DB schema, Please drop the database and try again
2021-06-29 17:26:51,716  [JISQL] /usr/java/jdk1.8.0_181-cloudera/bin/java  -cp /usr/share/java/mysql-connector-java.jar:/opt/ranger/ranger-2.1.0-admin/jisql/lib/* org.apache.util.sql.Jisql -driver mysqlconj -cstring jdbc:mysql://172.x.x.x/ranger -u 'rangeradmin' -p '********' -noheader -trim -c \;  -query "delete from x_db_version_h where version = 'CORE_DB_SCHEMA' and active = 'N' and updated_by='pro10.vpc.example.com';"
Tue Jun 29 17:26:51 CST 2021 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
2021-06-29 17:26:52,364  [E] CORE_DB_SCHEMA import failed!
```
解决：
根据[这里](https://www.jamediasolutions.com/blog/deterministic-no-sql-or-reads-sql-data-in-its-declaration.html)的解决方法，在文件`/opt/ranger/ranger-2.1.0-admin/db/mysql/optimized/current/ranger_core_db_mysql.sql`中加入`SET GLOBAL log_bin_trust_function_creators = 1;`即可解决。

当然，重新执行 setup.sh 可能会遇到依赖问题，所以最好直接到数据库里`drop database ranger;`

顺利的话就配置好了，执行`ranger-admin start`进行启动。
执行`ranger-admin stop` 或 `ranger-admin restart` 进行停止或重启。

启动好后，用 `admin` 用户访问：http://127.0.0.1:6080 ，其中密码在配置文件的`rangerAdmin_password`中配置。