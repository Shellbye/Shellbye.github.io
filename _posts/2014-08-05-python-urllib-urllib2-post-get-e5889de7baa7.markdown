---
author: shellbye
comments: true
date: 2014-08-05 08:22:38+00:00
layout: post
slug: python-urllib-urllib2-post-get-%e5%88%9d%e7%ba%a7
title: python urllib urllib2 post get 初级
wordpress_id: 995
categories:
- tech_world
tags:
- Python
- urllib2
---

一直对urllib和urllib2是怎么区分post方法与get方法搞的不是很懂，今天参考[这篇博客](http://buddylindsey.com/basic-urllib-get-and-post-with-and-without-data/)自己写了点测试代码试了一下，原来这么简单。

我是django在自己本地建了一个简单的测试网址，django的设置和url啥的就不多说了，为了简单起见，我的后台的函数是这样的：

{% highlight python %}
from django.views.decorators.csrf import csrf_exempt
import json


@csrf_exempt
def test_get_post(request):
    ret = {
        'method': request.method,
        'get_params': request.GET,
        'post_params': request.POST,
    }
    return HttpResponse(json.dumps(ret))
{% endhighlight %}


然后测试的main方法如下：

{% highlight python %}
# -*- coding:utf-8 -*-
__author__ = 'shellbye.com@gmail.com'

import urllib
import urllib2


def this_is_a_post():
    data = urllib.urlencode({"key": "value"})
    post_request = urllib2.urlopen("http://127.0.0.1:8001/polls/", data)
    print post_request.read()


def this_is_a_get():
    get_without_data = urllib2.urlopen("http://127.0.0.1:8001/polls/")
    print get_without_data.read()

    get_with_data = urllib2.urlopen("http://127.0.0.1:8001/polls/?%s" % urllib.urlencode({"key": "value"}))
    print get_with_data.read()


def get_post():
    get_post_data = urllib2.urlopen("http://127.0.0.1:8001/polls/?%s" % urllib.urlencode({"key": "value"}),
                                    urllib.urlencode({"key": "value"}))
    print get_post_data.read()


if __name__ == "__main__":
    this_is_a_get()
    print "============================================================================"
    this_is_a_post()
    print "============================================================================"
    get_post()
{% endhighlight %}


函数的打印结果如下：


    {"post_params": {}, "method": "GET", "get_params": {}}
    {"post_params": {}, "method": "GET", "get_params": {"key": "value"}}
    ============================================================================
    {"post_params": {"key": "value"}, "method": "POST", "get_params": {}}
    ============================================================================
    {"post_params": {"key": "value"}, "method": "POST", "get_params": {"key": "value"}}



可以看出，其实这个urlopen是完全看有没有第二个参数来判断是不是post的，因为post的参数不是像get一样来自url。从第三个函数看的出来在使用post方法时，仍然可以同时用get方法。
