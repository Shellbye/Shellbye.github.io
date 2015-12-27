---
author: shellbye
comments: true
date: 2015-12-27 11:27:08+00:00
layout: post
slug: pure-js-implements-getJSON-with-header-X-Requested-With
title: Pure js implements getJSON with X-Requested-With header
categories:
- tech_world
tags:
- javascript
- js
- jquery
- http
---

用纯js实现jquery里面的getJSON方法在网上是有比较多的资料的，
比如在[You Might Not Need Jquery]中，就有如下实现：

{% highlight javascript %}
// jquery
$.getJSON('/my/url', function(data) {

});

// pure js
var request = new XMLHttpRequest();
request.open('GET', '/my/url', true);

request.onload = function() {
  if (this.status >= 200 && this.status < 400) {
    // Success!
    var data = JSON.parse(this.response);
  } else {
    // We reached our target server, but it returned an error

  }
};

request.onerror = function() {
  // There was a connection error of some sort
};

request.send();
{% endhighlight %}

但是我在用这段代码异步获取验证码图片时，其效果却和```getJSON```不一样，
用后者时是可以正常返回结果的，但是用纯js时，后台却返回了404，
对比两次的HTTP请求之后，发现用纯js方法时构造的HTTP请求比用```getJSON```少了一个Header，
就是```X-Requested-With```，这是一个很重要的非标准Header，
[Wiki]解释的比较清楚了，

> mainly used to identify Ajax requests. 
Most JavaScript frameworks send this field with value of ```XMLHttpRequest```

也就是说这个Header是用来标记请求是否为```Ajax```的，为什么要这样做呢？
为了防止跨站脚本攻击，也就是大名鼎鼎的```CSRF```，那么既然如此，需要怎么做呢？
很简单，就是在上面的代码```open```之后添加如下一行即可：

{% highlight javascript %}
request.setRequestHeader("X-Requested-With", "XMLHttpRequest");
{% endhighlight %}

[You Might Not Need Jquery]:http://youmightnotneedjquery.com/#json
[Wiki]:https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Common_non-standard_request_fields