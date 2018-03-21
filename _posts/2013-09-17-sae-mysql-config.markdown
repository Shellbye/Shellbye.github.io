---
author: shellbye
comments: true
date: 2013-09-17 16:57:23+00:00
layout: post
slug: sae-mysql-config
title: 'SAE 数据库连接配置 '
categories:
- tech_world
tags:
- MySQL
---

新博客的第一篇文章就记录下配置SAE的数据库问题吧，感觉SAE在MYSQL的配置指导上有点坑人。
在应用的管理中的MySQL管理中，文档写到：


>用户名　 : SAE_MYSQL_USER  
密　　码 : SAE_MYSQL_PASS  
主库域名 : SAE_MYSQL_HOST_M  
从库域名 : SAE_MYSQL_HOST_S  
端　　口 : SAE_MYSQL_PORT  
数据库名 : SAE_MYSQL_DB  


这应该自然会让人以为你在自己的代码里可以这样写：
`define('DB_NAME', SAE_MYSQL_HOST_M);  
define('DB_USER', SAE_MYSQL_USER);  
define('DB_PASSWORD', SAE_MYSQL_PASS);  
define('DB_HOST', SAE_MYSQL_HOST_M .':' . SAE_MYSQL_PORT);`  
虽然感觉不是很对，因为毕竟密码等应该不是常量吧，但是想想也许后台会根据不同的用户进行转换和处理，但是试了好多次都不行。会报错说应用未授权如下：
`this app is not anthorised`
但是找了半天也没有找到去哪里才能找到授权，于是不得不去研究，然后看到如下段落：


>为了同学们使用方便,SAE提供了预定义常量,简化数据库的链接:  
define( 'SAE_MYSQL_HOST_M', 'w.rdc.sae.sina.com.cn' );//主库地址  
define( 'SAE_MYSQL_HOST_S', 'r.rdc.sae.sina.com.cn' );//从库地址  
define( 'SAE_MYSQL_PORT', 3307 );//数据库端口  
define( 'SAE_MYSQL_USER', SAE_ACCESSKEY );//数据库用户名  
define( 'SAE_MYSQL_PASS', SAE_SECRETKEY );//数据库密码  
define( 'SAE_MYSQL_DB', 'app_' . $_SERVER['HTTP_APPNAME'] );//数据库名  


看到ACCESSKEY和SECRETKEY我感觉有点眼熟，这几天研究SAE时总是在很显眼的地方看到这两个东西，然后便在“我的应用”里找到了它们，并把它们放到了定义它们的常量的地方，最后便成为了这个样子（假设ACCESSKEY为fwjo8ruq4ghfh,SECRETKEY为a3seifpoff84tpqgerhf834g23）：
{% highlight bash %}
define('DB_NAME', 'app_yourappname');
define('DB_USER', 'fwjo8ruq4ghfh');
define('DB_PASSWORD', 'a3seifpoff84tpqgerhf834g23');
define('DB_HOST', 'w.rdc.sae.sina.com.cn:3307');
{% endhighlight %}

然后刷新页面，终于看到了WordPress的Hello World页面！
