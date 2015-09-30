---
author: shellbye
comments: true
date: 2015-09-05 11:37:08+00:00
layout: post
slug: java_note_design_pattern
title: Java note design_pattern
categories:
- tech_world
tags:
- java
- job
---

### 设计模式
1. singleton pattern 单例模式  
1.基础版：
{% highlight java linenos %}
public class ClassicSingleton {
	private static ClassicSingleton instance = null;
	protected ClassicSingleton() {
		// Exists only to defeat instantiation.
	}
	public static ClassicSingleton getInstance() {
		if(instance == null) {
			instance = new ClassicSingleton();
		}
		return instance;
	}
}
{% endhighlight %}  
2.双重检查版：
{% highlight java linenos %}
public class Singleton {
	private static Singleton instance = null;
	protected Singleton() {
		// Exists only to defeat instantiation.
	}
	public static Singleton getInstance() {
		if(singleton == null) {
			synchronized(Singleton.class) {
				if(singleton == null) {
					singleton = new Singleton();
				}
			}
		}
		return singleton;
	}
}
{% endhighlight %}  
3.简洁版：
{% highlight java linenos %}
public class SimpleSingleton {
	public final static SimpleSingleton INSTANCE = new SimpleSingleton();
	private SimpleSingleton() {
		// Exists only to defeat instantiation.
	}
}
{% endhighlight %}  
4.枚举版：
{% highlight java linenos %}
public enum EnumSingleton {
    INSTANCE;
    public void someOtherMethod() {
        //
	}
}
{% endhighlight %}  
参考：  
[http://www.javaworld.com/article/2073352/core-java/simply-singleton.html](http://www.javaworld.com/article/2073352/core-java/simply-singleton.html){:target="_blank"}  
[http://www.blogjava.net/kenzhh/archive/2013/03/15/357824.html](http://www.javaworld.com/article/2073352/core-java/simply-singleton.html){:target="_blank"}

