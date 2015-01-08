---
author: shellbye
comments: true
date: 2014-08-13 09:56:07+00:00
layout: post
slug: django-admin-css-missing
title: Django Admin CSS missing
wordpress_id: 1011
categories:
- tech_world
tags:
- Django
---

在用Django写网站的时候，本地的开发一直没有问题，Django自带的后台admin也用的很顺畅，但是部署在服务器上之后，发现自带的admin页面变丑了，原来是css等admin下的static文件找不到了。原因可能是我在setting.py中的配置问题，代码片段如下：

    
    STATIC_URL = '/static/'
    
    # Additional locations of static files
    STATICFILES_DIRS = (
        'static',
    )


在stackoverflow上找到了这个[问题](http://stackoverflow.com/questions/7241688/django-admin-css-missing)，其实里面也没有给出漂亮的解决方法，只是把\django\contrib\admin\static\目录下的内容copy到static目录里面确实有点粗野，而且我后俩由admin转向了xadmin，而xadmin的static文件有8M多，几乎和整个项目一样了，就这样push到版本库也实在是感觉不合适。但是不得不进行，看了上面那个问题的下面一个回答，说可以用manage.py来帮我完成这个copy工作，具体的做法如下：

1.[manage.py命令](https://docs.djangoproject.com/en/dev/howto/static-files/#deployment)

    
    python manage.py collectstatic


这个命令会帮你把所有INSTALLED_APPS用到的static文件都汇聚到项目根目录下，文档参考[这里](https://docs.djangoproject.com/en/dev/ref/contrib/staticfiles/#django-admin-collectstatic)。

2.copy

把上面的命令整理好的static文件直接copy到你的项目static目录里。话说写到这里我才反应过来为毛非要手动copy呢？这个肯定是可以配置的嘛。看3.

3.settings.py配置以及manage.py的结合使用

运行下面这个命令时，收集到的文件时被汇聚到了settings.py里面的[STATIC_ROOT](https://docs.djangoproject.com/en/dev/ref/settings/#std:setting-STATIC_ROOT)这个参数所配置的路径的，但是如果你直接这样配置：

    
    STATIC_ROOT = 'static/'
    
    # URL prefix for static files.
    # Example: "http://example.com/static/", "http://static.example.com/"
    STATIC_URL = '/static/'
    
    # Additional locations of static files
    STATICFILES_DIRS = (
        'static',
    )


就会出现如下报错信息：

    
    ImproperlyConfigured: The STATICFILES_DIRS setting should not contain the STATIC_ROOT setting


提示的比较清楚了，即STATICFILES_DIRS是不允许包含STATIC_ROOT的，我的理解是不然岂不是相当于从STATIC_ROOT拷贝东西到STATIC_ROOT了？瞎猜的，不负责哈。于是乎，我们需要先注释掉STATICFILES_DIRS里面的static，然后在运行
    
    python manage.py collectstatic

命令，最后在注释回来，并且最好在把STATIC_ROOT的值替换回默认值空字符串。
