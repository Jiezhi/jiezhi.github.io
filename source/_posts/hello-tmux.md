title: tmux初步入门
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: 'http://qn-jiezhi.nospider.net/pet-0e24cfd3754db9af5e2f7119f1160964.jpg-thumb1'
metaAlignment: center
coverCaption: Default
coverMeta: out
comments: true
date: 2016-09-29 15:04:17
url:
tags:
- tool
- tmux
categories:
- linux
photos:
---
记录一下常用的tmux命令
<!--more-->
* Create Named Sessions
```bash
tmux new-session -s basic

or

tmux new -s basic
```


* detach session
```bash
PREFIX d
```


* Retattaching to Existing Sessions
```bash
tmux list-sessions

or

tmux ls
```

* Attach to Sessions
```bash
tmux attach -t basic
```
