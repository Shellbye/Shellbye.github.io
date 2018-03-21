---
author: shellbye
comments: true
date: 2014-12-15 09:17:10+00:00
layout: post
slug: mysql-forget-root-password
title: MySQL forget root password
wordpress_id: 1149
categories:
- tech_world
tags:
- linux
- MySQL
- ubuntu
---

也许并不是所有人都像我一样会忘记MySQL的root密码，但是作为已经忘记不止一次的人，我觉得有必要记录下如何重置，记住了，只能重置，找回是没有希望的了。

以下操作最好统一使用root权限进行，因为大家可能不一定都用root登录，所以我统一都会加sudo。

1.关闭MySQL服务：

    
    sudo service mysql stop



2.确保关闭了所有的MySQL服务

    
    sudo killall mysql



3.用安全模式开启MySQL，此时可以不用密码登录MySQL

    
    sudo mysqld_safe --safe-grant-tables



4.安全模式下用root身份登录MySQL

    
    sudo mysql -u root -p



5.重置密码

    
    mysql> use mysql;



    
    mysql> UPDATE user SET password = PASSWORD("new_password") WHERE User = 'root';



6.关闭安全模式

    
    sudo killall mysqld_safe



7.重启MySQL

    
    sudo service mysql start



All Done!

参考：

[1].[http://newexception.com/recover-mysql-root-password](http://newexception.com/recover-mysql-root-password)
