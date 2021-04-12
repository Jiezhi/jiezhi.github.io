title: Ubuntu 64位adb无法使用问题的解决
tags:
  - android
  - aosp
id: 255
categories:
  - Android
date: 2015-04-20 21:22:01
premalink: android_edittext_no_key
thumbnailImage: http://qn-jiezhi.nospider.net/10126010,2560,1600.jpg-thumb1
metaAlignment: center
coverImage: http://qn-jiezhi.nospider.net/10052206,2560,1600.jpg-cover
coverCaption: "Android"
coverMeta: out
photos:
comments: true
---

![](http://i2.hexunimg.cn/2011-11-30/135825142.jpg)

今天在Ubuntu 14.02 （64位）下安装了Android开发环境，其中遇到adb无法使用的情况，以为是没配置环境变量的问题，就添加一下：

```
sudo vim /etc/profile
```

```
export PATH="$PATH:/home/username/sdk/tools"
export PATH="$PATH:/home/username/sdk/paltform-tools"
```

但是系统注销后运行，还是会报错：没有该文件或文件夹。

经查阅发现Linux下SDK的adb命令是32位的，所以要在64位系统上要安装兼容包：

Ubuntu 13.10及以上的版本：

```
sudo dpkg --add-architecture i386
sudo apt-get update
sudo apt-get install libncurses5:i386 libstdc++6:i386 zlib1g:i386
```
以下的版本：

```
apt-get install ia32-libs
```

执行过后可发现adb可正常运行。
