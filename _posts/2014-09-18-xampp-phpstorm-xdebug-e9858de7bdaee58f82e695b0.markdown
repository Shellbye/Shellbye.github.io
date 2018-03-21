---
author: shellbye
comments: true
date: 2014-09-18 09:00:30+00:00
layout: post
slug: xampp-phpstorm-xdebug-%e9%85%8d%e7%bd%ae%e5%8f%82%e6%95%b0
title: XAMPP PhpStorm Xdebug 配置参数
wordpress_id: 1033
categories:
- tech_world
tags:
- PHP
- xampp
---

折腾了好一会儿，终于可以让PhpStorm进入debug环境，看了不少教程，最后还是参考官网的说明文档自己搞定了。



1.安装xdebug

先运行

    php -i > a.txt

把当前php的版本信息等获取到，并将a.txt的内容全部复制到[xdebug的官网检测网页](http://xdebug.org/wizard.php)，官网会告诉你你需要安装的是哪个版本，以及具体的安装信息.

然后配置php.ini文件（位置：\xampp\php），在php.ini的最下面，按照[PhpStorm官网的教程](http://www.jetbrains.com/phpstorm/webhelp/configuring-xdebug.html)，注释掉相应的行（删除每行前面的英文分号';'）。



2.配置PhpStorm

这里需要说明的是，不需要新建什么PHP Webapplicant，这个和Pycharm不一样，PhpStorm不需要在IDE里启动服务器，直接使用XAMPP的Apache服务就可以了。

依次打开

    File-->Settings-->Php-->Debug

然后Debug port填写9000（默认值），并勾选

    Can accept external connections



然后再在

    Debug-->DBGP Proxy

里面，IDE key 填写PhpStorm，Host填写localhost，Port填写80.

保存退出

点击工具栏的“_Listen debugger connections_”按钮（类似电话，蓝色为监听状态，红色为关闭状态）开始监听debug请求。

3.浏览器配置

使用chrome，[下载插件xdebug](https://chrome.google.com/webstore/detail/xdebug-helper/eadndfjplgieldjbigjakmdgkmoaaaoc?hl=en)，然后在选项中配置xdebug，IDE key选择PhpStorm，然后把localhost添加到白名单中.

并点击地址栏的开始debug模式，大功告成。

4.参考链接：  
[1].http://confluence.jetbrains.com/display/PhpStorm/Zero-configuration+Web+Application+Debugging+with+Xdebug+and+PhpStorm
[2].http://www.jetbrains.com/phpstorm/webhelp/configuring-php-development-environment.html
[3].http://www.chenxuanyi.cn/xampp-phpstorm-xdebug.html
[4].http://www.zeali.net/entry/688
