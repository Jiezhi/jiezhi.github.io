title: python函数式编程
tags:
  - code
  - python
id: 53
categories:
  - Python 2.x
date: 2015-03-16 00:08:05
premalink: android_edittext_no_key
thumbnailImage: http://qn-jiezhi.nospider.net/10126010,2560,1600.jpg-thumb1
metaAlignment: center
coverImage: http://qn-jiezhi.nospider.net/10052206,2560,1600.jpg-cover
coverCaption: "Android"
coverMeta: out
photos:
comments: true
---

1.我们可以把一个函数直接作为参数传入另一个参数：

```python
import math

def add(x, y, f):
    return f(x) + f(y)

print add(25, 9, math.sqrt)</pre>
```
2.map()函数

**map()**是 Python 内置的高阶函数，它接收一个函数 f 和一个 list，并通过把函数 f 依次作用在 list 的每个元素上，得到一个新的 list 并返回。

```Python
def format_name(s):
    return s.capitalize()

print map(format_name, ['adam', 'LISA', 'barT'])</pre>
```
3.reduce()函数

**reduce()**函数也是Python内置的一个高阶函数。reduce()函数接收的参数和 map()类似，一个函数 f，一个list，但行为和 map()不同，reduce()传入的函数 f 必须接收两个参数，reduce()对list的每个元素反复调用函数f，并返回最终结果值。

```python
def prod(x, y):
    return x * y

print reduce(prod, [2, 4, 5, 7, 12])
```

4.filter()函数

**filter()**函数是 Python 内置的另一个有用的高阶函数，filter()函数接收一个函数 f 和一个list，这个函数 f 的作用是对每个元素进行判断，返回 True或 False，filter()根据判断结果自动过滤掉不符合条件的元素，返回由符合条件元素组成的新list。

利用filter()过滤出1~100（包括1和100）中平方根是整数的数

```python
import math

def is_sqr(x):
    i = int(math.sqrt(x))
    return i * i == x

print filter(is_sqr, range(1, 101))
```
以上资料来自慕课网中[《python进阶》](http://www.imooc.com/learn/317)课程。
