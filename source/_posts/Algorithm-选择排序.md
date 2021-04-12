title: Algorithm-选择排序
tags:
  - 排序
  - sort
  - 算法
  - 选择排序
id: 591
categories:
  - Algorithm
date: 2015-06-24 18:44:31
coverImage: 'http://qn-jiezhi.nospider.net/137487-106.jpg-thumb1'
coverCaption: "Default"
coverMeta: out
photos:
comments: true
---
C语言实现的选择排序。
<!--more-->
```c
#include <stdio.h>
 
void SelectionSort(int *a, int n);
 
int main()
{
    int a[6] = {3, 6, 4, 2, 1, 5};
    int i;
    for (i = 0; i < 6; i++)
        printf("a[%d]=%d ", i, a[i]);
    printf("\n");
    SelectionSort(a, 6);
    for (i = 0; i < 6; i++)
        printf("a[%d]=%d ", i, a[i]);
    printf("\n");
    return 0;
}
 
void SelectionSort(int *a, int n)
{
    int i, j;
    int tmp;
    int t;
    for (i = 0; i < n - 1; i++)
    {
        tmp = i;
        for (j = i + 1; j < n; j++)
        {
            if (a[j] < a[tmp])
                tmp = j;
        }
        t = a[tmp];
        a[tmp] = a[i];
        a[i] = t;
    }
}
```
