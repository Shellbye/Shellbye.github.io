---
author: shellbye
comments: true
date: 2014-07-09 01:21:41+00:00
layout: post
slug: mysql-cant-connect-to-mysql-server-on-localhost-10061
title: 'MySQL _mysql_exceptions.OperationalError: (2003, "Can''t connect to MySQL
  server on ''localhost'' (10061)")'
wordpress_id: 961
categories:
- 技术世界
tags:
- MySQL
---

在根据[这个提问](http://stackoverflow.com/questions/372885/how-do-i-connect-to-a-mysql-database-in-python)尝试使用python连接MySQL时，使用如下连接配置：

    
    import MySQLdb
    
    db = MySQLdb.connect(host="localhost", user="root", passwd="", db="student")


但是在尝试连接的时候报错如下：

    
    _mysql_exceptions.OperationalError: (2003, "Can't connect to MySQL server on 'localhost' (10061)")


查阅了一些资料，有很多中解释，概况起来大概可以这样理解：

1.MySQL在Windows上把localhost解析为了IPv6的::1，参考[这篇博客](http://www.victor-ratajczyk.com/post/2012/02/25/mysql-fails-to-resolve-localhost-disable-ipv6-on-windows.aspx)。

2.[官网](http://dev.mysql.com/doc/refman/5.6/en/can-not-connect-to-server.html)的解释，大概是指localhost是UNIX系统使用的(亲测可用)，要想在Windows下使用，是[比较麻烦](http://dev.mysql.com/doc/refman/5.0/en/connecting.html)滴。

概况起来讲，就是在Windows上就不要用localhost了，反正它最后也要被解析为127.0.0.1，还不如直接用127.0.0.1来的方便。

所以，上面的代码改为这样就没有问题了：

    
    import MySQLdb
    
    db = MySQLdb.connect(host="127.0.0.1", user="root", passwd="", db="student")


其他参考资料：

[1].http://stackoverflow.com/questions/7382602/what-is-the-difference-between-127-0-0-1-and-localhost

[2].http://forum.wampserver.com/read.php?2,110383,110408

[3].http://mysql-python.sourceforge.net/MySQLdb.html
