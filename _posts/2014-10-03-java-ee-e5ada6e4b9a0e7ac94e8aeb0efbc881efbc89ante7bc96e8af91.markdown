---
author: shellbye
comments: true
date: 2014-10-03 01:32:39+00:00
layout: post
slug: java-ee-%e5%ad%a6%e4%b9%a0%e7%ac%94%e8%ae%b0%ef%bc%881%ef%bc%89ant%e7%bc%96%e8%af%91
title: JAVA EE 学习笔记（1）ANT编译
wordpress_id: 1059
categories:
- 技术世界
tags:
- ant
- java ee
---

最近在看[《轻量级Java EE企业应用实战》](http://book.douban.com/subject/3333726/)学习javaee，今天遇到这样一个问题，就是build.xml输出build successful，但是classes里面什么都没有，我的build.xml文件如下，ant使用的命令就是简单的一句
    
    ant




    
    
    
    <project default="" basedir="." name="webDemo">
    	
    	<property name="src" value="src"></property>
    	<property name="classes" value="classes"></property>
    	
    	<path id="classpath">
    		<fileset dir="lib">
    			<include name="*.jar"></include>
    		</fileset>
    		<pathelement path="${classes}"></pathelement>
    	</path>
    	
    	<target name="compile" description="编译Java源文件">
    		
    		<delete dir="${classes}"></delete>
    		
    		<mkdir dir="${classes}"></mkdir>
    		
    		<javac deprecation="false" includeantruntime="yes" destdir="${classes}" failonerror="true" debug="true" optimize="false">
    			
    			<src path="${src}"></src>
    			
    			<classpath refid="classpath"></classpath>
    		</javac>
    	</target>
    </project>



后来在[这里](http://bbs.csdn.net/topics/190037193)找到了一个原因，原来并不是所有的build.xml文件都是可以只用ant命令之间编译的，需要project里面有default="compile"，或者使用
    
    ant compile

命令来编译，此时就会输入如下结果

    
    
    C:\xampp\tomcat\webapps\webDemo\WEB-INF>ant compile
    Buildfile: C:\xampp\tomcat\webapps\webDemo\WEB-INF\build.xml
    
    compile:
    [delete] Deleting directory C:\xampp\tomcat\webapps\webDemo\WEB-INF\classes
    [mkdir] Created dir: C:\xampp\tomcat\webapps\webDemo\WEB-INF\classes
    [javac] Compiling 1 source file to C:\xampp\tomcat\webapps\webDemo\WEB-INF\classes
    
    BUILD SUCCESSFUL
    Total time: 1 second
    


查看classes，发现想要的类文件都生成好了。
