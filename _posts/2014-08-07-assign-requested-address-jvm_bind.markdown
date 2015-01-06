---
author: shellbye
comments: true
date: 2014-08-07 03:44:15+00:00
layout: post
slug: assign-requested-address-jvm_bind
title: 'Cannot assign requested address: JVM_Bind'
wordpress_id: 1001
categories:
- 技术世界
tags:
- java
- tomcat
---

启动Tomcat时有可能回遇到如题的错误，可能的原因有以下三种：

1.端口占用
网络上大部分解决方案都说是端口占用，解决方案就是找到占用端口的进程，然后把它杀掉：


    
    netstat -a -p tcp -o


2.多次启动
这个可能不是很常见，但是我也遇到过，可能是之前的没有关闭就再次启动了，也可能是关闭出错。解决方法就是在任务管理器里找到javaw.exe把它关掉就可以了。
3.地址解析问题
这个也比较常见，尤其是国内因为网络限制较多，所以很多人改了C:\Windows\System32\drivers\etc\hosts文件，导致localhost被解析到了不是127.0.0.1的位置，如下的hosts文件就会出错：

    
    127.0.0.1 localhost
    192.168.2.163 localhost


解决方案是去掉第二行就可以了。
