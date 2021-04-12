title: PHP处理来自Python的Post的json数据
tags:
  - json
  - PHP
  - POST
  - python
id: 366
categories:
  - PHP
date: 2015-05-18 11:48:53
premalink: android_edittext_no_key
thumbnailImage: http://qn-jiezhi.nospider.net/10126010,2560,1600.jpg-thumb1
metaAlignment: center
coverImage: http://qn-jiezhi.nospider.net/10052206,2560,1600.jpg-cover
coverCaption: "Android"
coverMeta: out
photos:
comments: true
---

最近用Python处理了一些json数据，但在过程中遇到一些问题，遂记录之。
<!--more-->
1.Python Post json格式数据至服务器：

查阅了一些资料，大多是这么样的：

```python
__author__ = 'jiezhi'
 
import urllib
import urllib2
 
data = {'name': 'jiezhi', 'age': '24'}
ret = urllib2.urlopen(url='http://jiezhiblog.com/test.php', data=urllib.urlencode(data))
print ret.read()
```

但是，到php那里往往是array类型的了。

经过几番折腾改用下面的代码：

```python
__author__ = 'jiezhi'
 
import urllib2
import json
 
data = {'name': 'jiezhi', 'age': '24'}
ret = urllib2.urlopen(url='http://jiezhiblog.com/test.php', data=json.dumps(data))
print ret.read()
```

2.在PHP端问题

用了改后的Python代码，却发现$_POST没有获取到数据，所以改用**file_get_contents("php://input")**来获取提交的数据：
```php
<?php
    $input = file_get_contents("php://input");
    var_dump($input);
    if ($input){
        print_r($input);
        $arr = json_decode($input,true);
        echo "arr";
        print_r($arr);
    }
?>
```
此时可以正确获取到提交的数据。
