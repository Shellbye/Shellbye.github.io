---
author: shellbye
comments: true
date: 2013-09-18 15:50:53+00:00
layout: post
slug: sae-wordpress-modify-fixed-url
title: SAE WordPress 修改固定链接
wordpress_id: 20
categories:
- 技术世界
tags:
- SAE
- WorPress
---

搞了三四天，终于在SAE上搭起了WordPress，但是匆匆发了一篇测试文章之后，发现文章可以预览但是无法点击进入详情，点击链接会报错如下：
Not Found


The requested URL /***/***/ was not found on this server.




Google了好一阵吧，找到了[这篇](http://notes.sinaapp.com/miscellaneous/sae-wordpress-urlrewrite)文章，看了里面的解决方案，但是我迟迟没有按照他说的做，因为我的config.yaml文件只有如下两行：


`---
name: baijianguo
version: 1
...
`
所以一直没有按照作者的方法改，但是再也找不到其他方法了，于是硬着头皮把作者所说的如下的代码贴进了config.yaml中：
`---
handle:
- rewrite: if(!is_dir() && !is_file()) goto "index.php?%{QUERY_STRING}"
`
然后一个华丽丽的刷新，成功了！！！
