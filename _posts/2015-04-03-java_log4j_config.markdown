---
author: shellbye
comments: true
date: 2015-04-03 10:27:08+00:00
layout: post
slug: java_log4j_config
title: java log4j config
categories:
- tech_world
tags:
- java
- log4j
---


今天为了给别人的项目配置日志，参考了很早之前写的关于log4j2的[一篇笔记](/blog/tech_world/jfianl-e9858de7bdae-log4j2-e7ac94e8aeb0/)。
当我配置了半天之后才发现原来这个项目里有自己的日志，但是用的不是2而是log4j，所以我参考着一些资料配置了一下老版本的log4j。

首先是配置文件log4j.xml，相比新版，也就是少了个2而已

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">

<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
    <appender name="console" class="org.apache.log4j.ConsoleAppender">
        <param name="Target" value="System.out"/>
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%-5p %c{1} - %m%n"/>
        </layout>
    </appender>
    <appender name="util" class="org.apache.log4j.FileAppender">
        <param name="File" value="util.log" />
        <param name="Append" value="true" />
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%d{yyyy MM dd HH:mm:ss} %-5p %l - %m%n"/>
        </layout>
    </appender>

    <root>
        <priority value ="debug" />
        <appender-ref ref="util" />
    </root>

</log4j:configuration>
{% endhighlight %}

这个文件应该也是要放在resources目录里面，接着就可以在代码中如下使用：

{% highlight java %}
public class Main {

    public static void main(String[] args) {
        org.apache.log4j.Logger log = org.apache.log4j.Logger.getLogger(Main.class);
        log.error("This is a error message");
    }
}

{% endhighlight %}

上面的配置会在项目根目录自动生成 ```util.log``` 文件，并记录日志。

参考：

[1].[https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/PatternLayout.html](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/PatternLayout.html)

[2].[http://wiki.apache.org/logging-log4j/FileAppender](http://wiki.apache.org/logging-log4j/FileAppender)