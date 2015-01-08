---
author: shellbye
comments: true
date: 2014-07-15 03:26:47+00:00
layout: post
slug: python-change-default-encoding
title: Python 修改默认编码
wordpress_id: 972
categories:
- tech_world
tags:
- Python
- 编码
---

写Python 时遇到这个问题：

    
     'ascii' codec can't encode characters in position 156-159: ordinal not in range(128)


在找了一堆资料并尝试了：

    
    reload(sys)
    sys.setdefaultencoding("utf-8")


之后，无奈还是没有用，最后不得已修改文件Python27\Lib\site.py 并对#479行做了如下修改：

    
    encoding = "utf-8" # Default value set by _PyUnicode_Init()


然后就OK了
