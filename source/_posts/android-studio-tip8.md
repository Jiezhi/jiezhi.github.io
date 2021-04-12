title: 【译】Android Studio 技巧8 —— Json模型
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: 'http://qn-jiezhi.nospider.net/pet2c03cbbedff9872c460c528e00dea539.jpg-thumb1'
metaAlignment: center
comments: true
date: 2017-03-21 20:58:31
url:
tags:
- android
- json
- plugin
- translate
categories:
- Android
photos:
---
最近JSON对象非常受欢迎。那让我们看看如何对他们建模。
<!--more-->

试想一下，你的后端团队给了你一个你期望的JSON示例。 分析过后，你尝试建立POJO模型，所以你通过众多的第三方库简单地进行注入（例如[gson](https://github.com/google/gson)）。 你花了很多时间来创建表示json的每个单个实体的POJO。 这非常无聊而且浪费了大量的时间。 部分人可能会使用[json2pojo](http://www.jsonschema2pojo.org/)网站，但你仍然必须手动将生成的类放入到你的包，而且可能变得很混乱。

让我们看一个不错的插件，可以更高效地处理这个过程。

---

### [RoboPOJOGenerator](https://plugins.jetbrains.com/plugin/8634-robopojogenerator)
它支持从模式(schema)或从实际的JSON数据中生成POJO模型。 它还可以生成支持gson，Jackson，LoganSquare和AutoValueGson方案的对象，因此您可以使用任何种类的库来解析。 它还支持*Kotlin*，这是一个非常好的功能。它还会问你是否要生成*toString*和*getters＆setters*方法。
![](https://cdn-images-1.medium.com/max/800/1*YoEqqnUgEcJDyFgu6_KLgw.gif)

---

我希望这将帮助你加快开发过程，并且减少无聊的工作量！ 敬请关注下一篇文章！
