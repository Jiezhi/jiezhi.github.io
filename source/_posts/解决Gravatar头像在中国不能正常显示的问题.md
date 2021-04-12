title: 解决Gravatar头像在中国不能正常显示的问题
date: 2015-09-16 15:14:06
categories:
- hexo
tags:
- gravatar
- hexo
thumbnailImage: http://qn-jiezhi.nospider.net/pet-0e24cfd3754db9af5e2f7119f1160964.jpg-cover
metaAlignment: center
coverImage: http://qn-jiezhi.nospider.net/android12736509,2560,1600.jpg
coverCaption: "Default Cover Image"
coverMeta: out
comments: true

---
如果你遇到Gravatar在中国不能正常显示的问题，这篇文章也许可以帮助你。
<!-- more -->
昨天花了大半天时间终于把hexo搭建好了，也挑选了个模板，并进行配置。同时也将之前wp中的一些文章，再次迁移至此。等deploy至github后，用手机访问，发现不能正常显示图片。嗯，在国内你会遇见各种问题。Gravatar已经被墙了。

搜了下，好像使用SSL访问Gravatar则可以，所以将hexo中头像配置文件修改了一下：

{% codeblock hexo/node_modules/hexo/lib/plugins/helper/gravatar.js %}

var str = 'https://www.gravatar.com/avatar/' + md5(email.toLowerCase());

{% endcodeblock %}

在电脑上试了下是可以的，但在手机上还是无法正确显示头像。。。



索性直接改为微博头像吧，我的weibo头像地址：http://tp2.sinaimg.cn/1723846713/180/40002045318/1

{% codeblock hexo/node_modules/hexo/lib/plugins/helper/gravatar.js %}

function gravatarHelper(email, options){

  // use weibo avatar instead of gravatar.

  return 'http://tp2.sinaimg.cn/1723846713/180/40002045318/1';

}

{% endcodeblock %}



改完后重新生成即可。

```
hexo generate
```