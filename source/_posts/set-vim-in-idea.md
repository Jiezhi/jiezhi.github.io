title: 设置 idea 中的 Vim（同样适用于jetbrains 家其他产品）
author: Jiezhi.G
email: Jiezhi.G@gmail.com
categories:
  - Tools
premalink: fix_gravatar
metaAlignment: center
comments: true
date: 2019-08-30 09:33:38
url:
tags:
photos:
---
之前每次启动 idea 或 pycharm 后都要单独设置下才开始工作，那么怎么才能像设置 vimrc 一样设置 idea 中的 vim 呢？
<!--more-->

可能很多人.vimrc 文件中都有这个设置：
```shell
set relativenumber
set number
```
{% asset_img vim-rnu.png %}
这个设置使得上下跳转（j/k）再也不用去计算行数了.

idea 中的 vim 设置也像.vimrc一样，只不过设置在`.ideavimrc`文件中。

所以只要在在~/.ideavimrc 文件中加上以下配置即可（有其他vim设置也可以加入到这里）:
```shell
set relativenumber
set number
```

