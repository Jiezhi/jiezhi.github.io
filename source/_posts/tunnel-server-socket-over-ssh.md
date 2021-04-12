title: 通过 ssh 远程代理，访问远程资源
author: Jiezhi.G
email: Jiezhi.G@gmail.com
categories:
  - Linux
premalink: fix_gravatar
metaAlignment: center
comments: true
date: 2019-03-29 10:10:26
url:
tags:
  - ssh
photos:
---
平时我们使用SSH大多用来，登录远程服务器来进行远程操作。

但是 SSH 强大的功能远远不止登录远程服务器，比如说我在服务器上部署了一个服务，但是端口号并没有对外开放怎么办？又或者生产环境的数据库本地无法连接，而只能通过线上环境连接怎么办？
<!--more-->
# SSH
## SSH 访问远程页面
*在 Shadowsocks 之前，很多人是使用 SSH 来跨过长城的。*
现在遇到的问题，生产环境部署了 Flink，虽说 Flink 提供了 REST Api 来查看信息的，但是查看页面还是更方便点。但是又没办法连接生产环境怎么办？
其实可以通过 SSH 建立本地和服务器的通道，进而可以访问远程服务器的资源。
比如，flink 页面默认端口号为8081，在终端里执行这个命令后，即可通过访问本地的 localhost:8081 访问服务器上的 flink 页面。
`ssh -L 8081:localhost:8081 user@flinkserver`

## SSH 代理访问数据库
其实道理和上面的一样，一般生产环境数据库我们是没法直接连接的。不过只要有可以访问该数据库的其他服务器 SSH 配置就行了，同一个原理，但实现方式就很多了。
1. 直接登录服务器，通过 mysql 命令来访问。简单粗暴，但比较繁琐。

2. 使用支持 SSH tunnel 的客户端来连接数据库，比如 [DataGrip](https://www.jetbrains.com/datagrip/)
{% asset_img datagrip_ssh.png %}

3. 同样，使用 ssh 命令来建立通道，本地就可以访问了。
比如数据库地址为：192.168.100.100:3306，远程服务器为：root@192.168.10.200，使用本地3307端口来映射（其他端口也行，我因为本地3306被用了，所以用3307了）
`ssh -L 3307:192.168.100.100:3306 root@192.168.10.200`

执行过后，只需要连接 `localhost:3307` 就可以访问数据库了。

---
SSH 还有很多强大的功能，下次有空再写一下配置的优化。

---
Reference:
https://askubuntu.com/questions/112177/how-do-i-tunnel-and-browse-the-server-webpage-on-my-laptop

https://unix.stackexchange.com/questions/14160/ssh-tunneling-error-channel-1-open-failed-administratively-prohibited-open

