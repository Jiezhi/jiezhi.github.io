title: android中的ArrayMap和SparseArray
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: 'http://qn-jiezhi.nospider.net/blog/1.jpeg-thumb1'
metaAlignment: center
comments: true
date: 2017-03-30 21:02:58
url:
tags:
- android
categories:
- Android
photos:
---

为了提高Android应用程序的性能，Android系统提供了专门为移动开发而设计的集合。

<!--more-->

集合是软件开发中最常用的东西了。 一般来说，当需要将数据存储在键值对中时，我们想到的第一个数据结构就是HashMap。 它非常的灵活，因此它是存储键值对的数据结构的最优选择。

**因此，ArrayMap和SparseArray比使用HashMap有更高的内存效率。**

## HashMap原理
HashMap基本上是HashMap.Entry对象的一个数组。 Entry是HashMap的内部类，它用来保存键值对。


当键值对被插入到HashMap中时，会计算出该键的hashCode，并将该值分配给EntryClass的hashCode变量。 通过该hashCode，我们可以获取该键值对在存储池中的索引。 如果存储池中已经存在了键值对，则将插入新的键值对，并把最后一个键值对指向这个新的键值对，这使得存储池成为一个链接列表。


从数组中检索值的时间效率为恒定时间或O(1)。 这意味着不管数组的大小，拉取数组中的任何元素时间都相同。 这可以通过使用散列函数来生成指定键的数组索引。

HashMap用于将整数键映射到对象。

![](https://cdn-images-1.medium.com/max/800/1*lXkll8fb72OFk5NVVbi_wQ.png)

以下是创建HashMap和获取键和值的示例代码：
```Java
HashMap< String, String> map = new HashMap< String, String>();
map.put(“Key1”, "Value1");
map.put(“Key2”, " Value2");
map.put(“Key3”, " Value3");

Set set = hmap.entrySet();
Iterator iterator = set.iterator();

// Iterate over HashMap
while(iterator.hasNext()) {
    Map.Entry mEntry = (Map.Entry)iterator.next();
    String key = mEntry.getKey();
    String value = mEntry.getValue();
}
```

## ArrayMap原理
不像HashMap中包含一个数组，ArrayMap中包含了两个小数组。 第一个数组（Hash-Array）按顺序包含指定的哈希键。 第二个数组（Key Value Array）根据第一个数组存储对象的键和值。


当我们搜索其中的内容时，将会在Hash-Array上完成二分查找，通过找到的哈希索引，然后直接从第二个数组（Key Value Array）返回键值对。 如果第二个数组（Key Value Array）中的键不匹配，则通过第二个数组（Key Value Array）完成线性遍历来解决冲突。
![](https://cdn-images-1.medium.com/max/800/1*v1_3ug_tpscGtYc7JxP2Og.png)

以下是创建ArrayMap和获取键和值的示例代码：

```Java
ArrayMap<String, String> arrayMap = new ArrayMap<>();
arrayMap.put(“Key1”, “Value1”);
arrayMap.put(“Key2”, “Value2”);
arrayMap.put(“Key3”, “Value3”);

for (int i = 0; i < arrayMap.size(); i++) {
    String key = arrayMap.keyAt(i);
    String value = arrayMap.valueAt(i);
}
```

## SparseArray原理
与ArrayMap的主要区别在于，在SparseArray键中始终是原始类型。 在其他方面的操作原理是相似的。 当键是原始类型时，稀疏数组（Sparse arrays）可用于替换哈希映射（hash maps）。 SparseArray旨在消除自动装箱的问题（ArrayMap不能避免自动装箱问题），而这种方法会影响内存消耗。

以下是创建SparseArray的示例代码：
```Java
SparseArray sparseArray = new SparseArray();
sparseArray.put(1, “Value1”);

SparseLongArray sparseLongArray = new SparseLongArray();
sparseLongArray.put(1, 1L);

SparseBooleanArray sparseBooleanArray = new SparseBooleanArray();
sparseBooleanArray.put(1, true);

SparseIntArray sparseIntArray = new SparseIntArray();
sparseIntArray.put(1, 2);
```

该类在API 1中已经存在了，但在API 11中经过了重新设计。兼容库中的更新版本的SparseArrayCompat也可用于较旧设备。

还有几种其他类型的SparseArray：

*LongSparseArray，SparseIntArray，SparseBooleanArray*等
HashMap可以被以下的Array类替换：

![](https://cdn-images-1.medium.com/max/800/1*7FOi8twIY2LmDCpM-rOtwg.png)

## Comparison
内存的连续分配和回收以及垃圾收集将导致Android应用程序的滞后，从而降低了应用程序的性能。 除此之外，ArrayMap＆SparseArray通过使用2个小数组而不是一个大的数组来避免内存问题。

### 使用SparseArray比使用HashMap的好处是：
* 使用原语（primitives）带来更多的内存效率
* 没有自动装箱操作
* 自由分配内存

### 缺点:
* 对于大集合，速度较慢
* 仅适用于Android

一般来说，如果没有频繁的插入或删除操作，且大小是小于1000，那么ArrayMap / SparseArray类是非常好的选择。

来自Android开发者的视频将为您提供进一步的细节：
![](https://youtu.be/ORgucLTtTDI)

## 结论
我们可以得出结论，将整数映射到对象，SparseArray比使用HashMap的更有效。 理论是SparseArray可以比HashMap（<1000）更快地添加和检索元素，在这种情况下，减少了哈希函数处理时间。

> 原文地址：https://android.jlelse.eu/app-optimization-with-arraymap-sparsearray-in-android-c0b7de22541a
