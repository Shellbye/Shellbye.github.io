---
author: shellbye
comments: true
date: 2014-07-08 03:52:20+00:00
layout: post
slug: tomcat-spring-mimemessagehelper-encode
title: tomcat + spring MimeMessageHelper encode
wordpress_id: 957
categories:
- 技术世界
tags:
- java
- spring
- tomcat
- 编码
---

中国的程序员在写代码时，永远都会受到乱码的困扰，什么时候才能不在编码的问题的上浪费大量时间啊！

Spring框架中的MimeMessageHelper可以帮助发送各种各样格式的邮件，我在使用它发送html格式的邮件时，又一次遇到了乱码问题。

debug发现数据到达后台时已经变成了乱码，但是前端页面确实已经进行了utf-8的编码，于是想到可能tomcat的问题，可能是传到后台的过程中由于tomcat没有正确解析所以导致乱码，于是对tomcat中conf/server.xml做了如下修改：

{% highlight YAML %}



{% endhighlight %}


即添加了URIEncoding="UTF-8"属性。 再次debug，发现数据到达后台是正确的，但是进入MimeMessageHelper后发现出现乱码，问题出现在MimeMessageHelper中，我起初使用的MimeMessageHelper构造器是这样的： 


{% highlight java %}
MimeMessageHelper helper = new MimeMessageHelper(message, true);
{% endhighlight %}

不知道编码居然是GBK，去[官网](http://docs.spring.io/spring/docs/1.2.9/api/org/springframework/mail/javamail/MimeMessageHelper.html#MimeMessageHelper(javax.mail.internet.MimeMessage, boolean, java.lang.String))查了下，发现MimeMessageHelper有带有编码信息的构造器，于是把代码改成了：

{% highlight java %}
MimeMessageHelper helper = new MimeMessageHelper(message, true, "UTF-8");

{% endhighlight %}


Problem Solved!
