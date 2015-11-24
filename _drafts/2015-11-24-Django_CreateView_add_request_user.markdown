---
author: shellbye
comments: true
data: 2015-11-24 14:59
layout: post
slug: django_createview_add_request_user
title: Django CreateView add request user
categories:
- tech_world
tags:
- Django
- Python
---

在实际的开发中,我们有些操作常常是要记录当前操作的用户的,
比如我在下面的例子中,用户可以自定义话题,那么,所有用户自定义
的话题,就需要记录是哪个用户定义了它,下面我就结合Django自带
的CreateView来记录下今天的Django学习笔记.

首先是`models.py`

{% highlight python %}
# -*- coding: utf-8 -*-
from django.db import models
from django.core.urlresolvers import reverse
from django.contrib.auth.models import User


class Topic(models.Model):
    title = models.CharField(max_length=200)
    create_time = models.DateTimeField(auto_now_add=True)
    create_user = models.ForeignKey(User)

    def __unicode__(self):
        return self.title

    def get_absolute_url(self):
        return reverse("interaction:detail", args=[self.id])
{% endhighlight %}

这里添加`get_absolute_url`方法,是因为在`CreateView`创建成功之后,
后重定向到`model`的get_absolute_url所指向的地址,如果这里不实现该方法,
也是可以的,就需要在CreateView中给出`success_url`属性,
或实现`get_success_url()`方法,这三种方式是一样的作用,
就是指定创建成功之后的重定向的位置.

然后是`views.py`

{% highlight python %}
# -*- coding: utf-8 -*-
from django.views.generic import *
from models import *
from forms import *


class TopicCreateView(CreateView):
    model = Topic
    form_class = TopicForm
    template_name_suffix = '_create_form'

    def get_form_kwargs(self):
        # add request for form to validate
        kwargs = super(TopicCreateView, self).get_form_kwargs()
        kwargs.update({"request": self.request})
        return kwargs

{% endhighlight %}

在这里,重写了`get_form_kwargs`,
是因为在下面的`forms.py`中,`TopicForm`需要用到`request`,
所以这里就把`request`放到其初始化方法中,
这样就可以在`forms.py`中获取到当前用户
其中还用到了`form_class`这个属性,用户指定创建时适用的表单.
这个类我是在`forms.py`里面定义的,如下:

{% highlight python %}
# -*- coding: utf-8 -*-
from django import forms
import models


class TopicForm(forms.ModelForm):

    def __init__(self, *args, **kwargs):
        # 这里一定要用pop,而不是get,
        # 因为form的__init__方法并没有"request"这个参数
        self.request = kwargs.pop("request", None)
        super(TopicForm, self).__init__(*args, **kwargs)

    def save(self, commit=True):
        self.instance.create_user = self.request.user
        return super(TopicForm, self).save(commit=True)

    class Meta:
        model = models.Topic
        fields = ['title']
{% endhighlight %}

这里的`fields`,是指定表单里要包含哪些信息,
因为`create_time`是系统自动在创建时添加的,所以不需要.
而`create_user`是因为我们要自动添加为当前用户,
所以也不需要,于是就只需要一个`title`就够了.
这里重写了默认的`save`方法,就是在这里,
我们把当前用户写入了当前的事例,成功的实现了目的.

接着就是`templates`了,
因为我们通过`template_name_suffix`重新定义了其后缀,
所以我们的`templates`如下,其名字为`topic_create_form.html`

{% highlight python %}
{% raw %}
{% block content %}
    <form action="" method="POST">
        {% csrf_token %}
        {{ form.as_p }}
        <input type="submit" value="Create">
    </form>
{% endblock %}
{% endraw %}
{% endhighlight %}

最后是`urls.py`

{% highlight python %}
# -*- coding: utf-8 -*-
from django.conf.urls import url
from django.contrib.auth.decorators import login_required
import views

urlpatterns = [
    url(R'^create_topic/$', login_required(views.TopicCreateView.as_view()), name='create_topic'),
]

{% endhighlight %}

当然,这一切看上去比较简单,但是实际上从头开始操作还是有比较多的问题,
我也是整理了一个思维导图才搞懂这些`View`,`model`和`form`之间的调用关系,
下面是这个思维导图的一个图片版本:

![CreateView.png](/assets/CreateView.png)

