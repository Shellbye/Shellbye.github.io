---
author: shellbye
comments: true
date: 2014-09-25 03:41:45+00:00
layout: post
slug: linux-shell-bash-get-command-output
title: Linux Shell Bash get command output
wordpress_id: 1049
categories:
- 技术世界
tags:
- bash
- linux
---

有如下脚本，为何运行错误？


{% highlight YAML %}
#!/bin/sh
current_path = `pwd`
echo $current_path
{% endhighlight %}


这是我在写脚本在服务器上部署网站时遇到的一个问题，很纠结，语法完全没有错误啊，为啥输出如下：


{% highlight YAML %}
current_path: not found
{% endhighlight %}


最后在看别人的脚本时发现人家没有我那多余的空格（不能怪python让我养成良好的格式习惯），于是“新”的脚本如下：


{% highlight YAML %}
#!/bin/sh
current_path=`pwd`
echo $current_path
{% endhighlight %}


成功。画蛇添足啊，我觉得可能是因为那个空格使得linux把current_path当做命令了，所以才会有上面的报错信息。
