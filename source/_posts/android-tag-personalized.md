title: android输出带上当前类名和方法名log
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: 'http://qn-jiezhi.nospider.net/android1119002570721cd2c8o.jpg-thumb1'
metaAlignment: center
coverImage: 'http://qn-jiezhi.nospider.net/android12736509,2560,1600.jpg'
coverCaption: Default
coverMeta: out
comments: true
date: 2015-09-25 15:13:23
url:
tags:
- Android
- Log
- Tips
categories:
- Android
photos:
---

这里介绍一下我自己在debug Android程序中使用log的Tips。如果出现问题，可以快速看到方法的调用堆栈，便于找到出错的敌方。
<!--more-->


```Java
import android.util.Log;

/**
 * Created by jiezhi on 9/25/15.
 * Function:
 */
public class LogUtil {
    private static final String TAG = "jiezhi:LogUtil";

    static boolean DEBUG = true;

    public static void d() {
        if (!DEBUG) {
            return;
        }
        StackTraceElement[] stacks = Thread.currentThread().getStackTrace();
        String file = stacks[3].getFileName();
        String fileName = file.substring(0, file.indexOf('.'));
        String methodName = stacks[3].getMethodName();
        Log.d(fileName, "------->" + methodName + "<-------");
    }
}
```

在方法中直接调用**LogUtil.d()**即可输出类名和方法名。

