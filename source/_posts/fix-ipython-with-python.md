title: ipython切换python
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: 'http://qn-jiezhi.nospider.net/python-Python-logo-notext.svg.png-thumb1'
metaAlignment: center
coverImage: 'http://qn-jiezhi.nospider.net/python-python-programming.jpg-cover'
coverCaption: Default
coverMeta: out
comments: true
date: 2015-10-20 15:33:52
url:
tags:
- python
- mac
- ipython
categories:
- python
photos:
---
由于Mac自带Python权限方面的问题，所以用brew另外安装了python。但用pip安装了模块后发现，python可以导入而ipython却无法导入。
<!--more-->

意味着在Mac上存在了两个python，我已经将brew安装的python设置为默认的了，其地址为：
```bash
➜  bin  which python
/usr/local/bin/python
```
而Mac自带的python地址其实是**/usr/bin/python**。

在看一下ipython：
```bash
➜  bin  which ipython
/usr/local/bin/ipython
➜  bin  head `which ipython`
#!/usr/bin/python

# -*- coding: utf-8 -*-
import re
import sys

from IPython import start_ipython

if __name__ == '__main__':
    sys.argv[0] = re.sub(r'(-script\.pyw|\.exe)?$', '', sys.argv[0])
```
这也就意味着ipython现在默认是指向系统自带的python的，所以我们需要修改ipython命令头部。
即：**#!/usr/bin/python**改为**#!/usr/local/bin/python**。

问题解决。
