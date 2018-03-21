---
author: shellbye
comments: true
date: 2016-04-11 10:27:08+00:00
layout: post
slug: spark-network-wordcount-example-note
title: Spark network workcount
categories:
- tech_world
tags:
- spark
- docker
---

不得不先吐槽下spark的[官方文档]{:target="_blank"}，很多东西说的不是清楚，
也没有把一些容易出错的地方提前告知，让我这种入门级的菜鸟踩了不少坑，
要不是工作需要，按照我往常“试一试”的心态，说不定早就放弃掉了。

在[上一篇]{:target="_blank"}博客中，我记录了在Mac上结合Docker来使用Spark的一些笔记，
本文主要记录在具体使用Spark Streaming的过程中，在练习官方示例时遇到的一些问题及其解决方案。

官方文档在介绍Streaming时，用到了一个简单的[例子]{:target="_blank"},
在对代码进行了简单的讲解之后，官网的演示方法是这样描述的：

1.在一个中断中输入`nc -lk 9999`，这里是用来模拟9999端口的输入；  
2.然后在另一个终端中，输入`./bin/spark-submit examples/src/main/python/streaming/network_wordcount.py localhost 9999`，

到这里，就应该可以正常使用了，然后一切并没有那么简单，
首先，如果你像我一样，并没有严格按照上面的顺序操作，那么，当你先进行了操作2的时候，
你会迎来如下错误：

{% highlight bash %}
ERROR ReceiverTracker: Deregistered receiver for stream 0: Restarting receiver with delay 2000ms: Error connecting to localhost:9999 - java.net.ConnectException: Connection refused
{% endhighlight %}

这个“问题”我也在网上搜了好久，最后在[这篇博客]{:target="_blank"}里找到原因，那就是打开的顺序也很重要，
不过这个问题随着你按照操作1进行操作，那么这个问题也就不是问题了。

接下来的问题是，即使当你按照上面的顺序进行了所有操作之后，依然没法按照预期看到输出，而是：


{% highlight bash %}
WARN BlockManager: Block input-0-1460374276000 replicated to only 0 peer(s) instead of 1 peers
{% endhighlight %}

看到居然不是ERROR，顿时也算是轻松了许多，在万能的[SO]上还是轻松找到了[答案]{:target="_blank"}，
原来是因为在本地模式运行Spark Streaming时，如果使用了`local`或者`local[1]`这种模式，
那么就只有一个线程在运行了，所以就没有多余的线程来打印了！官方说法见[这里]。

折腾了好久，终于见到期待的输出了！如下：

![spark-stream-suc](/assets/spark-stream-suc.png)  



[官方文档]:http://spark.apache.org/docs/latest/streaming-programming-guide.html
[上一篇]:/blog/tech_world/spark-docker-mac/
[例子]:http://spark.apache.org/docs/latest/streaming-programming-guide.html#a-quick-example
[这篇博客]:http://bit1129.iteye.com/blog/2174751
[答案]:http://stackoverflow.com/questions/28050262/spark-streaming-network-wordcount-py-does-not-print-result
[这里]:http://spark.apache.org/docs/latest/streaming-programming-guide.html#points-to-remember-1
