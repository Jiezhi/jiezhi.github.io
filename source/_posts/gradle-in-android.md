title: Android中app.build配置
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: >-
  http://qn-jiezhi.nospider.net/pet-0e24cfd3754db9af5e2f7119f1160964.jpg-thumb1
metaAlignment: center
comments: true
date: 2017-10-18 15:40:29
url:
tags:
categories:
photos:
---
备份一下项目中的app.build文件
<!--more-->
```java
    release {
           minifyEnabled true
           shrinkResources true
           proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
           resValue "string", "app_name", "AppName"
           resValue "string", "account_type", "io.jiezhi.app.type"
       }
       preRelease {
           debuggable true
           jniDebuggable true
           minifyEnabled true
           zipAlignEnabled true
           proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
           applicationIdSuffix ".pre"
           resValue "string", "app_name", "AppName（Pre）"
           resValue "string", "account_type", "io.jiezhi.app.type.pre"
           versionNameSuffix '-pre'
       }
       debug {
           applicationIdSuffix ".debug"
           resValue "string", "app_name", "AppName（debug）"
           resValue "string", "account_type", "io.jiezhi.app.type.debug"
           versionNameSuffix '-debug'
       }
```
