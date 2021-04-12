title: Ubuntu无法登陆问题
tags:
  - ubuntu
id: 291
categories:
  - Ubuntu
date: 2015-04-29 21:35:57
premalink: android_edittext_no_key
thumbnailImage: http://qn-jiezhi.nospider.net/10126010,2560,1600.jpg-thumb1
metaAlignment: center
coverImage: http://qn-jiezhi.nospider.net/10052206,2560,1600.jpg-cover
coverCaption: "Android"
coverMeta: out
photos:
comments: true
---

今天修改/etc/environment文件，重启后发现无法登陆进入了，用vssh登陆出现以下错误：
```
-bash: groups: command not found
Command 'ls' is available in '/bin/ls'
The command could not be located because '/bin' is not included in the PATH environment variable.
ls: command not found
Command 'lesspipe' is available in the following places
 * /bin/lesspipe
 * /usr/bin/lesspipe
The command could not be located because '/bin:/usr/bin' is not included in the PATH environment variable.
lesspipe: command not found
Command 'dircolors' is available in '/usr/bin/dircolors'
The command could not be located because '/usr/bin' is not included in the PATH environment variable.
dircolors: command not found
Command 'ls' is available in '/bin/ls'
The command could not be located because '/bin' is not included in the PATH environment variable.
ls: command not found
```
此时一些例如"ls"的外置命令都无法使用，解决办法：
```
export PATH=/usr/bin:/bin
sudo vim /etc/environment
```

然后在里面添加变量：

```
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
```

保存后：

```
source /etc/environment
```
问题解决！
