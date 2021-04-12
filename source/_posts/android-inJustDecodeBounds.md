title: 对inJustDecodeBounds的理解
date: 2015-08-28 15:59:00
author: Jiezhi.G
email: Jiezhi.G@gmail.com
url: {{ url }}
tags:
- android
- BitmapFactory
categories:
- Android
premalink: android_edittext_no_key
thumbnailImage: http://qn-jiezhi.nospider.net/android10050791,2560,1600.jpg-thumb1
metaAlignment: center
photos:
comments: true
---

浅谈对BitmapFactory.Options.inJustDecodeBounds的理解。

<!--more-->

public boolean inJustDecodeBounds
> If set to true, the decoder will return null (no bitmap), but the out… fields will still be set, allowing the caller to query the bitmap without having to allocate the memory for its pixels.

可能英文水平的关系，看这个解释楞是没看出个所以然来，不过在看Android官方这篇[博客](http://developer.android.com/training/displaying-bitmaps/load-bitmap.html#load-bitmap) 有个解释倒是能领悟一二：

>Setting the **inJustDecodeBounds** property to true while decoding **avoids memory allocation**, returning null for the bitmap object but setting outWidth, outHeight and outMimeType. This technique allows you to read the dimensions and type of the image data prior to construction (and memory allocation) of the bitmap.

即：如果inJustDecoedBounds设置为true的话，解码bitmap时可以只返回其高、宽和Mime类型，而**不必为其申请内存，从而节省了内存空间**。

下面是官方给的code snippet:
```java
BitmapFactory.Options options = new BitmapFactory.Options();
options.inJustDecodeBounds = true;
BitmapFactory.decodeResource(getResources(), R.id.myimage, options);
int imageHeight = options.outHeight;
int imageWidth = options.outWidth;
String imageType = options.outMimeType;
```
