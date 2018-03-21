---
author: shellbye
comments: true
date: 2014-08-25 07:11:24+00:00
layout: post
slug: putty-%e4%b8%ad%e6%97%a0%e6%b3%95%e4%bd%bf%e7%94%a8-byubo-%e5%8a%9f%e8%83%bd%e9%94%ae
title: putty 中无法使用 byubo 功能键
wordpress_id: 1013
categories:
- tech_world
---

参考文章：[http://choorucode.com/2013/01/21/byobu-function-keys-do-not-work-in-putty/ ](http://choorucode.com/2013/01/21/byobu-function-keys-do-not-work-in-putty/)

我试了一下，原文的Clean solution在我这里行不通，但是Dirtier solution却可以，设置过程如下：

载入（右侧load）session，进入Terminal-->Keyboard-->The Function keys and keypad，勾选Xterm R6 即可。

Putty Release 0.63亲测可用
