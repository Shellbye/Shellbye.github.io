---
author: shellbye
comments: true
date: 2014-08-30 03:07:39+00:00
layout: post
slug: '%e3%80%8apython-%e9%ab%98%e7%ba%a7%e7%bc%96%e7%a8%8b%e3%80%8b%e8%af%bb%e4%b9%a6%e7%ac%94%e8%ae%b0%ef%bc%881%ef%bc%89'
title: 《Python 高级编程》读书笔记（1）
wordpress_id: 1022
categories:
- tech_world
tags:
- Python
---

目前为止觉得书很不错，对得起书名的高级二字，确实接触到了不少入门教程不曾提到过的东西，今天要记录的是来自2.2.4 itertools模块的groupby. 首先展示书中的源码：

    >>>from itertools import groupby
    >>>def compress(data):
    ...     return ((len(list(group)), name)
    ...              for name, group in groupby(data))
    ...
    >>>def decompress(data):
    ...     return (car * size for size, car in data)
    ...
    >>>list(compress('get uuuuuuuuuuuuuuuuuup'))
    [(1, 'g'), (1, 'e'), (1, 't'), (1, ' '),
    (18, 'u'), (1, 'p')]
    >>>compressed = compress('get uuuuuuuuuuuuuuuuuup')
    >>>''.join(decompress(compressed))
    'get uuuuuuuuuuuuuuuuuup'


我觉得这个代片段的关键就在compress函数的return语句。下面就单独拆分下看这句：

    >>>for name, group in groupby(data): print name, group

输出结果如下：

    g <itertools._grouper object at 0xb61f0a2c>
    e <itertools._grouper object at 0xb61f08cc>
    t <itertools._grouper object at 0xb61f0a0c>
      <itertools._grouper object at 0xb61f0a2c>
    u <itertools._grouper object at 0xb61f08cc>
    p <itertools._grouper object at 0xb61f0a0c>

显然，name是比较好理解的，基本相当于key值。而这里的group的输出似乎就没什么提示作用了，然后，按照源代码的样子在我们的测试代码里添加东西：

    >>>for name, group in groupby(data): print name, list(group)

输入结果如下：

    g ['g']
    e ['e']
    t ['t']
      [' ']
    u ['u', 'u', 'u', 'u', 'u', 'u', 'u', 'u', 'u', 'u', 'u', 'u', 'u', 'u', 'u', 'u', 'u', 'u']
    p ['p']


看到这里就已经清晰了许多了，原来groupby把我们的输入分割并且把相同的字符放到了一起（一个grouper对象里面），于是，加上最后的len()方法之后，这一行语句做了什么也就清楚了：

    >>>for name, group in groupby(data): print name, len(list(group))

输入结果如下：

    g 1
    e 1
    t 1
      1
    u 18
    p 1

剩下的基本上都比较简单了。