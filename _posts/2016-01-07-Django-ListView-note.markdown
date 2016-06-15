---
author: shellbye
comments: true
date: 2016-01-07 11:27:08+00:00
layout: post
slug: django-pagination-note
title: Django Pagination note
categories:
- tech_world
tags:
- Django
---

在`ListView`中使用了`paginate_by`之后，
并在`template`中使用了`object_list.first`这样的变量时，
Django抛出了如下错误：

{% highlight bash %}
Cannot reorder a query once a slice has been taken.
{% endhighlight %}

在[Advanced Django ListView]{:target="_blank"}中，我也提到过同样的问题，
但是两者的原因是不一样的。
上一次的错误是`AssertionError at Cannot filter a query once a slice has been taken`，
即一旦分片之后，因为SQL已经执行了所以没法再去`filter`，
而这一次的问题是不能再排序（`reorder`），之所以说是再排序，
是因为在分页的时候，Django已经进行了一次[排序]{:target="_blank"}，
这次排序中，如果在`model`中没有指定排序的方式（`ordering`），
那么就会默认使用其主键（默认为`id`）。
那么第二次排序发生在哪里呢？发生在调用`object_list.first`时，
看[源码]{:target="_blank"}可以发现，first并不是简单的取了当前`queryset`的第一个，
而是又一个判断的过程，判断它是否已经排序，若已经排序，则取第一个，
否则按照主键排序再取第一个，这就是第二次排序发生的地方，
究其原因，是因为我在`model`的定义中没有在`Meta`中指定排序字段，
即`ordering`.如果指定了该字段，则可以避免上面的问题。

在找资料的过程中，我发现了一种可以避免第二次排序的方式，
就是不用`object_list.first`这样的形式，而用`object_list.0`，
这个就不管顺序了，直接取出目前最前面那个，这样同样也不会出现第二次排序，
也不会报错了。

[Advanced Django ListView]:/blog/tech_world/Django-ListView-advance/
[排序]:https://docs.djangoproject.com/en/1.9/ref/models/querysets/#order-by
[源码]:https://github.com/django/django/blob/master/django/db/models/query.py#L544
