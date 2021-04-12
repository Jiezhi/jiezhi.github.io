title: Mac上python安装第三方库
tags:
- mac
- python
id: 179
categories:
- Python 2.x
date: 2015-04-15 10:29:25
premalink: android_edittext_no_key
thumbnailImage: http://qn-jiezhi.nospider.net/10126010,2560,1600.jpg-thumb1
metaAlignment: center
coverImage: http://qn-jiezhi.nospider.net/10052206,2560,1600.jpg-cover
coverCaption: "Android"
coverMeta: out
photos:
comments: true
---

本人靠着《thinkpython》这本书算是对python入了门，但对第三方库的使用还不熟悉。

因为百度空间即将关闭，想借[Evi1m0大神](https://gist.github.com/Evi1m0)的一段python爬出代码来爬一下自己的网站的，结果其中缺少第三方库而无法运行。
**1.在线安装**
搜一些资料大多是用pip安装的，结果我的Mac上也没有pip。
首先安装pip：

```bash
sudo easy_install pip
```

如果我要安装wget模块，直接执行：
```bash
sudo pip install wget
```
**2.离线安装**
如果你已经把第三方模块下载好了，比如wget模块。其目录结构如下：

```bash
-rw-r--r--@ 1 jiezhi  staff   3.3K Jul 20  2014 PKG-INFO  
-rw-r--r--@ 1 jiezhi  staff   2.0K Jul 20  2014 README.txt  
drwxr-xr-x  3 jiezhi  staff   102B Apr 14 21:49 build  
-rw-r--r--@ 1 jiezhi  staff   1.1K Jul 20  2014 setup.py  
-rwxr-xr-x@ 1 jiezhi  staff    13K Jul 20  2014 wget.py
```
其中只要执行下面的命令即可：

```bash
sudo python setup.py install
```