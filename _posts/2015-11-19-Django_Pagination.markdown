---
author: shellbye
comments: true
data: 2015-11-19 11:16
layout: post
slug: django-pagination
title: Django Pagination
categories:
- tech_world
tags:
- Django
---

做过web开发的人一定对分页有一定的印象,就我个人而言,
一直是害怕分页的,因为我第一次接触分页时,那个记忆是比较痛苦的,
当时是师兄坐在我背后,他指挥着我写分页,而我那时又啥也不懂,
虽然最后通过这种方式懂了,但是这个过程还是让我心有余悸.
所以这也是我写技术博客的一个原因,很多技术很牛的人其实是不善于教别人的,
只有像我这种刚刚学会的人,才知道怎么去教别人.

先说说为什么要分页,在刚开始学web开发时,很多人意识不到分页的重要性,
比如当初的我.觉得能把数据库里的东西拿出来,展示到网页上是一件非常牛逼的事,
其他的似乎都不重要了.但是很快的,你要遇到你人生当初的第一次"用户体验",
所有数据一次加载出来是比较省事,但是体验不好.试想你去看书,书只有一页,
很长很长的一页,如果你一次看不完,那么第二次你就很难从上次终止的地方开始了.
所以分页就出现了.

web中也是一样的道理,用户需要一部分一部分的看,这样比较方便查找,
而且从服务器的角度看,一点一点的返回数据,比一次性返回全部数据也压力更小,
而且也更节约带宽.

说这么多废话是因为Django把分页已经写到非常简单的地步了,简单到我如果不说点废话,
那么这篇博客就真没啥东西了.[上一篇]{:target="_blank"}写了Django的ListView,
那么这篇就基于上篇写分页.

首先是`views.py`:

{% highlight python %}
class NewsList(ListView):
    model = News
    paginate_by = 2

{% endhighlight %}

相比与没有分页的情况,只是多了一个新的属性`paginate_by`,
该属性用于指定每页所包含的对象个数.加了这一行代码之后,分页的`views.py`部分就搞定了.
接下来是`templates`里面的页面了:

{% highlight python %}
{% raw %}
    {% if is_paginated %}
        {% if page_obj.has_previous %}
            <a href="?page={{ page_obj.previous_page_number }}">previous</a>
        {% endif %}

        <span class="current">
            Page {{ page_obj.number }} of {{ page_obj.paginator.num_pages }}.
        </span>

        {% if page_obj.has_next %}
            <a href="?page={{ page_obj.next_page_number }}">next</a>
        {% endif %}
    {% endif %}
{% endraw %}
{% endhighlight %}

这是需要添加的分页控件的代码,其中最主要的一个变量就是`page_obj`,
它用来控制分页控件的前页和后页,以及是否又前一页后一页等.其中,
页数的名字"page"是可以自己在`views.py`里通过`page_kwarg`这个变量来自定义的.
分页的顺序也是可以通过`ordering`来自己定义的,指定排序依据的field就可以.

[上一篇]:/blog/tech_world/django_ListView_DetailView_basic/
