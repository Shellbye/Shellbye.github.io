---
author: shellbye
comments: true
date: 2014-12-01 07:02:45+00:00
layout: post
slug: java-create-jar-file
title: java create jar file
wordpress_id: 1132
categories:
- tech_world
tags:
- java
---

新建可执行jar文件
1.Main.java

    
    public class Main {
        public static void main(String[] args) throws Exception {
            String a = "aaaaaaa";
            System.out.println(a);
        }
    }



2.manifest.txt

    
    
    Main-Class: Main
    


注意，manifest.txt最后要以空行结尾，且Main-Class:之后要有一个空格，不然就会报错如下：

    
    java.io.IOException: invalid header field



3.编译生成Main.class文件

    
    javac Main.java


生成Main.class文件，目录此时如下：

    
    Main.java
    Main.class
    manifest.txt



4.生成Main.jar文件

    
    jar -cvfm Main.jar manifest.txt Main.class



5.修改Main.jar文件执行权限

    
    sudo chmod u+x Main.jar



6.执行jar文件

    
    ./Main.jar
    # output: aaaaaaa


参考：
[0].[http://www.skylit.com/javamethods/faqs/javaindos.html](http://www.skylit.com/javamethods/faqs/javaindos.html)
[1].[http://www.mkyong.com/java/how-to-make-an-executable-jar-file/](http://www.mkyong.com/java/how-to-make-an-executable-jar-file/)
[2].[https://docs.oracle.com/javase/tutorial/deployment/jar/build.html](https://docs.oracle.com/javase/tutorial/deployment/jar/build.html)
[3].[https://docs.oracle.com/javase/tutorial/deployment/jar/appman.html](https://docs.oracle.com/javase/tutorial/deployment/jar/appman.html)
