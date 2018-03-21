---
author: shellbye
comments: true
date: 2015-12-29 10:27:08+00:00
layout: post
slug: Django_ListView_Advanced
title: Advanced Django ListView
categories:
- tech_world
tags:
- Django
---

之前学习Djang时写过一个入门版本的关于ListView的[笔记]{:target="_blank"}，
但是其中的东西都笔记基础，没法进行高级的定制，所以有了这篇笔记，
纪录下自己最新的一些心得。

###1.添加返回数据  

接[上一篇]{:target="_blank"}笔记，假如现在你要在新闻的列表页中集中显示所有的新闻类别，
要怎么做呢？因为你把很多的逻辑都托管给Django了，所以这个时候，新添加的逻辑也是要依赖于Django的，
比如你要新添加的数据，就要用到[`get_context_data`]{:target="_blank"}这个函数了，
官网中关于`get_context_data`的介绍是：

> Returns context data for displaying the list of objects.  

这是一个字典，其中默认已经包含了四个对象，一个是返回的对象列表和三个关于分页的对象。
现在我们要在其中添加东西，那就就自己实现一遍这个函数就可以了，代码如下：

{% highlight python %}
def get_context_data(self, **kwargs):
	# 先通过调用父类的方法获取到原始的context对象，其中包含上面提到的四个对象
	context = super(NewsList, self).get_context_data(**kwargs)
	# 在其中添加我们想要添加的对象，这样context就有五个对象了
	context['category_list'] = NewsCategory.objects.all()
	return context
{% endhighlight %}

然后在`templates`中，我们就可以直接使用`category_list`这个对象了，像这样：

{% highlight python %}
{% raw %}
{% for c in category_list %}
    {{c.title}}
{% endfor %}
{% endraw %}
{% endhighlight %}

###2.自定义返回对象集  
在[上一篇]{:target="_blank"}笔记中，我们是直接获取的所有的新闻，
现在假设我们要添加一个搜索功能，按照新闻标题进行搜索，然后只返回那些命中搜索的新闻，
这就需要过滤返回的结果集了，基于上面的内容，我们最容易想到的就是在`get_context_data`中，
对其中的`object_list`进行过滤，比如在`return`语句前添加如下一行：

{% highlight python %}
context['news_list'] = context['news_list'].filter(title__contains='search_text')
{% endhighlight %}

这样的写法，当有分页功能时（大多数情况下是有的），就会出现如下错误：

> AssertionError at Cannot filter a query once a slice has been taken.

官网的解释在[这里]，说白了就是一旦分页了，也就是`slice`了之后，这个查询就在数据库里执行过了，
所以这个时候就没法继续去`filter`了。所以这并不是一个优雅的解决方案，优雅的方案是使用正确的函数，
这里正确的函数就是[`get_queryset`]{:target="_blank"}这个函数了，比如：

{% highlight python %}
def get_queryset(self):
	if 'q' in self.request.GET:
		return News.objects.filter(title__contains=self.request.GET['q'])
	return super(NewsList, self).get_queryset()
{% endhighlight %}

了解一个框架的最佳方式就是要懂其常用函数的功能，那么写一个框架的最佳方式，
就是函数名一定要见名知意，不然找正确的函数就比较痛苦了。


[笔记]:/blog/tech_world/django_ListView_DetailView_basic/
[上一篇]:/blog/tech_world/django_ListView_DetailView_basic/
[`get_context_data`]:https://docs.djangoproject.com/en/1.9/ref/class-based-views/mixins-multiple-object/#django.views.generic.list.MultipleObjectMixin.get_context_data
[这里]:https://docs.djangoproject.com/en/1.9/topics/db/queries/#limiting-querysets
[`get_queryset`]:https://docs.djangoproject.com/en/1.9/ref/class-based-views/mixins-multiple-object/#django.views.generic.list.MultipleObjectMixin.get_queryset
