title: python监测服务器 异常后发邮件
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: 'http://qn-jiezhi.nospider.net/python-Python-logo-notext.svg.png-thumb1'
metaAlignment: center
comments: true
date: 2016-03-20 21:58:31
url:
tags:
- monitor
- python
- email
categories:
- python
photos:
---
最近写了个python脚本来监控服务器，结合crontab定时任务来定期检查服务器是否可以正常访问。

<!--more-->
这里服务器给定一个固定url，服务器端监测数据库表是否正常，如果正常直接给出「ok」的回复。
里面逻辑也很简单直接上代码：
```python
#!/usr/bin/python
# coding: utf-8
"""
Author: Jiezhi.G@gmail.com
Function: Monitor server and send mail when server down!
Date: 2016-03-18
PS: You can add this msg to cron:
* 9,21 * * * python ~/monitor.py >> ~/monitor.log
"""

import urllib2
import smtplib
import time
from email.mime.text import MIMEText
from email.header import Header

sender_mail = "***"
password = '***'
receiver = ['***', '***', '***']
smtp_server = "smtp.yeah.net"

def send_alert_mail():
    msg = MIMEText('服务器异常，请检查服务器!', 'plain', 'utf-8')
    msg['Subject'] = '!!!服务器异常'
    msg['From'] = 'Monitor<***>'
    server = smtplib.SMTP(smtp_server, 25)
    server.login(sender_mail, password)
    server.sendmail(sender_mail, receiver, msg.as_string())
    server.quit()


def check_server(url):
    ret = urllib2.urlopen(urllib2.Request(url))
    data = ret.read()
    return data == "ok"

if __name__ == '__main__':
    url_ch = 'http://zh.example.com/api/isok.php'
    url_en = 'http://en.examp.com/api/isok.php'
    t = time.strftime("%Y-%m-%d %A %X %Z", time.localtime())
    if not check_server(url_ch):
        print t, 'server down'
        send_alert_mail()
    else:
        print t, 'every thing is ok'
```
