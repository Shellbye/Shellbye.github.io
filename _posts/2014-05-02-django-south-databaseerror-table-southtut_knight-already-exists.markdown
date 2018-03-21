---
author: shellbye
comments: true
date: 2014-05-02 14:00:08+00:00
layout: post
slug: django-south-databaseerror-table-southtut_knight-already-exists
title: 'Django South DatabaseError: table "southtut_knight" already exists'
wordpress_id: 878
categories:
- tech_world
tags:
- Django
- South
---

在按照[文档](http://south.readthedocs.org/en/latest/tutorial/part1.html)学习使用South时，遇到如下问题，

    
    DatabaseError: table "southtut_knight" already exists


在google上找了一下，貌似是因为south的安装方式不对，当时看官方的[安装文档](http://south.readthedocs.org/en/latest/installation.html#installation)时，就很好奇为啥不用pip而是用easy_install，于是我用pip卸载了south（pip uninstall south），然后又用easy_install重新安装了一遍，问题果然解决了。

参考资料（需要翻墙）
https://groups.google.com/forum/#!topic/south-users/P8oMI2hExUM
