---
author: shellbye
comments: true
date: 2014-04-11 10:08:54+00:00
layout: post
slug: python-triple-operator
title: Python 三目运算  triple operator
wordpress_id: 852
categories:
- tech_world
tags:
- Python
---

Python中没有显示的类似C里的三目运算符:

    
    c = i == 0 ? 1 : -1;


但是Python可以利用and和or来完成相同的功能，比如上面的C代码，在Python中可以这样写：

    
    c = bool(i == 0) and 1 or -1



    
    [expression] and [on_true] or [on_false]


注意：这里写1（第二行里的on_true）的位置不能写包含假值（False）的表达式，否则将永久返回-1.

其中[bool](https://docs.python.org/2/library/functions.html#bool)是Python内置的函数，用来把括号里的东西转化一个布尔值。

[原理分析](http://www.diveintopython.net/power_of_introspection/and_or.html)：

Python中and与or的执行顺序都是由左至右，但是它们的返回顺序是不一样的。

1.and中，如果and前后的值都为真，则返回最后一个真的值，即最右边，比如如下表达式：

    
    1 and 2


将会返回2，同理，如下的表达式返回3：

    
    1 and 2 and 3


如果and前后有一个为假，则会返回第一个假的值，比如如下表达式，将会返回False：

    
    False and 2


然后如下的表达式会返回0：

    
    0 and False and 2


2.or则是不一样的处理，or在执行的时候，将会返回第一个为真的值，如果没有真值，则返回最后一个假值。比如：

    
    0 or False or 2


就返回2，而

    
    0 or False or []


则返回[].

这样就可以回过头来看Python版的三目运算了，在下面的表达式中，

    
    c = bool(i == 0) and 1 or -1


如果bool里的运算是真的，则bool(i == 0) and 1返回1，于是1在与-1进行or运算后返回的结果就是1.
