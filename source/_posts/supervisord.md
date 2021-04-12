title: Supervisord介绍与使用
author: Jiezhi.G
email: Jiezhi.G@gmail.com
categories:
  - Linux
premalink: fix_gravatar
metaAlignment: center
comments: true
date: 2020-01-16 15:19:46
url:
tags:
  - tools
photos:
---
有时候我们希望有些进程能时刻运行，并能在出错后自动重启。除了用系统带的 systemd 之外，你还可以用supervisord。
这里简单介绍 supervisord 的安装和使用。
<!--more-->
[项目地址](http://supervisord.org/index.html)


# 安装
安装比较简单：
`pip install supervisor`

# 创建配置文件
使用`echo_supervisord_conf`命令可以创建一份配置文件模板，比如要把配置文件放在`/etc/supervisord.conf`，则执行：
`echo_supervisord_conf > /etc/supervisord.conf`
另外如果你想把配置文件放在其他地方，则可以自己指定地址，只是在启动 supervisord 时加`-c`显式指定配置文件地址`supervisord -c supervisord.conf` 

# 配置
## 配置任务
修改`supervisord.conf`文件，在84行左右位置加入你想要执行的进程，按如下格式：
```bash
[program:theprogramname]
command=/bin/cat              ; the program (relative uses PATH, can take args)
```
比如我想用命令通过 ssh 来代理远程的一个 web 页面：
```bash
[program:cm]
command=ssh -N -L *:7180:localhost:7180 214
```
## 配置 http 服务
supervisord 可以开启 http 服务，通过 web 页面来查看和管理任务。
如果需要开启，可以打开`inet_http_server`配置，大概在39行。
```bash
[inet_http_server]         ; inet (TCP) server disabled by default
port=0.0.0.0:9001          ; ip_address:port specifier, *:port for all iface
username=admin             ; default is no username (open server)
password=thepassword       ; default is no password (open server)
```

# 启动
直接执行`supervisord`即可启动，如果需要指定配置文件位置则使用`-c`参数。

# 管理
## Web 页面管理
如果在配置中开启了 http 服务，则可以直接访问 http 地址，输入账户密码后即可。
![](https://i.loli.net/2020/01/16/c9oIUnOE4KTMJFf.png)

## 通过 supervisorctl 管理
比如要查看所有任务状态：
`supervisorctl status all`
或者重启所有服务：
`supervisorctl restart all`

# 重新加载配置文件
```shell
supervisorctl reread
supervisorctl update
```
更多命令可参见：
[supervisorctl-command-line-options](http://supervisord.org/running.html#supervisorctl-command-line-options)
