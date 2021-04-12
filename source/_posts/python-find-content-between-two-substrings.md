title: 查找子字符串之间的内容
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: 'http://qn-jiezhi.nospider.net/python-Python-logo-notext.svg.png-thumb1'
metaAlignment: center
comments: true
date: 2015-12-21 11:51:26
url:
tags:
- python
categories:
- python
photos:
---
直接代码。
<!--more-->
```python
s = "123123STRINGabcabc"

def find_between( s, first, last ):
    try:
        start = s.index( first ) + len( first )
        end = s.index( last, start )
        return s[start:end]
    except ValueError:
        return ""

def find_between_r( s, first, last ):
    try:
        start = s.rindex( first ) + len( first )
        end = s.rindex( last, start )
        return s[start:end]
    except ValueError:
        return ""


print find_between( s, "123", "abc" )
print find_between_r( s, "123", "abc" )
```

[参见]（http://stackoverflow.com/questions/3368969/find-string-between-two-substrings）

*听首音乐放松一下*：
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="http://music.163.com/outchain/player?type=2&id=26092806&auto=0&height=66"></iframe>

>author: Jiezhi.G
email: Jiezhi.G@gmail.com
