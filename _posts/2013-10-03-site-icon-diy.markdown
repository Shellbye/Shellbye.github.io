---
author: shellbye
comments: true
date: 2013-10-03 10:15:14+00:00
layout: post
slug: site-icon-diy
title: 网站图标LOGO制作
wordpress_id: 707
categories:
- 技术世界
tags:
- DIY
- ico
- site
---

由于我不懂PS，也没有学过美工技巧，所以我的网站lLOGO（icon）的制作过程可能会对于和我一样的菜鸟有些许小小的帮助。

一、找到图片

首先，找到你想要图标原型——很有可能不是你原创的（比如我的），那么一定要注意，必须要找到原作者或者拥有其版权的机构，获得相应的权利，具体是什么我就不是很清楚了，但是我觉得基于以下两点原因我们不得不这么做：



	
  * 1. 尊重原创。你想要作为网站LOGO的图片一定不是在互联网上随便找来的，它一定很漂亮，一定在某种程度上吸引到了你，那么我们可以认为这样的图片很有可能是原创作者精心制作的，那么，他的工作就应该获得尊重。

	
  * 2. 这是“你”的LOGO。很显然，你不会在自己的身份证上放上别人的照片，因为这代表了你本身。LOGO也一样，即使你无法自己原创一个，那么你也肯定希望你的找到并编辑过的这个一定是来源明确且合法的。


好了，以身作则，先给出我的LOGO的出处：[http://www.supercoloring.com/wp-content/original/2009_01/polar-bear-20-coloring-page.gif](http://www.supercoloring.com/wp-content/original/2009_01/polar-bear-20-coloring-page.gif)

二、将图片制作为带有透明通道

至于什么是透明通道，看以下两个图就知道了：

[caption id="attachment_708" align="alignnone" width="180"][![polar-bear-not-transparent](http://baijianguo-mystorage.stor.sinaapp.com/uploads/2013/10/polar-bear-20-coloring-page-300x207.png)](http://baijianguo-mystorage.stor.sinaapp.com/uploads/2013/10/polar-bear-20-coloring-page.png) polar-bear-not-transparent[/caption]

[caption id="attachment_709" align="alignnone" width="180"][![polar_bear_transparent](http://baijianguo-mystorage.stor.sinaapp.com/uploads/2013/10/oie_transparent-300x207.png)](http://baijianguo-mystorage.stor.sinaapp.com/uploads/2013/10/oie_transparent.png) polar_bear_transparent[/caption]

当然，这两幅图片都是放在红色的底布上的，但是没有透明通道的那副图片（上）用自己的白色背景覆盖了红色的底色，但是有透明通道的那副却显示出的其底色，这样有什么好处呢？通俗来讲，没有透明通道的那只北极熊走到那里都像是在一个相框里一样，周围永远都有一个白色的长方形，而又透明通道的那只北极熊则更像是一只真正的北极熊——它不会覆盖自己周围的背景色。

那么怎么把普通的图片制作成带有透明通道的图片呢？给大家推荐一个免费的在线制作网站：


<blockquote>[Free Online Image Editor](http://www.online-image-editor.com/)</blockquote>


上传图片后，在右侧的导航栏中选择Wizards，然后在子导航栏中选择带有钻石图标的transparency，最后在你希望变得透明的部分轻轻一点就可以了。当然，把最终图片保存到本地还是需要点击左侧的Save然后选择下载到本地即可。

三、制作ico文件

很多教程中给出的制作ico文件的方法都是需要下载一个很可怕的软件——像PhotoShop一样可怕，之所以说它们可怕，是因为它们实在是很难在短期内入门并制作出你想要的东西。我就是在尝试了很久之后放弃这条路的，所以，我觉得对于这样的软件，如果你身边有懂的人，最好问问他们，对他们而言，可能就是分分钟的事。那么，我们该怎么自己搞定呢？答案是求助万能的Google，于是我找到了如下网站：


<blockquote>[Faviconer - Draw or Generate favicon.ico website icon](http://www.faviconer.com/)</blockquote>


这个网站可以将你的图片轻松转制为ico文件（推荐32X32）——并且你可以在转制后的基础上在线编辑。好了，到此为止工作几乎完成了99%了！

四、上传

制作好的图标一般名字都是favicon.png或者是favicon.ico，那么最后一步就是在你的网站的<head>标签之间插入如下语句：

{% highlight YAML %}

{% endhighlight %}

需要注意的是这里假设你把ico文件放在了根目录（一般情况下），所以href的值直接就是文件名，如果你放在其他地方，那么这个是需要你自己修改的。

恭喜，到这里为止，你就有了自己的LOGO了！
