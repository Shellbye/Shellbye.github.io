---
author: shellbye
comments: true
date: 2014-12-01 07:02:45+00:00
layout: post
slug: java-create-jar-file
title: java create jar file
wordpress_id: 1132
categories:
- 技术世界
tags:
- java
---

新建可执行jar文件
1.Main.java

{% highlight java %}
public class Main {
    public static void main(String[] args) throws Exception {
        String a = "aaaaaaa";
        System.out.println(a);
    }
}
{% endhighlight %}


2.manifest.txt

{% highlight YAML %}

Main-Class: Main

{% endhighlight %}

注意，manifest.txt最后要以空行结尾，且Main-Class:之后要有一个空格，不然就会报错如下：

{% highlight YAML %}
java.io.IOException: invalid header field
{% endhighlight %}


3.编译生成Main.class文件

{% highlight YAML %}
javac Main.java
{% endhighlight %}

生成Main.class文件，目录此时如下：

{% highlight YAML %}
Main.java
Main.class
manifest.txt
{% endhighlight %}


4.生成Main.jar文件

{% highlight YAML %}
jar -cvfm Main.jar manifest.txt Main.class
{% endhighlight %}


5.修改Main.jar文件执行权限

{% highlight YAML %}
sudo chmod u+x Main.jar
{% endhighlight %}


6.执行jar文件

{% highlight YAML %}
./Main.jar
# output: aaaaaaa
{% endhighlight %}

参考：
[0].[http://www.skylit.com/javamethods/faqs/javaindos.html](http://www.skylit.com/javamethods/faqs/javaindos.html)
[1].[http://www.mkyong.com/java/how-to-make-an-executable-jar-file/](http://www.mkyong.com/java/how-to-make-an-executable-jar-file/)
[2].[https://docs.oracle.com/javase/tutorial/deployment/jar/build.html](https://docs.oracle.com/javase/tutorial/deployment/jar/build.html)
[3].[https://docs.oracle.com/javase/tutorial/deployment/jar/appman.html](https://docs.oracle.com/javase/tutorial/deployment/jar/appman.html)
