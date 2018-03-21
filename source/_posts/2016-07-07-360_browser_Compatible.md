---
author: shellbye
comments: true
date: 2016-07-07 10:27:08+00:00
layout: post
slug: 360_browser_compatible
title: 360浏览器修改文档模式
categories:
- tech_world
tags:
- tech
---

不知道出于什么考虑，360浏览器的模式文档模式居然是IE5（或IE7），
这对小公司来说简直是灾难，好在这个默认的文档模式可以按照如下方式修改：

{% highlight html %}
<meta http-equiv="X-UA-Compatible" content="IE=edge">
{% endhighlight %}
这个解决方法来自[这篇博客](http://blog.csdn.net/dkmings/article/details/51647811),
原文提到了第二种解决方案，貌似也是360官方推荐的，然而在我这里不管用，我也就不贴出来了。
