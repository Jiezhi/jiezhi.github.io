title: 解决mv Argument list too long问题
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: 'http://qn-jiezhi.nospider.net/10396724,2560,1600.jpg-thumb1'
metaAlignment: center
comments: true
date: 2015-11-03 15:49:12
url:
tags:
- Linux
- shell
- bash
categories:
- Linux
photos:
---

移动近百万文件的时候，忽然报出错误：
```bash
$ mv 1/11*.dat 2/
-bash: /bin/mv: Argument list too long
```
<!--more-->

显然当需要移动的文件太多的时候，mv命令就束手无策了。

比如要将文件夹*1*中所有以*11*开头的dat文件移到文件夹*2*下：
解决办法：
使用find命令：
```bash   
$ find 1 -name '11*.dat' -exec mv {} 2 \;
```
> 其中**-exec**后为需要运行的命令，**{}**为搜到的文件，**\;**为exec的结束符。

不过好像很耗时的样子。。

reference:
1.http://stackoverflow.com/questions/11942422/moving-large-number-of-filess
