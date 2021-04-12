title: C语言实现交换
id: 586
categories:
- Algorithm
date: 2015-06-24 11:52:43
tags:
- C
- Algorithm

thumbnailImage: http://qn-jiezhi.nospider.net/13419422,2560,1600.jpg-thumb1
metaAlignment: center
coverImage: http://qn-jiezhi.nospider.net/10439898,2560,1600.jpg
coverMeta: out
photos:
comments: true
---

这里介绍两种方式，一种是通过宏定义的方式实现，一种是通过指针交换实现：
<!--more-->
```c++
#include <stdio.h>;

#define swap_m(x, y, t) ((t)=(x),(x)=(y),(y)=(t))

void swap(int *x, int *y);

int main()
{
    int a = 10;
    int b = 1;
    int temp;
    swap(&amp;a, &amp;b);
    printf(&quot;a=%d, b=%d\n&quot;, a, b);
    swap_m(a, b, temp);
    printf(&quot;a=%d, b=%d\n&quot;, a, b);
    return 0;
}

void swap(int *x, int *y)
{
    int tmp;
    tmp = *x;
    *x = *y;
    *y = tmp;
}
```
