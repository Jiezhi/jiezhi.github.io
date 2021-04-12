title: 解决adb无法找到genymotion模拟器问题
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: 'http://qn-jiezhi.nospider.net/android1119002570721cd2c8o.jpg-thumb1'
metaAlignment: center
coverImage: 'http://qn-jiezhi.nospider.net/android12736509,2560,1600.jpg-cover'
coverCaption: Default
coverMeta: in
comments: true
date: 2016-05-23 21:33:05
url:
tags:
- adb
- android
- genymotion
categories:
- Android
photos:
---
今天升级了一下Android SDK结果开着genymotion模拟器的情况下却找不到设备。
<!--more-->
用`adb devices`查看会出现如下错误：

```bash
➜  ~ adb devices
List of devices attached
adb server version (32) doesn't match this client (35); killing...
error: could not install *smartsocket* listener: Address already in use
ADB server didn't ACK
* failed to start daemon *
error: cannot connect to daemon
```
看了是adb版本的问题了，在genymotion中有个设置成你自己的sdk地址即可，重启模拟器后即可：
![](genymotion-settings.png)


*Reference：https://twitter.com/chiuki/status/709410135551168512*

