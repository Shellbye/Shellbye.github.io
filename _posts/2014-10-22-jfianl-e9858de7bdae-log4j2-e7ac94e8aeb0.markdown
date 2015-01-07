---
author: shellbye
comments: true
date: 2014-10-22 09:02:20+00:00
layout: post
slug: jfianl-%e9%85%8d%e7%bd%ae-log4j2-%e7%ac%94%e8%ae%b0
title: jfianl 配置 log4j2 笔记
wordpress_id: 1073
categories:
- 技术世界
tags:
- jfianl
- log4j2
---

首先是pom.xml文件添加如下依赖(其他包管理方式参考[这里](http://logging.apache.org/log4j/2.x/maven-artifacts.html))：

{% highlight YAML %}


    org.apache.logging.log4j
    log4j-api
    2.0.2


    org.apache.logging.log4j
    log4j-core
    2.0.2

{% endhighlight %}


然后是log4j2的配置文件log4j2.xml(其他文件格式参考[这里](http://logging.apache.org/log4j/2.x/manual/configuration.html#AutomaticConfiguration))如下：

{% highlight YAML %}


    
        
            
                %d %p %logger{36} %msg%n
            
        
        
        
            
                %date %p %logger{36} %line %method %msg%n
            
        
    
    
        
            
        
        
            
        
    

{% endhighlight %}


这里需要注意
1.在Appenders里的File元素中，其属性fileName如果不是从根目录开始的话就是从tomcat/bin/开始的，所以配置的时候我就选择了如上配置方式。
2.根据我的测试结果，这个log4j2.xml文件在jfianl项目中只有放到src/resources下面才会起作用。可能这个是可以配置的，但是我这里就把这个文件放到这里了。
3.PatternLayout里面的Pattern的每个参数的意思可以参考这里的[官方文档](http://logging.apache.org/log4j/2.0/manual/layouts.html#Patterns)。

接着就可以在controller或者其他需要日志功能的地方使用了，如下：

{% highlight java %}

Logger logger = LogManager.getLogger("admin");
logger.error("this is a test!");

{% endhighlight %}


参考文档
1.[http://logging.apache.org/log4j/2.x/](http://logging.apache.org/log4j/2.x/)
