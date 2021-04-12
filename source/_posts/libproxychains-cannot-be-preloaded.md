title: 解决'libproxychains.so.3' from LD_PRELOAD cannot be preloaded问题
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: 'http://qn-jiezhi.nospider.net/pet-d0d3b36377ba6346b0f053a90f63f886.jpg-thumb1'
metaAlignment: center
comments: true
date: 2016-02-02 21:48:28
url:
tags:
- ss
- proxychains
categories:
- Ubuntu
photos:
---
在本地Ubuntu服务器配置好ss客户端后，如果想在命令行以及想ssh远程的时候可以访问某些404页面时需要**proxychains**工具。
<!--more-->

但是在运行proxychains时报错了：

```bash
➜  ~  proxychains ping google.com
ProxyChains-3.1 (http://proxychains.sf.net)
ERROR: ld.so: object 'libproxychains.so.3' from LD_PRELOAD cannot be preloaded (cannot open shared object file): ignored.
```

看来proxychains无法加载**libproxychains.so.3**库，查到的[资料](http://askubuntu.com/questions/293649/proxychains-ld-preload-cannot-be-preloaded)是修改**/usr/bin/proxychains**文件：

`export LD_PRELOAD=libproxychains.so.3`

改为

`export LD_PRELOAD=/usr/lib/libproxychains.so.3`

但是保存后还是会提示无法加载该库。

所以呢，用find命令找一下该库吧：

```bash
➜  ~  find /usr/ -name libproxychains.so.3 -print
/usr/lib/x86_64-linux-gnu/libproxychains.so.3
```

所以改为

`/usr/lib/x86_64-linux-gnu/libproxychains.so.3`

就可以了
