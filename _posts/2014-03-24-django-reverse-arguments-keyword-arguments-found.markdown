---
author: shellbye
comments: true
date: 2014-03-24 12:59:49+00:00
layout: post
slug: django-reverse-arguments-keyword-arguments-found
title: Django Reverse for '*' with arguments '()' and keyword arguments '{}' not found
wordpress_id: 833
categories:
- 技术世界
tags:
- Django
- Reverse
---

为了避免url的硬编码，Django提供了很方便的后台的reverse函数，以及前台的url标签，具体的使用方法文档很多，不再赘述，这里简单描述我遇到的一个小问题，以及其解决方案

在我的ROOT_URLCONF中，有如下一行配置：

    
    url(r'^django_databrowse/(.*)', django_databrowse.site.root, name="check_data"),
    )


然后在前端的页面中，有如下的类似引用

    
    {% raw <a href="{% url 'check_data' %}">查看数据</a> %}


这时报错如下：

    
    Reverse for 'check_data' with arguments '()' and keyword arguments '{}' not found.


也就是服务器没有找到check_data对应的内容，在stackoverflow上找到[这样一个](http://stackoverflow.com/questions/9649587/reverse-for-with-arguments-and-keyword-arguments-not-found)解决方案，

于是将前端修改为这样：

    
    <a href="{% url check_data %}">查看数据</a>


报错变成了

    
    'url' requires a non-empty first argument. The syntax changed in Django 1.5, see the docs.


果然Django的1.5版本中有变化，但是查看了一下文档，也依然没有找到解决方案。继续在stackoverflow上找，发现了[这样一个](http://stackoverflow.com/questions/14882491/django-release-1-5-url-requires-a-non-empty-first-argument-the-syntax-change)解决方案，但是他的解决办法太复杂，就连作者自己都说这样的方法很危险，容易破坏一些设计，但是他回答中的这两句提醒了我：

    
    {% raw {% url "something.else" foo bar %} %}
    )


这里的url构造是需要传入参数的，因为后台的

    
    url(r'^django_databrowse/(.*)', django_databrowse.site.root, name="check_data"),
    )


中，明显不是一个url的结尾，它后面还需要东西，即是从url上看它没有参数也能访问（根目录），但是传参还是必要的，至少要是一个空字符串，于是我把前端改成了：

    
    {% raw <a href="{% url 'check_data' ''%}">查看数据</a> %}


即在后面添加了空串作为参数传入，于是一切OK.

为了证实url中的传参是因为后天的那句配置需要参数，我将后台配置中的“(.*)”去掉，前台也不传入空串作为参数，一切仍然OK，猜测正确。
