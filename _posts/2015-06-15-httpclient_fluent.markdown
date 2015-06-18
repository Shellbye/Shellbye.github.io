---
author: shellbye
comments: true
date: 2015-06-15 11:27:08+00:00
layout: post
slug: httpclient-fluent
title: HttpClient fluent 
categories:
- tech_world
tags:
- java
- httpclient
---

在看Apache官方的[httpclient教程](https://hc.apache.org/httpcomponents-client-4.3.x/quickstart.html)时，
发现在运行所给的例子时，Idea居然无法找到正确的引入Request的方式，折腾了半天才发现，
原来fluent这个需要单独引入的，也就是说你的```pom.xml```光是下面这样是不行滴：

{% highlight xml %}
<dependency>
	<groupId>org.apache.httpcomponents</groupId>
	<artifactId>httpclient</artifactId>
	<version>4.3.6</version>
</dependency>
{% endhighlight %}

光是有这个你只能使用如下的引入：

{% highlight java %}
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
{% endhighlight %}

可以看到这个```org.apache.http.client```是可以使用的，我在google上找到一些例子，
发现Request是在client下面的flunt里面，也就是需要这样引入：

{% highlight java %}
import org.apache.http.client.fluent.Request;
{% endhighlight %}

但是光是有上面的```pom.xml```是远远不够滴，事实上，你的pom.xml需要是这个样子，才能正常的引入：

{% highlight xml %}
<dependency>
	<groupId>org.apache.httpcomponents</groupId>
	<artifactId>httpclient</artifactId>
	<version>4.3.6</version>
</dependency>
<dependency>
	<groupId>org.apache.httpcomponents</groupId>
	<artifactId>fluent-hc</artifactId>
	<version>4.3.6</version>
</dependency>
{% endhighlight %}

这里需要注意，两个的版本号```version```必须是一致的，不然指不定会出什么错。

说了这么多，那个这个fluent是干什么用的呢？
参考[这个官方文档](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/fluent.html)
可知，这个fluent就是相当于一种快捷方式，提供一些常用的API的快捷访问，比如如果你要打印某网页，
原始的做法是这样滴：

{% highlight java %}
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;

import java.io.BufferedReader;
import java.io.InputStreamReader;

public class Main {
    public static void main(String[] args) throws Exception {
        CloseableHttpClient httpclient = HttpClients.createDefault();
        HttpGet httpGet = new HttpGet("http://shellbye.com");
        CloseableHttpResponse response1 = httpclient.execute(httpGet);
        StringBuilder result = new StringBuilder();
        try {
            BufferedReader rd = new BufferedReader(
                    new InputStreamReader(response1.getEntity().getContent()));
            String line;
            while ((line = rd.readLine()) != null) {
                result.append(line);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            response1.close();
        }
        System.out.println(result.toString());
    }
}
{% endhighlight %}

但是使用了fluent之后就可以简化为：

{% highlight java %}
import org.apache.http.client.fluent.Request;

public class Main {
    public static void main(String[] args) throws Exception {
        System.out.println(Request.Get("http://shellbye.com").execute().returnContent().toString());
    }
}
{% endhighlight %}

是不是代码简洁了很多？而且打印出来的结果也是按照标准的html样式格式化了的，
而不是像上面的那个一样全部代码都在一行，还有就是，内容居然是转码了的，
在ide里可以直接看到其中的中文内容，而不是像上面那样中文都是乱码。
真是优势多多。具体的其他用法，参考上面给出的官方文档即可。

