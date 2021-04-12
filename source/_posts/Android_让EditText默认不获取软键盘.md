title: Android 让EditText默认不获取软键盘
tags:
  - android
id: 624
categories:
  - Android
date: 2015-08-25 18:11:52
premalink: android_edittext_no_key
thumbnailImage: http://qn-jiezhi.nospider.net/android1119002570721cd2c8o.jpg-thumb1
metaAlignment: center
photos:
comments: true
---

在Android开发中，会遇到这种情况，当页面中存在EditText时，默认会把软键盘给调出来，而我们并不想让软键盘弹出。
<!--more-->
解决方案：

在EditText的**父控件**中设置如下两个属性即可：
```xml
android:focusable="true"
android:focusableInTouchMode="true"
```
