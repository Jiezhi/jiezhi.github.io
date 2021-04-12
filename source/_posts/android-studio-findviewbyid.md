title: 【译】Android Studio 贴士 3— FindViewById
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: 'http://qn-jiezhi.nospider.net/pet-722caa52a88ae342b5b07ac71bc0b883.jpg-thumb1'
metaAlignment: center
comments: true
date: 2017-03-16 20:52:38
url:
tags:
- android
- translate
categories:
- Android
photos:
---
今天，我们讨论一下View。让我们谈谈如何快速地从xml布局文件中获取到定义好的View实例。
<!--more-->

本文翻译自[Federico Palmieri](https://android.jlelse.eu/@fedestylah?source=post_header_lockup) 的[文章](https://android.jlelse.eu/android-studio-tips-for-productivity-3-a87e84ca6f62)。

---

我很确定每个Android开发人员都会很快就开始厌烦，为layout文件中的View编写findviewbyid的重复动作。如果你不厌烦它，那么让我们尝试另一种方式，来避免浪费时间！

有些人可能使用[ButterKnife](http://jakewharton.github.io/butterknife/)来避免findviewbyid后的转换（可能还有其他一些原因）。即使这样，它也不会减轻给每个的id加注解的痛苦。

有多少次你从xml布局和你的java代码来回切换，只是因为你忘了一个view的id？让我们放弃这种方式吧！

我们有两种不同的方式可以使用，无论你使用简单的findviewbyid还是使用ButterKnife。

---

使用ButterKnife
如果你是ButterKnife的用户，那么你可能要学习下Android ButterKnife Zelezny。

[Android ButterKnife Zelezny](https://plugins.jetbrains.com/idea/plugin/7369-android-butterknife-zelezny)

这个插件将允许你简单地生成ButterKnife注解，而对于Activity和Fragment，它也将添加ButterKnife.bind(...)方法的调用。在View中，则需要你自己添加该调用。

* Activity
![](https://cdn-images-1.medium.com/max/1600/1*u1c9R8_uDr75A8QO3FdSbQ.gif)

* Fragment
![](https://cdn-images-1.medium.com/max/1600/1*XyXCOv5S-sFrejQPfgaVaw.gif)

* View
 ![](https://cdn-images-1.medium.com/max/1600/1*7JNg8td1MVHzTuzH6EVF6w.gif)

---


不使用ButterKnife
如果你不使用ButterKnife，那么你可能需要考虑使用FindViewByMe。

[FindViewByMe](https://plugins.jetbrains.com/idea/plugin/8261-findviewbyme)

编辑：我之前向该仓库中推送对Fragment和自定义View的支持，如今，开发人员发布了解决下面提到的问题的插件的v1.3.5。 我将相应地更新GIF。

这个很棒的插件仍然在开发中，目前还不能很好地支持Fragment和自定义view。 在Activity中，它将为你执行一切，而无需手动定义和赋值布局文件中的view。 在Fragment和自定义view中，您将需要稍微更多的努力。

* Activity  
![](https://cdn-images-1.medium.com/max/1600/1*BTURA408x-3YQzxL-KWbZQ.gif)


如前所述，它不能很好地与Fragment工作，但我们任然可以使它工作。 你需要重写onCreate，因为插件只能在onCreate方法中调用生成代码来工作。

* Fragment (Hexo插入视频可能有问题，可以点击链接查看)
[油管视频](https://youtu.be/fppaDGgpScc)

{% raw %}
<video src='https://youtu.be/xSgrrmBugZc' type='video/mp4' controls='controls'  width='100%' height='100%'>
</video>
{% endraw %}

在视图中，它的工作原理与Fragment中的几乎相同。 你不能重写onCreate，但你可以通过创建一个假的onCreate()方法来作弊。

* View
[油管视频](https://youtu.be/fppaDGgpScc)
{% raw %}
<video src='https://youtu.be/fppaDGgpScc' type='video/mp4' controls='controls'  width='100%' height='100%'>
</video>
{% endraw %}

你可以帮助这个插件的开发者继续改进它，并通过fork该[仓库](https://github.com/laobie/FindViewByMe)来解决上述问题。

---

感谢，和往常一样，请让我知道你的意见！
