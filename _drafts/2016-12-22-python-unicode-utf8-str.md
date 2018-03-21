---
author: shellbye
comments: true
date: 2016-12-22 00:00:08+00:00
layout: post
slug: python_unicode_utf8_str
title: Python unicode 字符编码问题汇总
categories:
- tech_world
tags:
- python
- unicode
---

# 基本概念
相信中国的程序员对编码问题的头疼已经不是一天两天了，
我自己也遇到过好多次，只是每次遇到都是“临阵磨枪”，临时Google，
下次遇到再来一遍，几乎每个项目都会遇上那么几次，
所以实在是烦不胜烦，决定写一篇博客，好好整理一下思路。

世界上最早的编码也是大家最熟悉的编码就是1963年发布的[ASCII]
（American Standard Code for Information Interchange）了，
ASCII码只用7位编码了128个字符，其中33个不可打印的字符和95个可打印的字符。

128个字符显然是不够的，于是不同的厂商开始自行添加剩余字符，局面比较混乱，
最后，为了统一字符编码产生了`unicode`。

一个字符是文本的最小单元，如`a`，`b`，`c`等，
`unicode`是通过一个整数（`code point`）来表征一个字符的，一般用16进制来表示。
那么，如果你拿到一个整数（`code point`），想要获得这个代码代表的`unicode`是啥怎么做呢？
答案是`Python2.X`使用[unichr],`Python3.X`使用[chr].

{% highlight python %}

>>> print unichr(0x12ca)  # Python2
ዊ
>>> print chr(0x12ca)  # Python3
ዊ

{% endhighlight %}

虽然字符是用整数（`code point`）表示的，但是在屏幕和纸上展示的时候，却是根据不同的语言，
用不同的符号显示的，比如上面的`unicode`字符`0x12ca`，在屏幕上最终展示就是`ዊ`。

把`unicode`转化为字符过程，就叫做编码，即`0x12ca`到`ዊ`。

那么有了上面的解释，最简单的编码方式就是把所有字符一一对应的编码就可以了，
比如`Python`的如下编码：

{% highlight text %}

   P           y           t           h           o           n
0x50 00 00 00 79 00 00 00 74 00 00 00 68 00 00 00 6f 00 00 00 6e 00 00 00
   0  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 21 22 23

{% endhighlight %}

但是，从上面我们可以看到，上面的串中有大量的`0`，这是不够高效的，
而且因为不同的处理器因为大端小端的问题处理起来也是问题多多，
所以在实际的情况中，这种编码方式并没有真正利用起来，
真正利用起来的是`utf-8`编码方式。

Python默认的编码方式是`ASCII`，所以在将`unicode`转为`ASCII`时，
如果整数（`code point`）超过了128，那么，
就会遇到万恶的`[UnicodeEncodeError]`，

`utf-8`（Unicode Transformation Format，
8表示这种编码使用了8个bit）是比较流行的一个编码方式，
`utf-8`使用如下的方式来进行编码：

{% highlight text %}

1. 如果`code point` <128, 那么就直接展示
2. 如果`code point`位于 128 和 `0x7ff`之间,就用两个`byte`来表示，
每个`byte`都在128到255之间.
3. 如果`code point` >0x7ff，就用三个或者四个`byte`表示， 每个`byte`都在128到255之间.

{% endhighlight %}

# python2.X
`unicode`字符串在python中是`unicode`类型的，可以用`isinstance(value, unicode)`
来查看一个字符串是否是`unicode`。内建函数[`unicode()`]可以把一个对象转为`unicode`字符串，
其方法签名如下：

{% highlight python %}

unicode(object[, encoding[, errors]])

{% endhighlight %}

如果参数`encoding`没有指定，则默认使用`ASCII`编码，

{% highlight python %}

>>> unicode("哈哈")
Traceback (most recent call last):
  File "<input>", line 1, in <module>
UnicodeDecodeError: 'ascii' codec can't decode byte 0xe5 in position 0:
ordinal not in range(128)
>>> unicode("哈哈", encoding='utf-8')
u'\u54c8\u54c8'

{% endhighlight %}

这里有一个有意思的现象，就是当你`[decode]`一个`unicode`对象时，
会产生`UnicodeEncodeError`：

{% highlight python %}

>>> a = unicode("哈哈", encoding='utf-8')
>>> a.decode('utf-8')
Traceback (most recent call last):
  File "<input>", line 1, in <module>
  File "/path/to/python2.7/encodings/utf_8.py", line 16, in decode
    return codecs.utf_8_decode(input, errors, True)
UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-1:
ordinal not in range(128)

{% endhighlight %}

这就奇怪了，明明我在`decode`，为什么产生了一个`UnicodeEncodeError`呢？
这个问题是因为[decode]在这里进行了一次[down-convert]:

> Paradoxically, a UnicodeEncodeError may happen when decoding.
The cause of it seems to be the coding-specific decode() functions that
normally expect a parameter of type str.
It appears that on seeing a unicode parameter,
the decode() functions "down-convert" it into str,
then decode the result assuming it to be of their own coding.
It also appears that the "down-conversion" is performed using the ASCII encoder.
Hence an encoding failure inside a decoder.

正是由于这个自作主张的[convert]，引发了上面的奇怪的bug。

在Python关于编码的[官方文档]中，有以下一个例子：

{% highlight python %}

>>> unicode('abcdef' + chr(255))    
Traceback (most recent call last):
...
UnicodeDecodeError: 'ascii' codec can't decode byte 0xff in position 6:
ordinal not in range(128)

{% endhighlight %}

文档中只给出了错误的例子，但是并没有提怎么样才是对的，
这是就要看看这个[版本的`chr`]了，

{% highlight text %}

Return a string of one character whose ASCII code is the integer i.
For example, chr(97) returns the string 'a'. This is the inverse of ord().
The argument must be in the range [0..255], inclusive;
ValueError will be raised if i is outside that range. See also unichr().

{% endhighlight %}

是不是其实并没有解释什么问题？是的，因为它还是说可以输入0到255的参数，
但是它没有说明白的是，你可以输入0到255，但是只有0到127才是有意义的，
其他大于127的值都是返回的不确定的东东，因为[ASCII]只到127，那么，
怎么样用正确的方式把`code point`为255的字符和`abc`拼接起来呢？
答案是指定编码：

{% highlight python %}

>>> unicode('abcdef' + chr(255), encoding="latin1")
abcdefÿ

{% endhighlight %}

或者你可以直接在内部就使用[unichr]而不是[chr]:

{% highlight python %}

>>> unicode('abcdef' + unichr(255))
abcdefÿ

{% endhighlight %}




[ASCII]:https://en.wikipedia.org/wiki/ASCII
[unichr]:https://docs.python.org/2/library/functions.html#unichr
[chr]:https://docs.python.org/3/library/functions.html#chr
[UnicodeEncodeError]:https://docs.python.org/2/library/exceptions.html#exceptions.UnicodeEncodeError
[unicode()]:https://docs.python.org/2/library/functions.html#unicode
[decode]:https://docs.python.org/2/library/codecs.html#codecs.decode
[down-convert]:https://wiki.python.org/moin/UnicodeEncodeError
[convert]:http://stackoverflow.com/questions/5096776/unicode-decodeutf-8-ignore-raising-unicodeencodeerror/5097106#5097106
[官方文档]:https://docs.python.org/2/howto/unicode.html#the-unicode-type
[版本的`chr`]:https://docs.python.org/2/library/functions.html#chr
