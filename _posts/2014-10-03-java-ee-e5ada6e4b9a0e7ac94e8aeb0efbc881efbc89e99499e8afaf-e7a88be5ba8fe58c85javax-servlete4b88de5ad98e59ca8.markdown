---
author: shellbye
comments: true
date: 2014-10-03 07:53:16+00:00
layout: post
slug: java-ee-%e5%ad%a6%e4%b9%a0%e7%ac%94%e8%ae%b0%ef%bc%881%ef%bc%89%e9%94%99%e8%af%af-%e7%a8%8b%e5%ba%8f%e5%8c%85javax-servlet%e4%b8%8d%e5%ad%98%e5%9c%a8
title: 'JAVA EE 学习笔记（2）错误: 程序包javax.servlet不存在'
wordpress_id: 1062
categories:
- tech_world
---

出现这个错误的原因是编译器找不到servlet-api.jar，为什么呢？因为我下载的是JAVA SE的JDK，而servlet-api.jar是不在SE中的，而是在EE中。但是基本上类似tomcat这样的servlet container都包含有servlet-api.jar（\tomcat\lib）。按照网上的说法，我先把servlet-api.jar的路径放到了CLASSPATH里面，但是不起作用。于是我尝试着把servlet-api.jar拷贝一份放到了JDK/jre/lib/ext里面，然后就可以了。

参考文章：

1.[http://blog.163.com/gis_warrior/blog/static/1936171732012811071642/](http://blog.163.com/gis_warrior/blog/static/1936171732012811071642/)
