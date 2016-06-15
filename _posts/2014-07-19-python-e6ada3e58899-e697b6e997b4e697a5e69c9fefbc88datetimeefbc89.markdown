---
author: shellbye
comments: true
date: 2014-07-19 04:42:53+00:00
layout: post
slug: python-%e6%ad%a3%e5%88%99-%e6%97%b6%e9%97%b4%e6%97%a5%e6%9c%9f%ef%bc%88datetime%ef%bc%89
title: Python 正则 时间日期（DateTime）
categories:
- tech_world
tags:
- Python
- 正则
---

正则表达式是我这种菜鸟不到万不得已绝对不会去碰的，但是，有些时候不用正则真的会很麻烦，两害相权取其轻，于是就稍稍研究了下正则，希望能够匹配诸如“2013-12-12 21:34:12”这样的字符串。

参考[这个文档](http://www.cnblogs.com/huxi/archive/2010/07/04/1771073.html)，最简单的正则是这么写：

1.0版本

{% highlight Python %}
pattern = re.compile(r"\d\d\d\d-\d\d \d\d:\d\d:\d\d")
{% endhighlight %}

其中，开头的r表示里面的所有字符都使用最原始意义，主要是指反斜杠“\”，具体参考[这里](http://stackoverflow.com/questions/2081640/what-exactly-do-u-and-r-string-flags-do-in-python-and-what-are-raw-string-l)。

当然，这样写有一点点low，虽然下面的也很low，但是比起上面的要好一点点：

2.0版本

{% highlight Python %}
pattern = re.compile(r"\d{4}(-\d\d){2} \d\d:\d\d:\d\d")
{% endhighlight %}

其中的{m}，表示前面的字符（或分组）匹配m次。注意到最后的时间其实是不太方便统一用{m}的方式来处理的，因为它有一个冒号（:），所以不得不把第一个拿出来，只对后面两个分组并重复匹配：

3.0版本

{% highlight Python %}
pattern = re.compile(r"^\d{4}(-\d\d){2} \d\d(:\d\d){2}")
{% endhighlight %}

关于分组（即圆括号内的东西）的东西，还没有搞清楚，但是我感觉所有的两个数字其实是可以分成一个组，然后不断的引用的，比如月份、日期、时、分和秒，等什么时候研究好了在更新吧。
