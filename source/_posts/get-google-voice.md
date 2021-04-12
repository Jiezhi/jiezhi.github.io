title: 6行脚本获取Google Voice
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: 'http://qn-jiezhi.nospider.net/pet-761a1e28219e08e1f1c5b491fd7aa3b3.jpg-thumb1'
metaAlignment: center
comments: true
date: 2017-03-12 21:14:41
url:
tags:
categories:
photos:
---
Google Voice（简称GV）在程序员的圈子里也不算太陌生了，尤其是经常混V站的人应该经常见到这货的出现。不清楚的可以关掉或者先自行Google了。

<!--more-->

如果你上网查教程，其实很简单无非使用类似TextNow/TextMe等先申请个美国的号码，然后绑定Google账号，再从Google申请个Voice号码即可。步骤也不多，但很多人Google Voice号码都选好了，可就是点击申请会一直出现『请求错误』的提示。据报道称，解决这个问题的唯一途径就是『狂点大法』——不停地点，运气好5分钟或半小时就能申请到好，也有运气不好，需要一天甚至更多的时间来点击申请。所以有的人就放弃了，或者直接去马云家直接花几十块钱买了。

目前实现这个『狂点大法』的无非两种途径，模拟鼠标点击和模拟网络请求。

模拟鼠标点击：
顾名思义就是不停地让鼠标在申请按钮上点击，直到你收到Google 邮件恭喜你获得了号码为止。这种很多人会使用『按键精灵』类似的软件来实现不停地点击。但是我选择了自己写python脚本来模拟点击，毕竟自己写的代码才放心。[代码](https://gist.github.com/Jiezhi/aad8a37114825b0ce1a5bc48bf5f3cac)如下：

```python
from pymouse import PyMouse
import time

m = PyMouse()

while True:
    m.click(528, 800-196, 1)
    time.sleep(3)
```

其中点击的坐标要改成你自己的，可使用截屏工具来获得按钮的坐标。

模拟网络请求：
这个方法就是直接跳过点击事件，毕竟你点击了也是通过网络请求来实现的。这样子更彻底点，这里粗略地介绍个思路：

打开浏览器的开发模式（F12）
点击网页上的申请按钮
找到本次post请求，然后右键复制cURL
把cURL请求扔到脚本里，找个服务器或者自己电脑终端里在后台运行即可
这个我也放出个[shell 脚本](https://gist.github.com/Jiezhi/d02c46449dda06ed0866757bc7ea92c2)吧：

```bash
for (( i=1; i>0; i++ ))
do
    curl 'https://www.google.com/voice/b/0/service/post' ... --compressed

    sleep 3s
done
```

如果你需要帮助可以在博客下方留言即可。
