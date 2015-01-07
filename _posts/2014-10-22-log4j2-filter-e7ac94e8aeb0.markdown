---
author: shellbye
comments: true
date: 2014-10-22 13:14:53+00:00
layout: post
slug: log4j2-filter-%e7%ac%94%e8%ae%b0
title: log4j2 filter 笔记
wordpress_id: 1078
categories:
- 技术世界
tags:
- log4j2
---

在[上一篇文章](http://www.shellbye.com/blog/%E6%8A%80%E6%9C%AF%E4%B8%96%E7%95%8C/jfianl-%E9%85%8D%E7%BD%AE-log4j2-%E7%AC%94%E8%AE%B0/)中，简单记录了log4j2的基本使用，下面记录下log4j2的filter的使用方法

log4j2.xml文件：

{% highlight YAML %}


    
        
            
                %d %p %logger{36} %msg%n
            
        
        
        
            
            
            
            
            
            
            
                %date %p %logger{36} %line %method %msg%n
            
        
    
    
        
            
        
        
            
        
    


{% endhighlight %}


注释中的：
第一个filter，ThresholdFilter比较容易理解，即level大于等于ERROR才会记录，比如下面的代码，只有222会记录在日志中：

{% highlight java %}
Logger logger = LogManager.getLogger("admin");
loggor.debug("111");
logger.error("222");
logger.info("333");
{% endhighlight %}


第二个filter，RegexFilter也比较容易理解，就是日志内容匹配了正则才会记录，比如如下代码，只有333 test 333会被记录：

{% highlight java %}
Logger logger = LogManager.getLogger("admin");
loggor.debug("111444");
logger.error("222333");
logger.info("333 test 333");
{% endhighlight %}


第三个filter， MarkerFilter比较不容易理解，因为Marker是log4j2里面的一个类型，咋看之下确实摸不着头脑，看过Maker的[文档](http://logging.apache.org/log4j/2.0/manual/markers.html)之后就懂了, 下面的代码，只有有SQL这个Maker的日志才会被记录：

{% highlight java %}
Logger logger = LogManager.getLogger("admin");
Marker SQL_MARKER = MarkerManager.getMarker("SQL");
logger.info("222222222222");
        logger.fatal(SQL_MARKER, "333333333");
{% endhighlight %}


其余filter可以参考[官方文档](http://logging.apache.org/log4j/2.0/manual/filters.html)。
