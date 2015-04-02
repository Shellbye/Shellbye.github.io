---
author: shellbye
comments: true
date: 2015-04-02 11:27:08+00:00
layout: post
slug: java_socket_http_post_get
title: java socket http post get
categories:
- tech_world
tags:
- java
- http
-socket
---


今天参考着[这篇文章](https://www3.ntu.edu.sg/home/ehchua/programming/webprogramming/HTTP_Basics.html)深入的学习一下HTTP的基础知识，
并用JAVA SOCKET模拟了基本的GET和POST请求，GET的方法比较简单，文章中给出了，我会把我的修改版本附在文末，
本文主要是记录我再写POST请求时遇到的一些问题。

首先我在自己本机用自己学习servlet时的[代码](https://github.com/Shellbye/ServletHelloWorld)在8080端口运行着一个由Tomcat管理的网站，
这样一来我的测试才能全部在掌握之中。

关于POST请求，以下几点是需要特别注意的：

1.```Content-Type``` 和 ```Content-Length``` 是必须要有的请求头，否则无法正常获取POST参数

2.POST中的数据是要key和value分别URL编码之后才用等号（=）和取地址符（）去连接的，比如要下面的代码里的```maths=on``` ,
我起初的写法是：
{% highlight java %}
String data = URLEncoder.encode("maths=on", "UTF-8");
{% endhighlight %}
结果就是服务器端无法正常获取参数，后来参考[这篇文章](http://www.java2s.com/Tutorial/Java/0320__Network/SendingaPOSTRequestUsingaSocket.htm)的写法，
改成了下面这样才可以正常运行：
{% highlight java %}
String data = URLEncoder.encode("maths", "UTF-8") + "=" + URLEncoder.encode("on", "UTF-8");
{% endhighlight %}

到此为止就大功告成了，完整的代码如下：

{% highlight java %}
package com.network;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.Socket;
import java.net.URLEncoder;

/**
 * User: shellbye.com@gmail.com
 * Date: 2015/4/2
 */
public class HttpClient {
    public static void main(String[] args) throws Exception {
//        HttpGet();
        HttpPost();
    }

    public static void HttpPost() throws Exception{
        // The host and port to be connected.
        String host = "127.0.0.1";
        int port = 8080;
        // Create a TCP socket and connect to the host:port.
        Socket socket = new Socket(host, port);
        // Create the input and output streams for the network socket.
        BufferedReader in
                = new BufferedReader(
                new InputStreamReader(socket.getInputStream()));
        PrintWriter out
                = new PrintWriter(socket.getOutputStream(), true);
        // Send request to the HTTP server.
        out.println("POST /CheckBox HTTP/1.0");
        out.println("Content-Type: application/x-www-form-urlencoded");
        String data = URLEncoder.encode("maths", "UTF-8") + "=" + URLEncoder.encode("on", "UTF-8");
        out.println("Content-Length: " + data.length());

        out.println();   // blank line separating header & body

        out.println(data);
        out.flush();
        // Read the response and display on console.
        String line;
        // readLine() returns null if server close the network socket.
        while((line = in.readLine()) != null) {
            System.out.println(line);
        }
        // Close the I/O streams.
        in.close();
        out.close();
    }

    public static void HttpGet() throws Exception{
        // The host and port to be connected.
        String host = "127.0.0.1";
        int port = 8080;
        // Create a TCP socket and connect to the host:port.
        Socket socket = new Socket(host, port);
        // Create the input and output streams for the network socket.
        BufferedReader in
                = new BufferedReader(
                new InputStreamReader(socket.getInputStream()));
        PrintWriter out
                = new PrintWriter(socket.getOutputStream(), true);
        // Send request to the HTTP server.
//        out.println("GET /home.html HTTP/1.0");
        out.println("GET /HelloForm?first_name=shellbye&last_name=bai HTTP/1.0");

        // Check if the file have been changed or not
        // and this header works only for file not path
//        out.println("If-Modified-Since: Tue, 31 Mar 2015 13:27:32 GMT");

        out.println();   // blank line separating header & body

        out.flush();
        // Read the response and display on console.
        String line;
        // readLine() returns null if server close the network socket.
        while((line = in.readLine()) != null) {
            System.out.println(line);
        }
        // Close the I/O streams.
        in.close();
        out.close();
    }
}

{% endhighlight %}