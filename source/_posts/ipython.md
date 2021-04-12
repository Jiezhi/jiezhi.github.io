title: iPython技巧
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: >-
  http://qn-jiezhi.nospider.net/pet-0e24cfd3754db9af5e2f7119f1160964.jpg-thumb1
metaAlignment: center
comments: true
date: 2017-10-18 15:43:12
url:
tags:
categories:
photos:
---
记录一下ipython的一些技巧

<!--more-->

## 常用命令

### Introspection

- 在对象前或对象后使用问号 **?** ，将会展示该对象的一些基本信息
- 如果在一个方法名上使用问号，将会展示该方法的说明文档（docstring）
- 使用双问号??将会展示该方法的源码
- ?和通配符*的搭配使用有奇效：

    In [100]:np.*load*?
    np.__loader__
    np.load
    np.loads
    np.loadtxt
    np.pkgload

### %run 命令

- 该命令可以在IPython中直接执行Python程序文件。

### %paste

- 执行剪贴板中的代码

## 快捷键

![](https://static.notion-static.com/1a3e46659f244dea86a85776cb6e6799/Untitled)

![](https://static.notion-static.com/64b10bbc0c644bbaa4c356fa803c87ee/Untitled)

参考

- 《Python for Data Analysis》Chapter 3
