title: 使用Decorator设计Cache
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: 'http://qn-jiezhi.nospider.net/python-Python-logo-notext.svg.png-thumb1'
metaAlignment: center
coverCaption: Default
coverMeta: out
comments: true
date: 2016-04-20 15:53:35
url:
tags:
- python
- Decorator
- cache
categories:
- python
photos:
---
利用decorator实现cache
<!--more-->
如果你使用的是python 3.2+，则可以直接使用functools中的lru_cache。
当然也可以自己实现的了。

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
"""
Created on 4/20/16

@author: Jiezhi.G@gmail.com

My Blog: jiezhi.github.io

Reference: <Expert Python Programming> Chapter 2--Decorators_Proxy Page.53
"""
import time
import hashlib
import pickle

cache = {}


def is_obsolete(entry, duration):
    return time.time() - entry['time'] > duration


def compute_key(function, args, kw):
    key = pickle.dumps((function.func_name, args, kw))
    return hashlib.sha1(key).hexdigest()


def memoize(duration=10):
    def _memoize(function):
        def __memoize(*args, **kw):
            key = compute_key(function, args, kw)

            # do we have it already?
            if key in cache and not is_obsolete(cache[key], duration):
                print 'we got a winner'
                return cache[key]['value']

            # computing
            result = function(*args, **kw)

            # storing the result
            cache[key] = {'value': result, 'time': time.time()}
            return result

        return __memoize
    return _memoize


@memoize()
def very_very_very_complex_stuff(a, b):
    return a + b


if __name__ == '__main__':
    print very_very_very_complex_stuff(2, 2)
    print cache
    print very_very_very_complex_stuff(2, 2)

```

运行后可以看到结果：
```bash
4
{'dedfca39c250ca2047c5d66a13c5df2e9ac90181': {'value': 4, 'time': 1461155366.249486}}
we got a winner
4
```
