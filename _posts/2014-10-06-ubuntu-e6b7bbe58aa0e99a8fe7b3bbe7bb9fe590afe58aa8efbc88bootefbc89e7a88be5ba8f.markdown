---
author: shellbye
comments: true
date: 2014-10-06 02:47:19+00:00
layout: post
slug: ubuntu-%e6%b7%bb%e5%8a%a0%e9%9a%8f%e7%b3%bb%e7%bb%9f%e5%90%af%e5%8a%a8%ef%bc%88boot%ef%bc%89%e7%a8%8b%e5%ba%8f
title: Ubuntu 添加随系统启动（boot）程序
wordpress_id: 1066
categories:
- 技术世界
tags:
- bash
- linux
- ubuntu
---

假设现在有个我们自己写的脚本叫custom.sh，我们想要它做一些系统启动(reboot)之后的一系列操作，那么怎么在系统启动之后自动的执行它呢？

在网上查了一些资料，都说直接在文件 /etc/rc.local 的 exit 0 之前添加要启动的程序即可。现在假设我的custom.sh在/root/mydir/里面，即/root/mydir/custom.sh。那么，是不是把 /etc/rc.local 改成如下这样就可以了呢？


    
    #!/bin/sh -e
    #
    # rc.local
    #
    # This script is executed at the end of each multiuser runlevel.
    # Make sure that the script will "exit 0" on success or any other
    # value on error.
    #
    # In order to enable or disable this script just change the execution
    # bits.
    #
    # By default this script does nothing.
    /root/mydir/custom.sh
    exit 0
    



一般情况下是的。但是由于我的custom.sh的特殊性，导致这样是无法启动程序的。那么最标准的做法是什么呢？最标准（也是对我的起作用的）就是把custom.sh放到/usr/bin/下面，如果custom里有很多用到当前目录里的东西怎么办呢？很简单，在写一个run.sh在里面cd并调用/root/mydir/custom.sh就可以了。记得，要把run.sh放到/usr/bin/下面
