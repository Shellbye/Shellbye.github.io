---
author: shellbye
comments: true
date: 2014-07-30 02:28:09+00:00
layout: post
slug: android-find-folder-tools-inside-sdk-pathtoyoursdk
title: Android Could not find folder 'tools' inside SDK '/path/to/your/sdk'.
wordpress_id: 988
categories:
- tech_world
---

因为在学Andoid的过程中需要用到Google Play Service 所以用SDK Manager更新了一些东西，结果发现SDK Tools版本更新的过高了，导致新建项目时Compile With一栏没有了可以选择的东西，于是又用SDK Manager把SDK Tools删掉了，这下糟了，重启eclipse之后连SDK Manager都打不开了，显示Could not find folder 'tools' inside SDK '/path/to/your/sdk'.如下图：

[![problem](http://www.shellbye.com/blog/wp-content/uploads/2014/07/problem-300x88.jpg)](http://www.shellbye.com/blog/wp-content/uploads/2014/07/problem.jpg)



解决方案是找到最原始的那个zip包，重新解压一份sdk文件夹里面的tools文件夹，然后把tools文件夹copy到已经安装好的sdk的里面，一切就OK了
