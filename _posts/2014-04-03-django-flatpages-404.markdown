---
author: shellbye
comments: true
date: 2014-04-03 07:33:10+00:00
layout: post
slug: django-flatpages-404
title: Django flatpages 404
wordpress_id: 843
categories:
- 技术世界
tags:
- '404'
- Django
- flatpages
---

仍然接着之前的工作，这次想要给网站添加静态页面，也就是Django自带的flatpages模块，需要在setting.py的INSTALLED_APPS中，添加如下一行：


    
    
    'django.contrib.flatpages',
    



注：因为


    
    'django.contrib.flatpages',



依赖于


    
    'django.contrib.sites',



所以一定要确保后者也要添加在INSTALLED_APPS中，

然后在MIDDLEWARE_CLASSES中添加


    
    
    'django.contrib.flatpages.middleware.FlatpageFallbackMiddleware'
    



最后运行manage.py syncdb新建两张表之后就可以在/admin/中看到效果了：

[caption id="attachment_844" align="alignnone" width="300"][![admin-flatpages](http://www.shellbye.com/blog/wp-content/uploads/2014/04/QQ截图20140403151932-300x132.png)](http://www.shellbye.com/blog/wp-content/uploads/2014/04/QQ截图20140403151932.png) admin-flatpages[/caption]

到这里我是都没有遇到啥问题的，真正的问题来自创建静态页面时，点击上图“添加”按钮并填写相关数据后，最后这个选项该怎么处理我就不清楚了，考虑到我们的网站都是基本上在本地的127.0.0.1：8000上跑，所以我自然的添加了一个127.0.0.1：8000，然后就一直遇到404，直到看了[这篇文章](http://matthewdaly.co.uk/blog/2012/03/24/yet-another-tutorial-for-building-a-blog-using-python-and-django-part-3/)，我才知道原来不用修改，直接使用默认的example.com就OK了。

[caption id="attachment_845" align="alignnone" width="300"][![admin-site](http://www.shellbye.com/blog/wp-content/uploads/2014/04/QQ截图201404031519321-300x89.png)](http://www.shellbye.com/blog/wp-content/uploads/2014/04/QQ截图201404031519321.png) admin-site[/caption]

以上操作完成之后，运行应该会出现

    
    
    TemplateDoesNotExist at /about/contact/
    flatpages/default.html
    



因为我们还没有添加模板，当然这个就很简单了，只要在templates里面添加flatpages/default.html，基本的模板如下：


    
    
    
    <html>
    <head>
    <title>{{ flatpage.title }}</title>
    </head>
    <body>
    {{ flatpage.content }}
    </body>
    </html>
    



这样就全部OK了。

参考：
[1] https://docs.djangoproject.com/en/dev/ref/contrib/flatpages/
[2] http://www.djangobook.com/en/2.0/chapter16.html#flatpages
