---
author: shellbye
comments: true
date: 2016-11-16 00:00:08+00:00
layout: post
slug: speed_up_django_admin_changelist_by_cache
title: Speed up Django admin by cache
categories:
- tech_world
tags:
- Django
---

由于之前从来没有遇到过累死的业务需求，所以一直没觉得Django自带的后台管理系统admin有什么问题，
最近在做一个中小学题目管理系统，上来就是百万级别的题目，结果在admin中，打开题目的列表查看第一页
的十来个数据居然需要十几秒，简直是慢的令人发指，用[django-debug-toolbar]{:target="_blank"}
查看了一下，居然是因为求总页数时的`select count(*) from soma_table;`导致的，
这个数据其实没什么大用，就是计算一下总共有几万页而已，具体是6847321还是6847322其中并不重要，
在网上简单查找了一下，找到了一个[通过利用mysql自带的估计表中的数据的方法]{:target="_blank"}来完成这一操作的，
然后我根据自己的业务逻辑，对上文中的方法进行了一些修改，结合Django自带的[缓存]{:target="_blank"}系统，
使得我的页数是精确值，但是又可以在很大程度上提升速度。代码见下：

首先需要在`settings.py`中启用缓存：
{% highlight python %}
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.filebased.FileBasedCache',
        'LOCATION': '/var/tmp/django_cache',
    }
}
{% endhighlight %}

然后在`models.py`中自定义`Manager`以及相关的覆盖方法如下：
{% highlight python %}
from django.core.cache import cache
from django.db.models.query import QuerySet
from django.db import models


class CacheCount(QuerySet):
    def count(self):
        cache_key = 'admin_total_count'  # change this accordingly
        _count = cache.get(cache_key)
        if _count:
            return _count
        count = self.query.get_count(using=self.db)
        cache.set(cache_key, count, 3600 * 24)
        return count

class CacheCountManager(models.Manager):
    def get_query_set(self, using=None):
        # 这里需要注意，因为我使用了多个数据库，所以这里需要制定具体的数据库，
        # 这里你的具体代码可能需要一些变动
        return CacheCount(self.model, using=using)

class YourModel(model.Model):
    ...
    objects = CacheCountManager()

{% endhighlight %}

接下来就是在`admin.py`中覆盖`get_queryset`方法：
{% highlight python %}
from django.contrib import admin
from app.models import YourModel


# Register your models here.
class YourModelModelAdmin(admin.ModelAdmin):
    # A handy constant for the name of the alternate database.
    using = 'the_db'

    def view_on_site(self, obj):
        return obj.url

    def save_model(self, request, obj, form, change):
        obj.save(using=self.using)

    def delete_model(self, request, obj):
        obj.delete(using=self.using)

    # Here is the code
    def get_queryset(self, request):
        qs = self.model.objects.get_query_set(using=self.using)

        ordering = self.ordering or ()  
        if ordering:
            qs = qs.order_by(*ordering)
        return qs

admin.site.register(YourModel, YourModelModelAdmin)
{% endhighlight %}

至此，再次打开admin的列表页，就只有第一次会像平时一样慢，但是之后就全部都是秒开了。


[django-debug-toolbar]:http://django-debug-toolbar.readthedocs.io/en/stable/
[通过利用mysql自带的估计表中的数据的方法]:https://craiglabenz.me/2013/06/12/how-i-made-django-admin-scale/
[缓存]:https://docs.djangoproject.com/en/1.10/topics/cache/
