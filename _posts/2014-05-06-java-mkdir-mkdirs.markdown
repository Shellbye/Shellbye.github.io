---
author: shellbye
comments: true
date: 2014-05-06 07:44:51+00:00
layout: post
slug: java-mkdir-mkdirs
title: JAVA mkdir() and mkdirs()
wordpress_id: 882
categories:
- tech_world
tags:
- java
---

[惨痛的历史](http://www.shellbye.com/blog/%E6%8A%80%E6%9C%AF%E4%B8%96%E7%95%8C/java-graphics-fillrect/)一次又一次的告诉我，写代码一定不要急，越晚开始写代码越好，写之前一定要多读读文档，多找找资料，磨刀不误砍柴工啊。

JAVA创建目录，随便Google一下到处是资料，几乎无一例外都是这样用：

{% highlight java%}
File file = new File("C:\\Directory1");
	if (!file.exists()) {
		if (file.mkdir()) {
			System.out.println("Directory is created!");
		} else {
			System.out.println("Failed to create directory!");
		}
	}
{% endhighlight %}

之前也用这样的方式成功地创建过目录，但是这次却无论如何无法再次成功创建，整了好久，实在没招，找来实验室一个师兄，最后发现mkdir()不能循环创建目录，如果你要创建比如"/dir1/dir11/dir111"而此时只有一个dir1存在，那么不好意思，mkdir是无法创建dir111的，因为什么呢？因为dir11不存在，这个怎么办？师兄说一个一个创建呗，这个不能忍啊，那代码岂不是很长了？我最讨厌长长的代码了，于是去Google了一下，在JAVA的[官方文档](http://docs.oracle.com/javase/7/docs/api/java/io/File.html#mkdir())里，就在mkdir的下面紧挨着就有一个mkdirs，我的天，看看mkdirs怎么写的：


>public boolean mkdirs()
Creates the directory named by this abstract pathname, including any necessary but nonexistent parent directories. Note that if this operation fails it may have succeeded in creating some of the necessary parent directories.

顿时觉得自己简直弱到不可理喻啊，以后一定要养成google完之后在仔仔细细查文档的好习惯！共勉！
