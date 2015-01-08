---
author: shellbye
comments: true
date: 2014-05-15 09:01:49+00:00
layout: post
slug: ubuntu-java-awt-drawstring-chinese
title: ubuntu上JAVA中awt的drawString方法无法写入中文
wordpress_id: 894
categories:
- tech_world
tags:
- java
- ubuntu
---

在windows下写了[一个图片处理的类](http://www.shellbye.com/blog/%E6%8A%80%E6%9C%AF%E4%B8%96%E7%95%8C/java-graphics-fillrect/)，其中用到了awt这个包，在windows和自己的电脑的ubuntu上测试都是没有问题的，但是部署到阿里云服务器上之后中文就都出不来，所有的中文都变成了方块。

因为自己脑惨，导致在尝试解决问题的过程中每次测试的时候都相当于没有测试，等我意识到自己的测试方法不对并最终成功解决问题的时候，我也就不知道到底是哪个方法最终解决了问题，于是这里的记录就是按照先后顺序的几乎全部操作了。

1.首先猜想大概是缺一些包，所有按照[这篇文章](http://publib.boulder.ibm.com/infocenter/javasdk/v5r0/index.jsp?topic=%2Fcom.ibm.java.doc.user.lnx.50%2Fuser%2Flimitations.html)使用了如下命令安装包：

    
    sudo apt-get install ia32-libs


2.然后又按照[这篇文档](http://wiki.debian.org.hk/w/Make_Debian_support_Chinese_(eng)#Installing_Free_Chinese_Fonts)安装了一些中文字体，命令如下：

    
    apt-get install ttf-arphic-ukai ttf-arphic-uming ttf-arphic-gbsn00lp ttf-arphic-bkai00mp ttf-arphic-bsmi00lp


3.最后按照[这篇文章](http://ccode.diandian.com/post/2012-07-04/40030629720)（它本身参考[这篇文章](http://askubuntu.com/questions/149876/how-can-i-install-one-language-by-command-line)）安装了中文语言支持包：

先是使用如下命令查看有哪些需要安装的：

    
    check-language-support -l zh


我的输出是：

    
    language-pack-gnome-zh-hant language-pack-zh-hant


所以我使用如下命令安装了这两个包：

    
    sudo apt-get install language-pack-gnome-zh-hant language-pack-zh-hant


完成这步之后，原文说只能显示繁体，于是又使用如下命令查看并安装了相应包：

    
    check-language-support -l zh-hans



    
    sudo apt-get install fonts-arphic-uming language-pack-gnome-zh-hans language-pack-zh-hans ttf-arphic-ukai ttf-wqy-zenhei


4.整个过程中因为我以为是缺乏awt的东西导致的问题，所有先使用了如下命令查看了下ubuntu下有哪写awt包：

    
    apt-cache search awt


然后有选择性的安装了如下几个：

    
    sudo apt-get install libdrawtk-dev libdrawtk0 libdrawtk0-dbg
    


就是这些了
