title: Ubuntu上安装MySQL并配置远程登录
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: 'http://qn-jiezhi.nospider.net/blog-mysql1.png-thumb1'
metaAlignment: center
comments: true
date: 2016-01-28 22:11:20
url:
tags:
- ubuntu
- mysql
categories:
- Server
photos:
---
详见内容。
<!--more-->
##安装
`sudo apt-get install mysql-server`

> 如果是第一次安装，则在安装过程中会提示让你配置密码，用户名默认为root。
	如果想再次配置可以用此命令：
	`sudo dpkg-reconfigure mysql-server-5.5`*（找到对应的版本号）*

正常情况下安装好后，mysql服务应该已经启动，可以查看下：
`sudo netstat -tap | grep mysql`
```bash
tcp        0      0 *:mysql                 *:*                     LISTEN      1392/mysqld
tcp        0      0 192.168.1.49:mysql      192.168.1.155:65335     ESTABLISHED 1392/mysqld
```

或者
`netstat -tuln`看一下是否有3306端口被使用
一般会是
```bash
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:139             0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN
tcp        0      0 127.0.1.1:53            0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN
...
```

##配置远程登录
从上面可以看到端口3306对应的地址是**127.0.0.1**，这就意味着这个端口即mysql服务只能在本机上访问。我们想要远程访问mysql服务，那么就需要修改这个地址。
1. 修改mysql配置文件
mysql默认配置文件地址应该是**/etc/mysql/my.cnf**（如果没有尝试下/etc/my.cnf）：
`sudo vim /etc/mysql/my.cnf`
注释掉这一行：
`bind_address=127.0.0.1`

2. 给mysql新建用户，并授予远程登录的权限：
* 登录mysql
`mysql -u root -p`
* 创建新用户
``
`CREATE USER 'jiezhi' IDENTIFIED BY 'mypass';`
> 其中 **jiezhi** 为**用户名**， **mypass**为**用户密码**；

* 授予远程登录权限
`GRANT ALL ON *.* TO 'jiezhi'@'%' IDENTIFIED BY 'mypass'`
> 其中**\*.\***为授权的数据库，格式为**dbname.tablename**，这里是全部授权;
	**jiezhi**为用户名， **%**意味着容许用户访问IP为任意IP，你也可以指定IP；
	**mypass**为用户密码。

##重启MySQL
`sudo service mysql restart`
或者
`sudo /etc/init.d/mysql restart`


##远程访问
假定MySQL服务器IP为**192.168.1.100**
则远程登录可使用如下命令，因为MySQL端口默认为3306，所以下面的**-P 3306为可选项**：
`mysql -u jiezhi -h 192.168.1.100 [-P 3306] -p`
然后输入密码就可以远程登录了。

##其它
当时我在局域网配置好后，用另外的机器死活登录不上，而服务器上端口3306对应的IP为0.0.0.0，按理都是正常的。折腾半天，用mac自带的端口扫描工具，发现扫描不到3306端口，最后意识到防火墙的问题，所以修复了iptables后即可正常访问。

有空会把iptables配置再写一篇吧。
