---
author: shellbye
comments: true
date: 2014-12-16 07:16:54+00:00
layout: post
slug: nginx-uwsgi-504-gateway-time
title: nginx uwsgi 504 gateway time-out
wordpress_id: 1152
categories:
- tech_world
tags:
- Django
- Nginx
- uWsgi
---

使用nginx做反向代理，然后结合uwsgi部署django项目，因为django中要访问远程数据库的一些数据，所以比较耗时，会导致出现504错误，参考了[这个官方文档](http://nginx.org/en/docs/http/ngx_http_uwsgi_module.html#uwsgi_read_timeout)后，对nginx的设置进行了如下修改：

    
    uwsgi_read_timeout 180;



然后就大功告成了。
