---
author: shellbye
comments: true
date: 2014-07-29 06:42:03+00:00
layout: post
slug: httpclient-tostring-unavailable-suspended-threads
title: HttpClient-----[toString() unavailable - no suspended threads]
wordpress_id: 985
categories:
- tech_world
---

在使用HttpClient做模拟登陆时，当连续多次发送HttpGet或者HttpPost请求时，HttpClient就出问题了，具体问题不知道是啥，只是程序结束不了，查看HttpClient则显示[toString() unavailable - no suspended threads]。问题重现如下：

{% highlight java %}
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.HttpClients;

public class Main {

    public static void main(String[] args) throws Exception {
        HttpClient httpclient = HttpClients.createDefault();
        HttpGet httpGet = new HttpGet("http://shellbye.com");
        HttpResponse response = httpclient.execute(httpGet);
        System.out.println(response.toString());

        HttpGet httpGet2 = new HttpGet("http://shellbye.com");
        HttpResponse response2 = httpclient.execute(httpGet2);
        System.out.println(response2.toString());

        HttpGet httpGet3 = new HttpGet("http://shellbye.com");
        HttpResponse response3 = httpclient.execute(httpGet3);
        System.out.println(response3.toString());
    }
}
{% endhighlight %}

运行上面的函数会发现第三个请求httpGet3迟迟无法结束，此时在eclipse（或者MyEclipse）下用鼠标查看有时候（注意，有时候哦）会显示[toString() unavailable - no suspended threads]。

单步跟踪httpclient.execute，发现它首先进入的一个叫做CloseableHttpClient的类，既然它是Closeable,那么就把它close一下试试，于是发现关闭连接就会避免这个问题，即加上httpGet.releaseConnection();整个正确的程序如下：

{% highlight java %}
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.HttpClients;

public class Main {

    public static void main(String[] args) throws Exception {
        HttpClient httpclient = HttpClients.createDefault();
        HttpGet httpGet = new HttpGet("http://shellbye.com");
        HttpResponse response = httpclient.execute(httpGet);
        System.out.println(response.toString());
        httpGet.releaseConnection();

        HttpGet httpGet2 = new HttpGet("http://shellbye.com");
        HttpResponse response2 = httpclient.execute(httpGet2);
        System.out.println(response2.toString());
        httpGet2.releaseConnection();

        HttpGet httpGet3 = new HttpGet("http://shellbye.com");
        HttpResponse response3 = httpclient.execute(httpGet3);
        System.out.println(response3.toString());
        httpGet3.releaseConnection();
    }
}
{% endhighlight %}
