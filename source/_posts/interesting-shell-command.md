title: 有趣的shell命令
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: 'http://qn-jiezhi.nospider.net/book-s27698123.jpg-thumb1'
comments: true
date: 2015-11-13 20:18:49
update: 2015-12-01
url:
tags:
- shell
- skill
categories:
- Linux
photos:
---
在[《Command Line Kung Fu》](http://book.douban.com/subject/26194502/)书中看到不少有用的命令，遂志于此。
<!--more-->
### Shell历史
* 以root身份执行之前的命令：
> $ sudo!! 或者 su -c "!!"


* 执行之前以给定字符串开头的命令：
```bash
➜  ~  whoami
jiezhi
➜  ~  !w
➜  ~  whoami
jiezhi
```


* **!^**复用前一个命令的首个参数（第二个词), **!$**复用前个命令的末个参数，如：
```bash
➜  ~  open source/_posts/interesting-shell-command.md -a Atom
➜  ~  !^ // 回车后会看到*source/_posts/interesting-shell-command.md*
//按<C+u>清除当前命令行后
➜  ~  !$ // 回车后可以看到*Atom*
```

* **!!:N**复用前个命令第N个参数
> 第一个参数为1，如!!:1,而第一个单词即命令本身为0
> 如在命令**open source/_posts/interesting-shell-command.md -a Atom**中
> **!!:0**为**open**, **!!:1**为**source/_posts/interesting-shell-command.md**

* 查看你用的最多的命令：
```shell
$ history | awk '{print $2}' | sort | uniq -c | sort -rn | head
```

* 清除历史记录
```shell
$ history -c
```

###文本处理
* 使用vim通过网络编辑文件
```shell
$ vim csp://remote-host//path/to/file
$ vim scp://remote-user@remote-host//path/to/file
```
* 以表格格式展示输出**column -t**,可以看出输出结果可读性更好了：
```shell
➜  ~  echo -e 'one two\nthree four'
one two
three four
➜  ~  echo -e 'one two\nthree four' | column -t
one    two
three  four
```

* 借助**sudo**向文件添加文本：
```shell
$ echo text | sudo tee -a file
```

* 使用**tr**命令，替换字符：
```shell
$ echo $PATH | tr ':' '\n'
```

### 网络和SSH
* 在当前目录下搭建服务器：
```shell
$ python -m SimpleHTTPServer [8080]
$ python3 -m http.server
```

*通过SSh远程挂载目录至本地
```shell
$ sshfs remote-host:/directory mountpoint
$ fusermount -u mountpoint
```
* 通过命令获取你的公共IP
```shell
$ curl ifconfig.me
$ curl ifconfig.me/ip
$ curl ifconfig.me/host
```
未完待续:-D