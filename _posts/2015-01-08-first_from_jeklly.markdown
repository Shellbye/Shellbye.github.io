---
author: shellbye
comments: true
date: 2015-01-08 11:27:08+00:00
layout: post
slug: first-blog-from-jekyll
title: First blog from jekyll
wordpress_id: 1099
categories:
- 技术世界
tags:
- jekyll
---

After two day work, my Wordpress now migrate to github page.

New site now looks like this:

![My new site](/assets/Screenshot.png)

Let's say how does the highlight works:

{% highlight python %}
def index(request):
    return request.user
{% endhighlight %}

Looks well, how about with line numbers:

{% highlight python linenos %}
def index(request):
    if request.user:
        return True
    else:
        return False
{% endhighlight %}