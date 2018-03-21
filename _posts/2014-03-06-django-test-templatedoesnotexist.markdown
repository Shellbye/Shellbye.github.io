---
author: shellbye
comments: true
date: 2014-03-06 02:28:05+00:00
layout: post
slug: django-test-templatedoesnotexist
title: Django test TemplateDoesNotExist
wordpress_id: 813
categories:
- tech_world
tags:
- Django
- TemplateDoesNotExist
- test
---

自己在用Django练手的时候，写了如下一个小的网站


    
    
    │  manage.py
    ├─accounts
    │  │  models.py
    │  │  tests.py
    │  │  urls.py
    │  │  views.py
    │  │  __init__.py
    │  │
    │  └─templates
    │      └─accounts
    │              detail.html
    │              index.html
    │              login.html
    │              register.html
    │
    ├─my_site
    │      settings.py
    │      urls.py
    │      wsgi.py
    │      __init__.py
    │
    └─templates
            base.html
    



在写测试时，当测试页面重定向的时候，因为被定向到的页面login.html包含如下语句


    
    {% raw %}
    {% extends "base.html" %}
    {% endraw %}



使得测试会报错


    
    
    TemplateDoesNotExist: base.html
    



这个时候，我的settings.py里的TEMPLATE_DIRS是这个样子的


    
    
    TEMPLATE_DIRS = (
        'templates'
    )
    



在编辑器Pycharm中，以及代码正常运行时，login.html是可以正常运行的，说明TEMPLATE_DIRS里的指示是对的，但是测试时总会报同样的错，测试代码如下


    
    
    self.assertRedirects(response, reverse("accounts:login"))
    




昨天晚上整了很久，也找了很多资料，都没有解决，包括把base.html放到accounts--template--accounts都不行，回去睡了一觉，今天大早来，参考了一下以前做过的一个django项目，里面的TEMPLATE_DIRS是这个样子的


    
    
    import os
    TEMPLATE_DIRS = (
        os.path.join(os.path.dirname(__file__), '..', 'templates').replace('\\', '/')
    )
    



虽然还不懂是怎么这样就可以了，但是问题起码解决了，先记录下问题，有空在研究下为啥第一种标准方式不行
