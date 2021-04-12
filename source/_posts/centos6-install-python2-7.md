title: centos6安装python2.7
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: 'http://qn-jiezhi.nospider.net/blog-centos_1.jpg-thumb1'
metaAlignment: center
comments: true
date: 2016-03-05 23:02:40
url:
tags:
- centos
- python2.7
- djangoC
categories:
- Centos
photos:
---
最近开始学习在centos上搭建nginx和django，但是centos6 自带的python2.6。但是基本无法运行django，所以需要安装python2.7。
<!--more-->

nginx以及从源码编译好并且已经运行了，但是配合django使用的时候却出了点问题，具体怎么怎么用django搭建服务器可参考[Setting up Django and your web server with uWSGI and nginx](http://uwsgi-docs.readthedocs.org/en/latest/tutorials/Django_and_nginx.html)。

1. centos6安装python2.7并设置为默认python命令：
* 下载python2.7.11后编译并安装：
```shell
wget http://www.python.org/ftp/python/2.7.11/Python-2.7.11.tar.bz2
tar jxvf Python-2.7.11.tar.bz2
cd Python-2.7.11
./configure && make && make install
```
安装好的2.7版本路径为**/usr/local/bin/python2.7**

* 安装好后替换默认**python**：
先查看默认的python路径：
```shell
which python
```
我这边的是位于**/usr/bin/python**,查看一下该文件并不是python2.6的链接文件，用**diff**命令看了下和同路径下的**python2.6**没有区别，所以可以直接删除或者重命名为*python.bak*

由于系统程序依赖2.6版本，所以先修改下依赖：
```shell
vim /usr/bin/yum
```
将其头部python改为**#!/usr/bin/python2.6**，运行下yum是否出错，不出错可以直接将python2.7设置为系统默认命令了：
```shell
ln -s /usr/local/bin/python2.7 /usr/bin/python
```
在执行下**python --version**看看是否已经是python2.7的了。


2.本以为这样就可以高枕无忧了，结果好多东西都得重新配置：
* pip
重新下载并配置：
```shell
wget https://bootstrap.pypa.io/get-pip.py
python get-pip.py
```
* 重新安装virtualenv
```shell
pip install virtualenv
```

* 运行django缺少_sqlite3：
```shell
ln -s /usr/lib/python2.6/lib-dynload/_sqlite3.so /usr/local/lib/python2.7/lib-dynload
```
*参考[这里](http://stackoverflow.com/questions/11394013/problems-with-python-2-7-3-on-centos-with-sqlite3-module)*

* 再次运行还会出现个问题：
`“UnicodeDecodeError: 'ascii' codec can't decode byte”`
如果是使用virtualenv环境下操作的话，此时可以在**django/lib/python2.7/site-packages
**路径下新建名为`sitecustomize.py`的文件：
```python
# encoding=utf8  
import sys  

reload(sys)  
sys.setdefaultencoding('utf8')
```
参考[这里](http://stackoverflow.com/questions/21129020/how-to-fix-unicodedecodeerror-ascii-codec-cant-decode-byte)
