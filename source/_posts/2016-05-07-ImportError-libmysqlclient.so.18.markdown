---
author: shellbye
comments: true
date: 2016-05-07 10:27:08+00:00
layout: post
slug: ImportError－libmysqlclient－so－18－cannot－open－shared－object－file－No－such－file－or－directory
title: python mysqldb的一个importerror
categories:
- tech_world
tags:
- spark
- docker
---

这次的这个问题有点离奇，程序前几天还正常运行，也正常演示了，
准备收拾行李回学校参加论文答辩前一天晚上，
最后运行了一个读取mysql的python脚本，居然发生如下报错信息：

ImportError: libmysqlclient.so.18: cannot open shared object file: No such file or directory

没有新安装啥软件，也没有任何其他相关操作，
服务器只是作为爬虫静静的爬了一晚上的数据，第二天就冒出个这么个错误，
而且是在我准备回学校前一天，简直是醉了，折腾了好久，都没解决，
于是根据历史经验“睡一觉第二天就解决了”，我就回去了，在当天晚上飞机的情况下，
来公司，居然真给解决了，当然，不同的情况解决方法可能不太一样，
我在网上找资料时，主要有以下几种解决方案，我都罗列下，说不定有帮助。

###1.设置LD_LIBRARY_PATH  
这可能是比较简单的一种情况，也是[MySQL]{:target="_blank"}官方有记录的一种解决方案，
具体做法就是设置LD_LIBRARY_PATH，将其设置为mysql的库

{% highlight bash %}
# 注意因为我不是这种情况，所以不保证管用，只是有这么一些解决方法
export LD_LIBRARY_PATH=/usr/lib/mysql
export LD_LIBRARY_PATH=/usr/lib64/mysql #64bit

{% endhighlight %}

###2.使用ldd命令  
这是我没用过的命令，不过有资料里确实是提到了这个命令解决相关问题的场景，
所有有同样的问题的人可以试试，比如[SO]{:target="_blank"}.

###3.使用链接  
这个方法大部分都是中文资料里提到的，就是先找到具体的文件位置，
比如本文中的`libmysqlclient.so.18`,

{% highlight bash %}
# 注意因为我不是这种情况，所以不保证管用，只是有这么一些解决方法
find / -name libmysqlclient.so.18

{% endhighlight %}

然后在将找到的文件链接到其他的库里，具体可以参考[这篇文章]{:target="_blank"}和[这篇] {:target="_blank"} 

###4.我的方法  
如果你没有找到文件，那么上面的所有方法都没用，因为你的服务器上根本没有，
这个时候就要考虑去下载一个了，因为我用的centos（爱不起来），我就先用这个命令搜了下哪里有需要的文件：

{% highlight bash %}
# 解决了我的问题

yum whatprovides */libmysqlclient.so.18

{% endhighlight %}

然后从输出的列表里，找了一个包安装，问题解决了

{% highlight bash %}
# 别的其他包应该也可以，我只是随便选了包含libmysqlclient.so.18的

yum install mariadb-libs-5.5.44-2.el7.centos.x86_64

{% endhighlight %}

[MySQL]:http://mysql-python.sourceforge.net/FAQ.html#importerror
[SO]:http://stackoverflow.com/questions/511011/how-can-i-tell-python-which-version-of-libmysqlclient-so-to-use
[这篇文章]:http://www.qttc.net/201404430.html
[这篇]:http://hzcsky.blog.51cto.com/1560073/891002
