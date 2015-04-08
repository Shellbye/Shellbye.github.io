---
author: shellbye
comments: true
date: 2015-04-08 09:27:08+00:00
layout: post
slug: django_log_config
title: django log config
categories:
- tech_world
tags:
- django
---

记得第一次接触[django](https://www.djangoproject.com/)是一年半前，跟着一个博士师兄写了一段时间之后，
师兄给我布置了一个任务，
让我给当时正在做的网站添加日志系统，当时接到这个任务时我就感觉做不了，因为虽然写了也有几天了，
但是基本上都是ctrl+c然后ctrl+v，然后改改表名，改改业务逻辑，对django没有任何的了解，
基本上只是在写python，而不是django.

折腾了几天之后，果然还是没有折腾出来，于是任务交由一个很NB的师弟去做了，当然就做出来了...囧

上一次再接触django的这个日志功能是大概一年前的样子，对django有了一点基本的认识，
也看了django官方的一些文档（真是好东西啊），但是试了试，还是没有配好，于是又交给了另一个师弟，囧

今天是突然看到了自己不久前写的一个django_base的小项目，旨在把用django做网站需要用到的一些基本功能都实现了，
然后在用的时候可以快速开发，免去一些基本的设置，比如登录注册忘记密码啥的。就像你所知道的那样，
这个小项目一直没有日志功能，对于终将部署到服务器上的网站来说，没有日志功能是一件非常可怕的事（如果你经历过的话），
于是今天再次尝试了一下django的[日志功能](https://docs.djangoproject.com/en/1.7/topics/logging/), 
虽然中途也遇到了一些问题，但是终究还是可以使用了，^_^

```setting.py```
{% highlight python %}
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'verbose': {
            'format': '%(levelname)s %(asctime)s %(module)s %(process)d %(thread)d %(message)s'
        },
        'simple': {
            'format': '%(levelname)s %(message)s'
        },
    },
    'filters': {
        'special': {
            '()': 'helper.utils.logging_filter',
            'foo': 'bar',
        }
    },
    'handlers': {
        'null': {
            'level': 'DEBUG',
            'class': 'logging.NullHandler',
        },
        'console': {
            'level': 'DEBUG',
            'class': 'logging.StreamHandler',
            'formatter': 'verbose'
        },
        'mail_admins': {
            'level': 'ERROR',
            'class': 'django.utils.log.AdminEmailHandler',
            'include_html': False, # set this to True may made the email be suspicious
        },
        'file': {
            'level': 'DEBUG',
            'class': 'logging.FileHandler',
            'filename': 'debug.log',
            'formatter': 'verbose'
        },
    },
    'loggers': {
        'django': {
            'handlers': ['console'],
            'level': os.getenv('DJANGO_LOG_LEVEL', 'INFO'),
        },
        'django.request': {
            'handlers': ['file'],
            'level': 'DEBUG',
            'propagate': True,
        },
        'accounts.views': {
            'handlers': ['file'],
            'level': 'INFO',
            'propagate': True,
        },
        'news': {
            'handlers': ['mail_admins'],
            'level': 'INFO',
        },
        'news.views': {
            'handlers': ['console'],
            'level': os.getenv('DJANGO_LOG_LEVEL', 'INFO'),
        }
    },
}
{% endhighlight %}

需要特别强调的是```mail_admins```，这里并不是简单的这样配置了就可以工作的，
还需要一些```settings.py```里的其他配置配合才能正常工作，主要有以下几点：

1.```ADMINS```一定要设置，并且格式如下：

{% highlight python %}
ADMINS = (
    ('shellbye', 'some_other_email@domain.com'),
)
{% endhighlight %}
    
注意结尾的那个英文逗号( ,)，这个是必须的，否则就不是tuple了。

2.```SERVER_EMAIL```也需要设置，
因为django[默认的设置](https://docs.djangoproject.com/en/1.7/ref/settings/#server-email)是root@localhost，
你的邮件服务器很可能没有这个账号，所以你需要设置成你的邮件服务器里存在的一个发信地址，比如：

{% highlight python %}
SERVER_EMAIL = 'your_account@example.com'
{% endhighlight %}

在我的项目里，设置好这些就可以发送日志邮件了，也许你的还有其他需要配置的，
具体的请参考万能的stackoverflow.com里的[这个](http://stackoverflow.com/questions/1414130/django-not-sending-emails-to-admins)回答。

3.```mail_admins```里面的```include_html```如果设置成```True```的话，有可能会被当做垃圾邮件。我使用的163的免费邮箱，
就发生了系统退信。