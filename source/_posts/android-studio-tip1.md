title: 【译】android studio 技巧1——Parcelable
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: 'http://qn-jiezhi.nospider.net/pet-0e24cfd3754db9af5e2f7119f1160964.jpg-thumb1'
metaAlignment: center
comments: true
date: 2017-03-19 15:49:25
url:
tags:
- android
- translate
categories:
- Android
photos:
---
最后，我决定开始我自己的技术博客。首先发布一系列的帖子来描述一些我每天开发都在使用的Android Studio插件。

<!--more-->
如果你不知道如何在Android Studio中安装插件，请点击此[链接](http://stackoverflow.com/a/30617737/2855514)。

### [Android Parcelable代码生成器](https://plugins.jetbrains.com/idea/plugin/7332-android-parcelable-code-generator)
你是否有过必须在两个Activity/Fragment之间转发数据情况？

如果是的话，你应该熟悉Bundle类的用法，你也知道你只能在其中放置一个特定类型的项目。

但是如果我想转发整个对象呢？ 那么，该对象将需要实现Serializable或Parcelable接口。 第一种方式对于开发人员还算简单，在第一个实例中，我们似乎要用这种方式。 但是，正如这个[帖子](http://www.developerphil.com/parcelable-vs-serializable/)解释的，使用Parcelable接口是更有效率的。 不过它的缺点是需要写一点代码，而且可能不是那么直观。
![](https://cdn-images-1.medium.com/max/1600/1*VCvqU-RYvueGkL8G9Btc7w.png)
这是“Android Parcelable代码生成器”插件所做的。 你可以让这个插件生成实现Parcelable接口所需的代码。

具体怎么做呢，请往下看：
![](https://cdn-images-1.medium.com/max/1600/1*hN7MZbTTNY8ZUYwVSsqEbQ.gif)

如果有人使用Kotliin的话，这里也有一款插件可以使用：[Parcelable Code Generator(for kotlin)](https://plugins.jetbrains.com/idea/plugin/8086-parcelable-code-generator-for-kotlin-)
---

敬请关注下一篇文章！
