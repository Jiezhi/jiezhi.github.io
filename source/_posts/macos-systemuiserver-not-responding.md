title: 解决 macos systemuiserver 无响应的问题
author: Jiezhi.G
email: Jiezhi.G@gmail.com
categories:
  - MacOS
premalink: fix_gravatar
metaAlignment: center
comments: true
date: 2019-03-07 09:28:50
url:
tags:
  - Macos
  - Bug
photos:
---

把 MBP 升级到最新系统（10.14.3）后，发现休眠后再打开电脑，点击无线网或切换wifi 的时候，系统开始转菊花，系统设置也无响应了，去Activity Monitor 里会发现 **systemuiserver not responding**，貌似除了重启别无他法。

当然除了重启，你只需要在命令行里执行一个命令就可以了。

```bash
sudo kill -9 `ps aux | grep -v grep | grep /usr/libexec/airportd | awk '{print $2}'`
```

<!--more-->

这行命令的意思是强行杀死airportd的进程，然后会导致相关进程重启。虽说没有从根本上解决问题，但比重启系统好多了。
