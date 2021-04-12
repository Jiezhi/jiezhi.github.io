title: Java中对方法的执行设置超时
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: 'http://qn-jiezhi.nospider.net/pet-0e24cfd3754db9af5e2f7119f1160964.jpg-thumb1'
metaAlignment: center
coverImage: 'http://qn-jiezhi.nospider.net/android12736509,2560,1600.jpg-cover'
coverCaption: Default
coverMeta: out
comments: true
date: 2015-12-03 19:42:46
url:
tags:
- java
- timeout
categories:
- Java
photos:
---
利用FutureTask对一个方法设置执行超时的操作
<!--more-->
```java
package io.github.jiezhi.concurrent;

import java.util.concurrent.*;

/**
 * Created by jiezhi on 12/3/15.
 */
public class TimeoutDemo {
    private static final String TAG = "jiezhi:TimeoutDemo";

    public static void main(String args[]){
        runFunTimeout();
    }

    /**
     * 在固定时间内执行一个函数,超时则退出
     */
    private static void runFunTimeout() {
        FutureTask<Object> task = new FutureTask<>(() -> needTime(10 * 1000));

        ExecutorService executor = Executors.newFixedThreadPool(1);
        executor.execute(task);

        try {
            task.get(5, TimeUnit.SECONDS);
        } catch (InterruptedException | ExecutionException | TimeoutException e) {
            e.printStackTrace();
        }

        executor.shutdown();
    }

    private static int needTime(long t) {
        try {
            Thread.sleep(t);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return 1;
    }


}


```
