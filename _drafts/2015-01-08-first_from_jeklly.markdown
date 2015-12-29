---
author: shellbye
comments: true
date: 2015-12-28 11:27:08+00:00
layout: post
slug: first-blog-from-jekyll
title: First blog from jekyll
categories:
- tech_world
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

This is a link about [Wiki]{:target="_blank"}.
This ia a link in my owe [site]{:target="_blank"}.

[Wiki]:https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Common_non-standard_request_fields
[site]:/blog/poet/
