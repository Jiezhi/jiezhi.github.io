title: 解决Error=NeedsBrowser问题
author: Jiezhi.G
email: Jiezhi.G@gmail.com
thumbnailImage: 'http://qn-jiezhi.nospider.net/google-google-services-google-play-500x500-4c29d72356b4cc153d0b8835d712ce2c-300x288.jpg-thumb1'
metaAlignment: center
comments: true
date: 2015-10-22 14:31:33
url:
tags:
- 爬虫
- Google Play
- 验证
categories:
- Other
photos:
---
在本地可以正常运行的Raccoon，部署到服务器时报异常：『com.akdeniz.googleplaycrawler.GooglePlayException: Error=NeedsBrowser』。
<!--more-->

异常大致如下：
```bash
Exception in thread "main" com.akdeniz.googleplaycrawler.GooglePlayException: Error=NeedsBrowser
Url=https://accounts.google.com/ContinueSignIn?sarp=1&scc=1&continue=https%3A%2F%2Faccounts.google.com%2Fo%2Fandroid%2Fauth%3Fhl%3Den_us%26xoauth_display_name%3DAndroid%2BLogin%2BService%26source%3DAndroid%2BLogin&plt=AKgnsbvpYOFQNYD02v2gawgW73Qa_H9ymB1OhI6mqifo1tfMndMaVWA6wyfuVSN94w4003Kc6lz9gJ51fjnAY97fXoMvEHTlV0AqVIwRpHNwcNMS50dFsO7e7dveFv-8HhJRvfDHHruJy7MJH-Gq808p3U_LANxN0g0irHHDcf3o4TjFQC1GHpg2abYrIkPYuea4qVhzHgN2gI7tduYeFF7VTSLhQ-SQIHWLmgMhnfrTGMBsgWUPN7w
ErrorDetail=To access your account, you must sign in on the web. Touch Next to start browser sign-in.
at com.akdeniz.googleplaycrawler.GooglePlayAPI.executeHttpRequest(GooglePlayAPI.java:522)
at com.akdeniz.googleplaycrawler.GooglePlayAPI.executePost(GooglePlayAPI.java:482)
at com.akdeniz.googleplaycrawler.GooglePlayAPI.executePost(GooglePlayAPI.java:462)
at com.akdeniz.googleplaycrawler.GooglePlayAPI.loginAC2DM(GooglePlayAPI.java:180)
...
```
Google了一下，说是Google处于安全考虑，在新设备上登录时做出了限制。

解决方案：https://accounts.google.com/b/0/DisplayUnlockCaptcha ，然后点击『继续/continue』即可。

*参考：https://github.com/Akdeniz/google-play-crawler/issues/79*
