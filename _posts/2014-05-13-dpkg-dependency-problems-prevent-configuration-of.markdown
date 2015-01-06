---
author: shellbye
comments: true
date: 2014-05-13 13:43:27+00:00
layout: post
slug: dpkg-dependency-problems-prevent-configuration-of
title: 'ubuntu dpkg: dependency problems prevent configuration of *'
wordpress_id: 889
categories:
- 技术世界
tags:
- apt-get
- ubuntu
---

在Ubuntu下安装Java的时候，先是按照[这个文档](https://www.digitalocean.com/community/articles/how-to-install-java-on-ubuntu-with-apt-get)使用命令行安装，在网络情况比较好的情况下，这个是一个比较简单省事的安装方法，但是无奈我当时网络极差，所以听从师兄的建议，果断用ctr+C结束了当前的“sudo apt-get install oracle-java7-installer”的安装，转而自己去官网下载jdk，然后参考[这个文档](http://www.wikihow.com/Install-Oracle-Java-JDK-on-Ubuntu-Linux)手动安装java，一切都看似进行的很顺利，java成功的运行了起来，但是之后再次使用万能的apt-get来安装东西的时候（我是在安装maven时遇到的问题），就遇到了“dpkg: dependency problems prevent configuration of”这个问题，网上找个各种资料，尝试了各种命令（当然是在没有了解这些命令具体是干什么的情况下），包括但不仅限于：

    
    dpkg --configure -a 


之所以列出这个命令，是因为它的出境率比较高，而且貌似也解决了不少人的问题，但是没有解决我的，真正解决我的apt-get问题的，还是apt-get本身，即下面这条命令：

    
    sudo apt-get remove xxxx yyyyy zzzzz


当然，这里的xxxx，yyyy与zzzzz就是具体的出问题的包了。

问题虽然解决了，但是目前还没了解到底是什么原因，猜测是因为我ctrl+c停止apt-get时，xxxx，yyyy和zzzz等包被错误的停止掉了，于是再次使用apt-get时就出了错，所以remove掉他们就似乎是合理的一种做法了。

update: 2014-05-14

在看了[这篇文档](https://help.ubuntu.com/community/AptGet/Howto)后，感觉这个命令也许也会起作用：

    
    apt-get -f install
