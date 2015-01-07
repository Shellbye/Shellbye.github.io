---
author: shellbye
comments: true
date: 2014-11-07 01:11:52+00:00
layout: post
slug: ubuntu-ruby-uninstall
title: ubuntu ruby uninstall
wordpress_id: 1103
categories:
- 技术世界
tags:
- ruby
---

由于用gem按转rails的时候需要ruby的版本大于等于1.9.3，但是ubuntu默认安装的是1.8，所以我就自己安装了1.9，不料这个1.9居然是1.9.1，于是又得装1.9.3，但是装完之后还是迟迟无法安装rails，于是尝试卸载ruby，在网上找了不少貌似成功的案例，但是对我的都不适用，最后发现可能是因为我装的版本过多，所以卸载时也要指明版本号，最后的成功的命令如下：

{% highlight YAML %}
sudo apt-get purge ruby1.8
{% endhighlight %}

安装了多个ruby之后，可以用如下命令在不同版本之间选择：

{% highlight YAML %}
update-alternatives --config ruby
{% endhighlight %}

