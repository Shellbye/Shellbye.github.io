---
author: shellbye
comments: true
date: 2014-12-15 09:17:10+00:00
layout: post
slug: mysql-forget-root-password
title: MySQL forget root password
wordpress_id: 1149
categories:
- 技术世界
tags:
- linux
- MySQL
- ubuntu
---

也许并不是所有人都像我一样会忘记MySQL的root密码，但是作为已经忘记不止一次的人，我觉得有必要记录下如何重置，记住了，只能重置，找回是没有希望的了。

以下操作最好统一使用root权限进行，因为大家可能不一定都用root登录，所以我统一都会加sudo。

1.关闭MySQL服务：

{% highlight YAML %}
sudo service mysql stop
{% endhighlight %}


2.确保关闭了所有的MySQL服务

{% highlight YAML %}
sudo killall mysql
{% endhighlight %}


3.用安全模式开启MySQL，此时可以不用密码登录MySQL

{% highlight YAML %}
sudo mysqld_safe --safe-grant-tables
{% endhighlight %}


4.安全模式下用root身份登录MySQL

{% highlight YAML %}
sudo mysql -u root -p
{% endhighlight %}


5.重置密码

{% highlight YAML %}
mysql> use mysql;
{% endhighlight %}


{% highlight YAML %}
mysql> UPDATE user SET password = PASSWORD("new_password") WHERE User = 'root';
{% endhighlight %}


6.关闭安全模式

{% highlight YAML %}
sudo killall mysqld_safe
{% endhighlight %}


7.重启MySQL

{% highlight YAML %}
sudo service mysql start
{% endhighlight %}


All Done!

参考：

[1].[http://newexception.com/recover-mysql-root-password](http://newexception.com/recover-mysql-root-password)
