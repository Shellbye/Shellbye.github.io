---
author: shellbye
comments: true
date: 2015-11-17 12:23:08+00:00
layout: post
slug: django-static-url
title: django static_url
categories:
- tech_world
tags:
- django
---

好久没有接触Django开发了,虽然之前用过很久,但是因为Python的工作机会不是很多,
于是找工作期间主要复习Java,Django和Python都放下太久了.有意思的是,
尽管我找工作期间一直在复习Java,可是最后签约的[好未来]{:target="_blank"}却恰恰需要的是我的Python+Django技能.
这不,又开始重新拾起Django,第一天就遇到一个问题.

为了加载静态文件,Django的默认`settings.py`里已经预设了如下配置:

{% highlight python %}
# Static files (CSS, JavaScript, Images)
# https://docs.djangoproject.com/en/1.8/howto/static-files/

STATIC_URL = '/static/'
{% endhighlight %}

然后我的项目根目录的`templates`里面,有如下的html语句(`base.html`):

{% highlight html %}
{% raw  %}{% load staticfiles %}{% endraw %}
<!-- 其他html语句 -->
<script type="text/javascript" src= "{%raw%}{% static "jquery-1.11.3.js" %}{%endraw%}"></script>
{% endhighlight %}

`jquery-1.11.3.js`这个文件是在`static`目录中,
但是直接在浏览器访问`http://127.0.0.1:8000/static/jquery-1.11.3.js`,
却总是404,最后查看[官方文档]{:target="_blank"},才意识到原来是自己没有设置`STATICFILES_DIRS`,
倒置Django不知道哪里去找静态文件,所以404,于是在`settings.py`里加了如下语句就可以正常工作了:

{% highlight python %}
STATICFILES_DIRS = (
    os.path.join(BASE_DIR, "static"),
)
{% endhighlight %}

那么,问题是,为啥官方文档没有直接把`STATICFILES_DIRS`设置好呢?仔细看了下官方文档,
发现其实这个`static`目录并不是为整个项目服务的,即根目录的`templates`是无法自动识别
`static`目录的,这个目录其实是为单独的app服务的,即该目录是应该放在app目录下,
与`migrations`和目录级别的`templates`服务的,在这种情况下,就不需要`STATICFILES_DIRS`这个设置了.
那么,`STATICFILES_DIRS`是为什么设置的呢?官网如是说:

> Your project will probably also have static assets that aren’t tied to a particular app.


即不是服务与单独的app的,而是类似`jquery`这种是服务于整个项目的静态文件,才需要用到.

那么,如果我设置了`STATICFILES_DIRS`为某目录,比如`static01`,其中有img.jpg,
又在app的`static`目录下也有img.jpg,那么,会加载哪个文件呢?

根据[`STATICFILES_FINDERS`]{:target="_blank"}的默认设置,是会优先从`STATICFILES_DIRS`中寻找的.

[好未来]:http://www.100tal.com/
[官方文档]:https://docs.djangoproject.com/en/1.8/howto/static-files/
[`STATICFILES_FINDERS`]:https://docs.djangoproject.com/en/1.8/ref/settings/#std:setting-STATICFILES_FINDERS