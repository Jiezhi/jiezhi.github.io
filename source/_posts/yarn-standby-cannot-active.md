---
title: HA模式下，YARN RM无法进入活动状态
date: 2022-02-14 13:53:34
tags: 
  - CDH
  - YARN
  - HA
categories:
  - BigData

---
凌晨 YARN 异常，HA 模式下两个 RM（ResourceManager）都处于备用（standby）状态，重启其中任何一个都无法使另外的活动（active）状态。

<!--more-->

定位日志看到：
```java
2022-02-14 06:39:05,930 INFO org.apache.hadoop.conf.Configuration: dynamic-resources.xml not found
2022-02-14 06:39:05,930 INFO org.apache.hadoop.yarn.server.resourcemanager.AMSProcessingChain: Initializing AMS Processing chain. Root Processor=[org.apache.hadoop.yarn.server.resourcemanager.DefaultAMSProcessor].
2022-02-14 06:39:05,930 WARN org.apache.hadoop.yarn.server.resourcemanager.RMAuditLogger: USER=yarn     OPERATION=transitionToActive    TARGET=RM       RESULT=FAILURE  DESCRIPTION=Exception transitioning to active   PERMISSIONS=
2022-02-14 06:39:05,930 WARN org.apache.hadoop.ha.ActiveStandbyElector: Exception handling the winning of election
org.apache.hadoop.ha.ServiceFailedException: RM could not transition to Active
        at org.apache.hadoop.yarn.server.resourcemanager.ActiveStandbyElectorBasedElectorService.becomeActive(ActiveStandbyElectorBasedElectorService.java:146)
        at org.apache.hadoop.ha.ActiveStandbyElector.becomeActive(ActiveStandbyElector.java:894)
        at org.apache.hadoop.ha.ActiveStandbyElector.processResult(ActiveStandbyElector.java:473)
        at org.apache.zookeeper.ClientCnxn$EventThread.processEvent(ClientCnxn.java:651)
        at org.apache.zookeeper.ClientCnxn$EventThread.run(ClientCnxn.java:526)
Caused by: org.apache.hadoop.ha.ServiceFailedException: Error when transitioning to Active mode
        at org.apache.hadoop.yarn.server.resourcemanager.AdminService.transitionToActive(AdminService.java:325)
        at org.apache.hadoop.yarn.server.resourcemanager.ActiveStandbyElectorBasedElectorService.becomeActive(ActiveStandbyElectorBasedElectorService.java:144)
        ... 4 more
Caused by: java.lang.NullPointerException
        at org.apache.hadoop.yarn.server.resourcemanager.scheduler.fair.FairScheduler.addApplicationAttempt(FairScheduler.java:526)
        at org.apache.hadoop.yarn.server.resourcemanager.scheduler.fair.FairScheduler.handle(FairScheduler.java:1257)
        at org.apache.hadoop.yarn.server.resourcemanager.scheduler.fair.FairScheduler.handle(FairScheduler.java:132)
        at org.apache.hadoop.yarn.server.resourcemanager.rmapp.attempt.RMAppAttemptImpl$AttemptRecoveredTransition.transition(RMAppAttemptImpl.java:1266)
        at org.apache.hadoop.yarn.server.resourcemanager.rmapp.RMAppImpl$RMAppRecoveredTransition.transition(RMAppImpl.java:1142)
```
看起来是空指针引起的异常，此时因为优先修复 YARN，保障任务顺利执行。

解决方案：

1. 执行 `yarn resourcemanager -format-state-store` 解决
2. 启动 RM

根据 [YARN 文档](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/YarnCommands.html) 描述, `-format-state-store`：

> Formats the RMStateStore. This will clear the RMStateStore and is useful if past applications are no longer needed. This should be run only when the ResourceManager is not running.