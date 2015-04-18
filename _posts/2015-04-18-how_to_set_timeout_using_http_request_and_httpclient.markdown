---
author: shellbye
comments: true
date: 2015-04-18 11:27:08+00:00
layout: post
slug: how_to_set_timeout_using_http_request_and_httpclient
title: How to set timeout using HttpRequest and httpclient
categories:
- tech_world
tags:
- java
- http
- socket
---

最近一直在用爬虫抓取智联招聘的简历信息，虽然智联招聘有验证码等诸多防御措施，
但是还是阻挡不了我的魔爪啊，嘿嘿。

这次是在我基本完成所有代码之后，开始大范围测试时遇到的一个问题，
就是Http请求超时，这样说这个问题其实不对，因为我的问题不是超时怎么了，
而是程序不知道超时了，一直在那里等啊等的，殊不知它辛辛苦苦发送的包，
可能就这样被智联招聘的服务器轻易的丢弃了（这个场景好熟悉？）。

所以需要告诉程序，当这个请求过了一定时间段还没有任何返回，
就放弃它！（是的，放弃是真正的勇气，也是真正的智慧）

放弃也要讲究方式，
在万能的[stackoverflow.com](http://stackoverflow.com/questions/6764035/apache-httpclient-timeout#answer-29592232)的帮助下，
解决方法记录如下。


正常的socket程序可能是这个样子：


{% highlight java %}
package com.shellbye.zhilian.main;

import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpGet;

/**
 * User: shellbye.com@gmail.com
 * Date: 2015/4/18
 */
public class Test {
    public static void main(String[] args) throws Exception{
        HttpClient httpclient = HttpClientBuilder.create().build();
        HttpGet codePost = new HttpGet("http://shellbye.com");
        HttpResponse codeResponse = httpclient.execute(codePost);
        System.out.println(codeResponse);
    }
}
{% endhighlight %}

程序的输出如下：

    HttpResponseProxy{HTTP/1.1 200 OK [Server: ........

当然，如果你像我一样在写爬虫，服务器可能就像我上面描述的那样，
不会给你回复，但是上面的程序会傻乎乎的一直等，永远不会执行完毕。
所以改进版本的程序是这个样子的：

{% highlight java %}
package com.shellbye.zhilian.main;

import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.config.RequestConfig;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.HttpClientBuilder;

/**
 * User: shellbye.com@gmail.com
 * Date: 2015/4/18
 */
public class Test {
    public static void main(String[] args) throws Exception{
        HttpClient httpclient = HttpClientBuilder.create().build();
        HttpGet codePost = new HttpGet("http://shellbye.com");
        RequestConfig.Builder builder  = RequestConfig.custom();
        int timeout = 5;
        builder.setConnectionRequestTimeout(timeout * 1000);
        builder.setConnectTimeout(timeout * 1000);
        builder.setSocketTimeout(timeout * 1000);
        RequestConfig requestConfig = builder.build();
        codePost.setConfig(requestConfig);
        HttpResponse codeResponse = httpclient.execute(codePost);
        System.out.println(codeResponse);
    }
}
{% endhighlight %}

如上代码，除了使用默认配置外，你可以自己定制(custom)你的http请求。
上面的三个timeout，我的理解如下（不太确定前两个的差别）：

1.ConnectionRequestTimeout: 经历多久之后，这次请求被视为过期
2.ConnectTimeout: 等待与服务器建立连接的时间
3.SocketTimeout: 与服务器的连接建立了，等待服务器发送数据的时间

其他我能找到的一些参考在[这里](http://mail-archives.apache.org/mod_mbox/hc-httpclient-users/201310.mbox/%3C1382118427.15734.YahooMailNeo@web142804.mail.bf1.yahoo.com%3E)，
和[这里](http://stackoverflow.com/questions/20271017/connection-and-connection-request-timeout)

程序写好了，那么问题来了，怎么测试？
怎么样人工的模拟一个超时的请求呢？
直接请求一个超慢的网站？不一定永远管用哦，
还是在万能的[stackoverflow.com](http://stackoverflow.com/questions/100841/artificially-create-a-connection-timeout-error#answer-904609)上，
找到了一个优雅的解决方案，就是用一个服务路由的IP地址。
所以最终的测试代码就是把上面我的博客地址所在行改成：

{% highlight java %}
HttpGet codePost = new HttpGet("http://10.255.255.1");
{% endhighlight %}

然后你就会看到你“期待”的超时错误！

写完本文之后发现一篇看起来不错的文章，
在[这里](http://www.baeldung.com/httpclient-timeout)，有兴趣的同学可以看下，我没仔细看，不过感觉比较有用。