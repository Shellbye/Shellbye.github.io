---
author: shellbye
comments: true
date: 2014-07-03 00:53:59+00:00
layout: post
slug: android-r-cannot-be-resolved
title: Android R cannot be resolved
wordpress_id: 947
categories:
- 技术世界
tags:
- Android
---

1.问题：

在按照[官方教程](https://developer.android.com/training/basics/firstapp/index.html?hl=cn)学习Android学习的时候，看到[这篇](https://developer.android.com/training/basics/actionbar/adding-buttons.html)时，程序就出了问题，最开始是新建的一些按钮无法在java文件中识别，原因是R文件并没有生成它们。

2.解决尝试

按照网上的建议，在eclipse中点击Project-clean了一下，结果这写彻底砸了，R文件也没有了，于是eclipse开始提示要我import Android.R, 去网上差了下，发现这样做是万万不可的，但是怎么样才能重新生存R文件呢？

3.问题所在：

试过了各种办法，很多资料都说是xml文件出错了，但是我左看友看没有看到哪里有xml文件的报错，最后在[这个问题](http://stackoverflow.com/questions/885009/r-cannot-be-resolved-android-error)的评论中找到了答案：原来真的是因为xml文件出错了，只是不是语法错误，而是xml文件中所引用的文件不存在。在上面提到的官方教程中，如果按照它的指示一步一步来，那么你会创建一个文件res/menu/main_activity_actions.xml，并且在其中有如下易行代码：

    
    android:icon="@drawable/ic_action_search"


就是这里，drawable里面并没有ic_action_search这个图片，所以导致这个xml文件出错，但是奇葩的地方是它并没有提示你，也没有出现熟悉的红线，而是悄悄的就这样错了。。。还好我当时在贴代码的时候就想过一下，这个搜索的图片哪里来的？后来我懒了一下，心想官方教程嘛，里面一定有，结果悲剧了一晚上，不能迷信权威哦。
