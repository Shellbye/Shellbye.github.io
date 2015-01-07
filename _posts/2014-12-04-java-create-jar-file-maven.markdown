---
author: shellbye
comments: true
date: 2014-12-04 10:02:51+00:00
layout: post
slug: java-create-jar-file-maven
title: java create jar file with maven
wordpress_id: 1141
categories:
- 技术世界
tags:
- java
- maven
---

在上一篇文章中，记录了用命令行通过jar命令来打jar包的方式，实际的使用中，我们往往要用到很多第三方的jar包，这个时候就要用到maven了。示例如下：

1.配置文件
首先肯定是要有pom.xml文件：

{% highlight YAML %}


    4.0.0

    com.shellbye.wsc.shulianxunying.zhihuuser
    zhihu
    1.0-SNAPSHOT

    
        1.7
        1.7
    

    
        
            
                org.apache.maven.plugins
                maven-shade-plugin
                2.3
                
                    
                        package
                        
                            shade
                        
                        
                            
                                
                                    path.to.your.main
                                
                            
                        
                    
                
            
        
    

    
        
            
        
    

{% endhighlight %}


2.打包
由于我使用的是优秀的IDE jetbrain的IDEA，所以直接点击maven的package就可以了

3.执行

{% highlight YAML %}
java -jar yourjarfile.jar
{% endhighlight %}


我在配置的过程中遇到error: generics are not supported in -source 1.3，耽误了很多时间，后来[3]的指导下解决了，解决的方法就是上面xml文件中的

{% highlight YAML %}

    1.7
    1.7

{% endhighlight %}

参考：

[1].[http://maven.apache.org/plugins/maven-shade-plugin/examples/executable-jar.html](http://maven.apache.org/plugins/maven-shade-plugin/examples/executable-jar.html)

[2].[http://askubuntu.com/questions/101746/how-can-i-execute-a-jar-file-from-the-terminal](http://askubuntu.com/questions/101746/how-can-i-execute-a-jar-file-from-the-terminal)

[3].[http://stackoverflow.com/questions/8750563/maven-doesnt-use-java-7](http://stackoverflow.com/questions/8750563/maven-doesnt-use-java-7)

[4].[http://askubuntu.com/questions/157229/java-home-is-not-defined-correctly](http://askubuntu.com/questions/157229/java-home-is-not-defined-correctly)
