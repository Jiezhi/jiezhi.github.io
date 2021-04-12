title: 解决Can't connect to MySQL server问题
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: 'http://qn-jiezhi.nospider.net/blog-mysql.png-thumb1'
metaAlignment: center
comments: true
date: 2016-01-28 22:06:43
url:
tags:
- MySQL
categories:
- MySQL
photos:
---

今天远程访问Ubuntu上的MySQL时出现错误：
`ERROR 2003 (HY000): Can't connect to MySQL server on '192.168.1.49' (60)`

<!--more-->


所以先登录服务器，用命令`netstat -tuln`查看一下：
```bash
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 0.0.0.0:3306            0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:139             0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN
tcp        0      0 127.0.1.1:53            0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:25              0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:445             0.0.0.0:*               LISTEN
...
```
可以看出，MySQL配置应该没问题的，当然也确保MySQL用户密码等都是对的。

查了半天，怀疑是不是iptables问题（但记不得之前曾经配置过iptables，所以一直没想这块）：
`sudo iptables -L`
```bash
Chain INPUT (policy ACCEPT)
target     prot opt source               destination
ACCEPT     all  --  anywhere             anywhere
ACCEPT     all  --  anywhere             anywhere             state RELATED,ESTABLISHED
ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:http
ACCEPT     tcp  --  localhost            anywhere             tcp dpt:mysql
DROP       tcp  --  anywhere             anywhere             tcp dpt:mysql
ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:mysql
ACCEPT     tcp  --  192.168.1.0/24       anywhere             tcp dpt:mysql

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination
DOCKER     all  --  anywhere             anywhere
ACCEPT     all  --  anywhere             anywhere             ctstate RELATED,ESTABLISHED
ACCEPT     all  --  anywhere             anywhere
ACCEPT     all  --  anywhere             anywhere

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination

Chain DOCKER (1 references)
target     prot opt source               destination

```
很奇怪这边怎么会有一个对mysql访问的DROP规则，但还是先删为敬！
这次再iptables命令多加个参数：
`sudo iptables -L -n --line-number  `
```bash
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination
1    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0
2    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0            state RELATED,ESTABLISHED
3    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:80
4    ACCEPT     tcp  --  127.0.0.1            0.0.0.0/0            tcp dpt:3306
5    DROP       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:3306
6    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:3306
7    ACCEPT     tcp  --  192.168.1.0/24       0.0.0.0/0            tcp dpt:3306

Chain FORWARD (policy ACCEPT)
num  target     prot opt source               destination
1    DOCKER     all  --  0.0.0.0/0            0.0.0.0/0
2    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0            ctstate RELATED,ESTABLISHED
3    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0
4    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0

```
这下每条规则前都有了序号，所以可以根据序号直接来修改或删除：

`sudo optables -D INPUT 5`
把INPUT的第五条规则删除，然后去客户端再次登录MySQL，成功！
