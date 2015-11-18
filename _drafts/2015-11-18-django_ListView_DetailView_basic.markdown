---
author: shellbye
comments: true
data: 15/11/18
layout: post
slug: django_listview_detailview_basic
title: Django ListView DetailView Basic
categories:
- tech_world
tags:
- Django
---

Django的class-based-view是基于function-based-view而言的,
其产生的原因是因为在网站开发的过程中,其实是有很多的共同的模式的.
比如今天要简单介绍的list-detail模式.很多的网页其实都包含这样的模式的,
比如我们拿BAT举例,百度的搜索结果页就是一个list,但是你点击某一个链接时,
你就进入到了的detail;淘宝也是一样,当你搜索"外套"时,出来的也都是list,
然后当你选中你想要细看的宝贝时,你也进入到了detail;QQ聊天就更明显了,
打开软件看到的是"好友列表",然后想和谁聊天,双击,就进入了detail.

在网站开发中,比如你要开发一个新闻网站,那么,首先你要有一个新闻列表页,
对应list,那么如果用function-based-view应该是这样写:

{% highlight python %}
from django.shortcuts import render
from django.utils.safestring import mark_safe

def index(request):
    news = News.objects.all()
    ret = {
        'news': news,
    }
    return render(request, "news/index.html", ret)
{% endhighlight %}

对应的`urls.py`是这个样子:

{% highlight python %}
from django.conf.urls import url
import views

urlpatterns = [
    url(r'^$', views.index, name='index'),
]
{% endhighlight %}

然后是具体的新闻页面,即detail,代码如下:

{% highlight python %}
def detail(request, news_id):
    news = News.objects.get(id=news_id)
    ret = {
        'title': mark_safe(news.title),
        'content': mark_safe(news.content),
    }
    return render(request, "news/detail.html", ret)
{% endhighlight %}

`urls.py`也就变成了这样:

{% highlight python %}
from django.conf.urls import url
import views

urlpatterns = [
    url(r'^$', views.index, name='index'),
    url(r'^detail/(?P<news_id>\d+)/$', views.detail, name='detail'),
]
{% endhighlight %}

代码量不算很大,但是假设现在要添加一个模块,叫新闻人物,
其逻辑基本上和新闻是一样的,也是list-detail模式,
你是不是就要ctrl+c然后ctrl+v然后把所有上面的"News"变成"People"?
这就是重复劳动了,怎么避免呢?万能的Django早已准备好了对付list-detail的类,
对于上面的代码,改造过程如下:

{% highlight python %}
from django.views.generic import ListView
from django.views.generic import DetailView

class NewsList(ListView):
    model = News

class NewsDetail(DetailView):
    model = News
{% endhighlight %}

`urls.py`变成如下:

{% highlight python %}
from django.conf.urls import url
import views

urlpatterns = [
    url(r'^list2/$', views.NewsList.as_view(), name='index'),
    url(r'^detail2/(?P<pk>[0-9]+)/$', views.NewsDetail.as_view(), name='detail'),
]
{% endhighlight %}

这是是不是很简单了?整个list-detail的逻辑全部在Django内部解决了,
你只需要告诉Django你的list-detail主题model是谁就可以了.当然,
很多工作不是不需要做了,而是由Django替你做了,比如,`templates`的指定,
Django为list默认去寻找`templates/app_name/model_name_list.html`,
为detail默认去寻找`templates/app_name/model_name_detail.html`.
而且这也是可以改变的,你可以通过类的`template_name`属性来设置,
如下:

{% highlight python %}
from django.views.generic import ListView
from django.views.generic import DetailView

class NewsList(ListView):
    model = News
    template_name = "my_own_templates.html"

class NewsDetail(DetailView):
    model = News
    context_object_name = 'the_news'
{% endhighlight %}

上面的`context_object_name`是`view`传给`templates`的变量名,
默认是`model_name_list`和`model_name`,可以通过这个属性自定义.

其实,还可以更简单点:那就是只有`urls.py`:

{% highlight python %}
from django.conf.urls import url
from django.views.generic import ListView
from django.views.generic import DetailView
import models
import views

urlpatterns = [
    url(r'^list3/$', ListView.as_view(model=models.News), name='index'),
    url(r'^detail3/(?P<pk>[0-9]+)/$', DetailView.as_view(model=models.News), name='detail'),
]
{% endhighlight %}

这样连`views.py`都不需要了,当你需要新的新闻人物时,你需要做的就是添加如下两行就可以了:

{% highlight python %}
    url(r'^list4/$', ListView.as_view(model=models.People), name='p-index'),
    url(r'^detail4/(?P<pk>[0-9]+)/$', DetailView.as_view(model=models.People), name='p-detail'),
{% endhighlight %}

是不是很简洁呢?
