---
author: shellbye
comments: true
date: 2014-10-22 09:02:20+00:00
layout: post
slug: jfianl-配置-log4j2-笔记
title: jfianl 配置 log4j2 笔记
wordpress_id: 1073
categories:
- tech_world
tags:
- jfianl
- log4j2
---

首先是pom.xml文件添加如下依赖(其他包管理方式参考[这里](http://logging.apache.org/log4j/2.x/maven-artifacts.html))：

    
    
    <dependency>
        <groupid>org.apache.logging.log4j</groupid>
        <artifactid>log4j-api</artifactid>
        <version>2.0.2</version>
    </dependency>
    <dependency>
        <groupid>org.apache.logging.log4j</groupid>
        <artifactid>log4j-core</artifactid>
        <version>2.0.2</version>
    </dependency>



然后是log4j2的配置文件log4j2.xml(其他文件格式参考[这里](http://logging.apache.org/log4j/2.x/manual/configuration.html#AutomaticConfiguration))如下：

    
    
    <configuration status="error">
        <appenders>
            <console name="Console" target="SYSTEM_OUT">
                <patternlayout>
                    <pattern>%d %p %logger{36} %msg%n</pattern>
                </patternlayout>
            </console>
            
            <file name="File" filename="../webapps/your_project.log">
                <patternlayout>
                    <pattern>%date %p %logger{36} %line %method %msg%n</pattern>
                </patternlayout>
            </file>
        </appenders>
        <loggers>
            <logger name="admin" level="info">
                <appenderref ref="File"></appenderref>
            </logger>
            <root level="error">
                <appenderref ref="Console"></appenderref>
            </root>
        </loggers>
    </configuration>



这里需要注意
1.在Appenders里的File元素中，其属性fileName如果不是从根目录开始的话就是从tomcat/bin/开始的，所以配置的时候我就选择了如上配置方式。
2.根据我的测试结果，这个log4j2.xml文件在jfianl项目中只有放到src/resources下面才会起作用。可能这个是可以配置的，但是我这里就把这个文件放到这里了。
3.PatternLayout里面的Pattern的每个参数的意思可以参考这里的[官方文档](http://logging.apache.org/log4j/2.0/manual/layouts.html#Patterns)。

接着就可以在controller或者其他需要日志功能的地方使用了，如下：

    
    
    Logger logger = LogManager.getLogger("admin");
    logger.error("this is a test!");
    



参考文档
1.[http://logging.apache.org/log4j/2.x/](http://logging.apache.org/log4j/2.x/)
