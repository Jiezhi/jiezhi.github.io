title: Android 在style.xml文件中设置match_parent 和 wrap_content
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: 'http://qn-jiezhi.nospider.net/pet-0e24cfd3754db9af5e2f7119f1160964.jpg-thumb1'
metaAlignment: center
comments: true
date: 2016-07-14 15:49:02
url:
tags:
- android
- View
categories:
- Android
photos:

---

在style文件中直接设置android:width值为wrap_content的话会报错:

	Cannot resolve symbol 'wrap_content'

那该如何解决？

<!--more-->

一般情况下，我们会把界面中共用的一些属性直接定义在style.xml文件中，但是在设置View的高度和宽度时，如果想用wrap_content和match_parent的话，则需要先定义一下：

在**dimmens.xml**文件中添加：

```xml
    <dimen name="match_parent">-1dp</dimen>
    <dimen name="wrap_content">-2dp</dimen>
```
然后在**style.xml**文件中直接引用即可，如：

```xml
<style name="begin_medium_text" parent="medium_text">
    <item name="android:gravity">end|center_vertical</item>
    <item name="android:width">@dimen/wrap_content</item>
    <item name="android:height">@dimen/match_parent</item>
</style>
```

Reference: http://stackoverflow.com/questions/6859331/how-can-i-use-layout-width-using-resource-file
