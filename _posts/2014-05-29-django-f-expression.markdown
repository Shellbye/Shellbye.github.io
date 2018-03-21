---
author: shellbye
comments: true
date: 2014-05-29 03:16:39+00:00
layout: post
slug: django-f-expression
title: Django F expression 实例
categories:
- tech_world
tags:
- database
- Django
- Python
---

之前做项目时，因为对Django的特性不是很熟，所以导致数据库在正常的操作顺序下，因为同步较多而产生过脏数据，后来通过Django的 F 函数解决了同步问题，但是没搞清原理，今天师姐问了下我原理，我就去看了下Django的[官方文档](https://docs.djangoproject.com/en/dev/ref/models/queries/#django.db.models.F)，做了个如下的简单实验，真正理解了 F 函数的原理。

{% highlight Python %}
# models.py
class Reporters(models.Model):
    name = models.CharField(max_length=100)
    stories_filed = models.IntegerField(default=0)




# views.py
def diveinto_f(request):
    from django.db import connection
    from models import Reporters
    reporter = Reporters.objects.get(name='Tintin')
    reporter.stories_filed += 1
    reporter.save()
    print connection.queries

    from django.db.models import F
    reporter1 = Reporters.objects.get(name='Tintin')
    reporter1.stories_filed = F('stories_filed') + 1
    reporter1.save()
    print connection.queries
    # ...




# urls.py
    url(r'^test/test/', 'diveinto_f'),
{% endhighlight %}

在运行diveinto_f前，数据库中的name为Tintin的记录中stories_filed的值为6，记录之，然后跑一遍diveinto_f，将print的两组值copy出来，去掉前面django自带的一些sql语句，然后对比两次的save语句的sql如下：


    [{u'time': u'0.001', u'sql': u"SELECT `credit_reporters`.`id`, `credit_reporters`.`name`, `credit_reporters`.`stories_filed` FROM `credit_reporters` WHERE `credit_reporters`.`name` = 'Tintin' "}, {u'time': u'0.001', u'sql': u'SELECT (1) AS `a` FROM `credit_reporters` WHERE `credit_reporters`.`id` = 1  LIMIT 1'}, {u'time': u'0.000', u'sql': u"UPDATE `credit_reporters` SET `name` = 'Tintin', `stories_filed` = 7 WHERE `credit_reporters`.`id` = 1 "}]

    [{u'time': u'0.001', u'sql': u"SELECT `credit_reporters`.`id`, `credit_reporters`.`name`, `credit_reporters`.`stories_filed` FROM `credit_reporters` WHERE `credit_reporters`.`name` = 'Tintin' "}, {u'time': u'0.000', u'sql': u'SELECT (1) AS `a` FROM `credit_reporters` WHERE `credit_reporters`.`id` = 1  LIMIT 1'}, {u'time': u'0.001', u'sql': u"UPDATE `credit_reporters` SET `name` = 'Tintin', `stories_filed` = `credit_reporters`.`stories_filed` + 1 WHERE `credit_reporters`.`id` = 1 "}]


可以清楚地看到，前面的select语句是一样的，但是最后的UPDATE的SET语言开始不同了，没有用到 F 函数的地方，是直接将运算的结果（即7）存入了数据库，而用到了 F 函数的地方，却是从数据库获取stories_filed的最新值，然后加1，差别就在这里了，就像官方文档说的，


>An F() object represents the value of a model field. It makes it possible to refer to model field values and perform database operations using them without actually having to pull them out of the database into Python memory.

>Instead, Django uses the F() object to generate a SQL expression that describes the required operation at the database level

大意就是说，F 函数使得可以在数据库层面上直接进行操作，而不是把数据库放到python内存中再进行操作。

参考资料：

[1].https://docs.djangoproject.com/en/dev/ref/models/queries/#django.db.models.F
[2].https://docs.djangoproject.com/en/dev/faq/models/#how-can-i-see-the-raw-sql-queries-django-is-running
