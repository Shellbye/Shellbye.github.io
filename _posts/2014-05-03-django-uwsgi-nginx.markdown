---
author: shellbye
comments: true
date: 2014-05-03 01:59:37+00:00
layout: post
slug: django-uwsgi-nginx
title: Django uWSGI Nginx 配置笔记
wordpress_id: 874
categories:
- 技术世界
tags:
- Django
- Nginx
- uWsgi
---

在参考了[这篇](http://uwsgi-docs.readthedocs.org/en/latest/tutorials/Django_and_nginx.html)牛逼的Djano+uWSGI+Nginx的配置文档在配置过程用，遇到很多问题，下面是一些简单的总结。

1.socket file
在这篇文档讲到socket文件的[时候](http://uwsgi-docs.readthedocs.org/en/latest/tutorials/Django_and_nginx.html#using-unix-sockets-instead-of-ports)，他只是简单的说在mysite_nginx.conf配置文件里修改下面这行：

    
    server unix:///path/to/your/mysite/mysite.sock; # for a file socket
    # server 127.0.0.1:8001; # for a web port socket (we'll use this first)


当我看到这里的时候，我就在想需要往mysite.sock里写什么呢？这个.sock文件需要什么格式吗？花了不少时间，甚至在stackoverflow上[问了一个问题](http://stackoverflow.com/questions/23148082/in-django-nginx-wsgi-what-is-a-mysite-sock)，终于得到了一个可行的解答，那就是：这是一个什么都不需要的空文件。。。

那么，什么是socket file呢？Unix domain socket (IPC socket)是同一个操作系统内部不同进程之间用于交换数据的端点(endpoint)，其概念类似管道，具体的参考资料详见文章末尾。

2.mysite_uwsgi.ini里的home
因为我没有在虚拟的环境下部署，而是直接在操作系统内部部署，所以mysite_uwsgi.ini文件中的home这个参数就不知道该怎么写，尝试了一下设置，比如设置为我的django项目所在的路径，事实证明不需要，把它注释掉就可以了。

参考资料
1.http://en.wikipedia.org/wiki/Unix_domain_socket
2.http://en.wikipedia.org/wiki/Unix_file_types#Socket
3.http://www.rainydayz.org/beej/bgipc/unixsock.html
