title: Algorithm-冒泡排序
tags:
  - 冒泡排序
  - 算法
  - sort
id: 589
categories:
  - Algorithm
date: 2015-06-24 12:43:22
photos:
comments: true
---
C语言实现冒泡排序。
<!--more-->
```c
#include

void BubbleSort(int *a, int n);

int main()
{
    int n = 6;
    int a[6] = {3, 2, 4, 6, 5, 1};
    for (int i = 0; i &lt; n; i++)
    {
        printf(&quot;a[%d]=%d ", i, a[i]);
    }
    printf(&quot;\n&quot;);
    BubbleSort(a, n);
    for (int i = 0; i &lt; n; i++)
    {
        printf(&quot;a[%d]=%d ", i, a[i]);
    }
    printf(&quot;\n&quot;);
    return 0;
}

void BubbleSort(int *a, int n)
{
    int i, j;
    int tmp;
    for (i = 0; i &lt; n - 1; i++)
    {
        for (j = 0; j &lt; n - i - 1; j++)
        {
            if (a[j] > a[j + 1])
            {
                tmp = a[j];
                a[j] = a[j + 1];
                a[j + 1] = tmp;
            }
        }
    }
}
```

	输出结果：

```bash
a[0]=3 a[1]=2 a[2]=4 a[3]=6 a[4]=5 a[5]=1
a[0]=1 a[1]=2 a[2]=3 a[3]=4 a[4]=5 a[5]=6
```
