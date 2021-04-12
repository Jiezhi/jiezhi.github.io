title: CDH 元数据库迁移之 Hue
author: Jiezhi.G
email: Jiezhi.G@gmail.com
categories:
  - BigData
premalink: fix_gravatar
metaAlignment: center
comments: true
date: 2021-03-05 16:48:11
url:
tags:
  - BigData
  - CDH
  - Hue
  - Migration
photos:
---
最近把 CDH 整个集群都迁移了，大部分都没啥太大问题，跟着官方文档操作就行了。

Hue 这个数据库中外键依赖太多，得来点sao操作。

<!--more-->

首先强烈建议不要把 CDH 集群的数据库装在 CDH 节点上，不然遇到集群迁移而不得不迁移数据库时就头疼了。

大部分的服务像 Hive、Sentry、Oozie 这类的，停服务之后直接 mysqldump 到新的数据库后，在配置中修改新的数据库配置，启动即可。

而 Hue 的库不一样，Hue 是基于 Django 开发的，当然不一样的地方在于其库中的表依赖关系太复杂。看图：

{% asset_img hue.png %}

加载 mysqldump 出来的文件会报错, 定位的错误是因为导入desktop_document2这张表出的错，一方面这张表中可能存在中文，另一方面有一个对自身的外键约束，所以导入时基本会挂。

CDH 本身也提供了 Hue 数据库迁移的[操作文档](https://docs.cloudera.com/documentation/enterprise/latest/topics/hue_dbs_migrate.html)，大致是把数据按 json 格式保存下来，然后同步数据库 DDL，操作数据库先去除外键依赖，然后加载数据，最后把依赖加上。

* Tips: 导出的文件 `/tmp/hue_database_dump.json` 比较大时，用 vi 打开比 vim 要快不少。*

{% asset_img cdh-hue.png %}

不过我按照文档操作在加载数据时出错了。此时隐约知道是因为前后两个运维装的数据库一个是MySQL, 一个是MariaDB，再加上配置文件有差异导致了数据同步种种问题（所以数据库迁移也最好用同样的配置，都是泪）。

既然 CDH 提供的操作不行，那就自己一张表一张表备份吧。

写了如下的脚本，理论上可以成功。

```bash
array=( search_facet search_result search_sorting auth_user search_collection useradmin_huepermission auth_group useradmin_grouppermission django_content_type auth_permission django_admin_log desktop_document jobsub_oozieaction jobsub_ooziedesign jobsub_ooziejavaaction jobsub_ooziemapreduceaction jobsub_ooziestreamingaction oozie_node oozie_link oozie_decision oozie_decisionend oozie_distcp oozie_email oozie_fork oozie_fs oozie_generic oozie_hive oozie_java oozie_join oozie_kill oozie_mapreduce oozie_pig oozie_shell oozie_sqoop oozie_ssh oozie_streaming oozie_end oozie_start beeswax_session desktop_userpreferences django_openid_auth_useropenid jobsub_jobdesign useradmin_userprofile desktop_defaultconfiguration desktop_documenttag beeswax_savedquery oozie_job desktop_document2 pig_document pig_pigscript jobsub_jobhistory useradmin_ldapgroup auth_group_permissions auth_user_groups defaultconfiguration_groups desktop_documentpermission auth_user_user_permissions desktop_document_tags beeswax_queryhistory oozie_history desktop_document2permission desktop_document2_dependencies documentpermission_groups documentpermission_users documentpermission2_groups oozie_bundle documentpermission2_users oozie_workflow oozie_subworkflow oozie_coordinator oozie_bundledcoordinator oozie_dataset oozie_datainput oozie_dataoutput beeswax_metainstall desktop_settings django_session django_site jobsub_checkforsetup django_migrations django_openid_auth_nonce django_openid_auth_association axes_accesslog axes_accessattempt )
for i in "${array[@]}"
do
    echo Processing $i
    mysqldump --default-character-set=utf8 --hex-blob -u hue -phuepw hue $i > $i.sql && ssh mysql-server 'mysql -uhue -phuepw hue' < $i.sql

done
```


---

表导入的顺序，是按外键依赖关系决定的。算是解决了外键的问题，实际操作还是遇到了问题，desktop_document2对自身的外键依赖会导致导入数据出现问题。

所以我把上面的脚本拆分成 desktop_document2 前后两个脚本。即先同步如下的表：
```bash
array=( search_facet search_result search_sorting auth_user search_collection useradmin_huepermission auth_group useradmin_grouppermission django_content_type auth_permission django_admin_log desktop_document jobsub_oozieaction jobsub_ooziedesign jobsub_ooziejavaaction jobsub_ooziemapreduceaction jobsub_ooziestreamingaction oozie_node oozie_link oozie_decision oozie_decisionend oozie_distcp oozie_email oozie_fork oozie_fs oozie_generic oozie_hive oozie_java oozie_join oozie_kill oozie_mapreduce oozie_pig oozie_shell oozie_sqoop oozie_ssh oozie_streaming oozie_end oozie_start beeswax_session desktop_userpreferences django_openid_auth_useropenid jobsub_jobdesign useradmin_userprofile desktop_defaultconfiguration desktop_documenttag beeswax_savedquery oozie_job )
for i in "${array[@]}"
do
    echo Processing $i
    mysqldump --default-character-set=utf8 --hex-blob -u hue -phuepw hue $i > $i.sql && ssh mysql-server 'mysql -uhue -phuepw hue' < $i.sql

done
```

然后同步desktop_document2这张表，在desktop_document2.sql 中先把 DDL 中最后对自身的外键依赖删除，然后导入数据，最后加上对应的外键依赖：

```sql
ALTER TABLE desktop_document2 ADD CONSTRAINT `desktop_document2_parent_directory_id_428ead9c_fk_desktop_d` FOREIGN KEY (`parent_directory_id`) REFERENCES `desktop_document2` (`id`);
```

最后同步剩下的表：

```bash
array=( pig_document pig_pigscript jobsub_jobhistory useradmin_ldapgroup auth_group_permissions auth_user_groups defaultconfiguration_groups desktop_documentpermission auth_user_user_permissions desktop_document_tags beeswax_queryhistory oozie_history desktop_document2permission desktop_document2_dependencies documentpermission_groups documentpermission_users documentpermission2_groups oozie_bundle documentpermission2_users oozie_workflow oozie_subworkflow oozie_coordinator oozie_bundledcoordinator oozie_dataset oozie_datainput oozie_dataoutput beeswax_metainstall desktop_settings django_session django_site jobsub_checkforsetup django_migrations django_openid_auth_nonce django_openid_auth_association axes_accesslog axes_accessattempt )
for i in "${array[@]}"
do
    echo Processing $i
    mysqldump --default-character-set=utf8 --hex-blob -u hue -phuepw hue $i > $i.sql && ssh mysql-server 'mysql -uhue -phuepw hue' < $i.sql

done
```

启动 Hue，貌似除了中文编码出现了点问题外，其他一切正常。

对于含中文的文档乱码，点开后中文显示正常，再点击保存就好了。所以懒得再去定位数据库编码的问题了。