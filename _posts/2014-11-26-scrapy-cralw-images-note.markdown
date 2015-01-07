---
author: shellbye
comments: true
date: 2014-11-26 03:41:21+00:00
layout: post
slug: scrapy-cralw-images-note
title: scrapy cralw images note
wordpress_id: 1123
tags:
- crawl
- Python
- scrapy
---

最近因项目需求写了点爬虫的东西，研究了下python的一个爬虫框架[scrapy](http://scrapy.org/)，在研究[图片自动](http://doc.scrapy.org/en/0.24/topics/images.html)[下载](http://doc.scrapy.org/en/0.24/topics/images.html)的时候，遇到一个问题，解决如下：

1.首先是items.py

{% highlight python %}

import scrapy

class MyImageItem(scrapy.Item):
    image_urls = scrapy.Field()
    images = scrapy.Field()

{% endhighlight %}

2.然后是settings.py需要添加如下几行：

{% highlight python %}

ITEM_PIPELINES = {
    'scrapy_mongodb.MongoDBPipeline': 300,
    'scrapy.contrib.pipeline.images.ImagesPipeline': 1,
}

IMAGES_STORE = '/home/shellbye/Pictures'

IMAGES_THUMBS = {
    'small': (50, 50),
    'big': (270, 270),
}

{% endhighlight %}

3.最后是爬虫image.py

{% highlight python %}

# -*- coding: utf-8 -*-
import scrapy
from ..items import MyImageItem
class CategorySpider(scrapy.Spider):
name = "image"
allowed_domains = ["douban.com"]
start_urls = (
    'http://www.douban.com/',
)

def parse(self, response):
    item_list = []
    for a in response.xpath("//img"):
        item = MyImageItem()
        # image_urls is not a single image url but multi of them
        # notice that the image url need to be full path
        item['image_urls'] = [a.xpath("@src").extract()[0]]
        item_list.append(item)
    return item_list

{% endhighlight %}


我的问题就是出在爬虫的倒数第三行，本来是这样写的：

{% highlight python %}
item['image_urls'] = a.xpath("@src").extract()[0]
{% endhighlight %}


log里面会报错如下：

{% highlight YAML %}
exceptions.ValueError: Missing scheme in request url: h
{% endhighlight %}

参考了[stackoverflow.com上面的这个问答](http://stackoverflow.com/questions/8773732/downloading-pictures-with-scrapy)，并且看了[scrapy源码](https://github.com/scrapy/scrapy/blob/master/scrapy/contrib/pipeline/images.py#L109)之后发现原来这个item的image_urls是一个字典，难怪要在url外面加[]，这也就解释了为什么不加[]会报上面的错，因为它把图片的链接"http://t.douban.com/img/files/file-1400831012.jpg"当作了一个字典，第一个元素自然是"h",而它想要的是http一样的协议名，所以报错如上。


参考：

[1].[http://stackoverflow.com/questions/8773732/downloading-pictures-with-scrapy](http://stackoverflow.com/questions/8773732/downloading-pictures-with-scrapy)
