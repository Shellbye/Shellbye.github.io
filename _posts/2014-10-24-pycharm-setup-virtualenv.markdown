---
author: shellbye
comments: true
date: 2014-10-24 13:34:26+00:00
layout: post
slug: pycharm-setup-virtualenv
title: Pycharm setup virtualenv
wordpress_id: 1083
categories:
- 技术世界
tags:
- Django
- pycharm
- Python
- virtualenv
---

迫不及待的想要尝试下最新版的Django1.7，但是有个旧的项目是使用的Django1.5，所以就在自己本地参照[官网文档安装了virtualenv](https://virtualenv.pypa.io/en/latest/)，并在pycharm里配置了一下，终于可以同时使用多版本的Django了。

注：最新版Pycharm（3.4），可以参考官方的[这个文档](http://www.jetbrains.com/pycharm/webhelp/creating-virtual-environment.html)。

1.安装virtualenv


    
    pip install virtualenv



2.新建虚拟环境ENV


    
    virtualenv ENV



3.切换至虚拟环境并激活之


    
    cd ENV




    
    source bin/activate



至此即可使用完全与系统python的环境隔离的虚拟环境

4.退出虚拟环境


    
    deactivate



5.在Pycharm中配置虚拟环境


    
    File-->Settings-->Project Interpreters-->Python Interpreters-->点击右侧绿色加号-->local-->然后选择/path/to/ENV/bin/python2.7



至此pycharm中配置virtualenv完成，而且亲测打开pycharm对应的项目就直接可以在不同的环境中切换，甚至不需要激活虚拟环境
