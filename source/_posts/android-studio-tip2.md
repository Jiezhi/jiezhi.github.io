title: 【译】Android Studio 技巧2——资源和图标
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: 'http://qn-jiezhi.nospider.net/pet-9faca0d14eb95123fbb85897640cdab8.jpg-thumb1'
metaAlignment: center
comments: true
date: 2017-03-19 14:16:21
url:
tags:
- android
- translate
categories:
- Android
photos:
---
这是我的第二篇帖子。 今天我要讨论一下资源，特别是图标这块。
<!--more-->
---

### [Android Drawable Importer](https://plugins.jetbrains.com/idea/plugin/7658-android-drawable-importer)

作为Android开发人员，你应该在drawable文件夹中手动地导入过很多图标了。

有些聪明的设计师给你的资源已经按照正确的命名放在了正确的文件夹中了，这会简化你很多的工作。但有时候你可能要重命名和手动移动每个PNG文件到相关的drawable文件夹中。 这会花费你一些时间，些许苦恼。 我非常想把这时间用在别的事情上，那我们如何改进它并停止浪费时间呢？

“Android Drawable Importer”这款插件将会帮助到你。 它主要有4个主要特点：

* 批量导入资源
* 导入图标资源包
* 导入矢量资源
* 多分辨率（Multisource）资源
---
### **批量导入资源**
这是个非常有用的特性，顾名思义，就是导入一批drawable。 假设你有5个只有一种分辨率的不同的图标，你可以一次把他们导入到指定分辨率的文件夹中。 如果你想的话，它还创建其他的png的drawable文件夹。
![](https://cdn-images-1.medium.com/max/800/1*ZKrIWeTY5cSM04u74chx6w.gif)

### **导入图标资源包**
*导入图标资源包*允许你从预定义资源集合中导入图标，而且你还可以指定其大小，颜色，格式和尺寸。
![](https://cdn-images-1.medium.com/max/800/1*7CAEuZjGeDRuYcUy8iBx0A.gif)

这已经不是特别有用了，因为Android Studio集成了一个名为[Image Asset Studio](https://developer.android.com/studio/write/image-asset-studio.html)的功能，通过官方的这个插件你可以做同样或更多的事。 例如，你可以将文本作为资源导出，或者你可以用来处理图片。
![](https://cdn-images-1.medium.com/max/800/1*Qb-T5WtUcvMDlo2v7zfefg.gif)

### **导入矢量资源**
*导入矢量资源*使你能够从给定的集合导入矢量资源。 Android Studio已经集成了一个名为[Vector Asset Studio](https://developer.android.com/studio/write/vector-asset-studio.html)的类似工具。 此外，它还允许你从给定的SVG或PSD导入向量。 我建议在这种情况下使用集成工具而不是插件。

### **多分辨率资源**
最后但同样重要的是，*多分辨率资源*。 你可以为每个导入的资源的不同分辨率指定为不同图标。 如果你需要根据屏幕分辨率使用不同的图标，或者如果你在不同的文件夹中有同样的图标，并且想要立即导入所有图标，这个可能非常有用。
![](https://cdn-images-1.medium.com/max/800/1*kisvCgiv-_DgO85mntwWNw.gif)

---

本文到此结束。 如果有想法请留下你的评论！

