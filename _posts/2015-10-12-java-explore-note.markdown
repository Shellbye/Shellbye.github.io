---
author: shellbye
comments: true
date: 2015-10-12 11:27:08+00:00
layout: post
slug: java-explore-note
title: java explore note
categories:
- tech_world
tags:
- java
- note
---

> 下面是在读InfoQ的迷你书的一些笔记，原书见这里：
> [http://www.infoq.com/cn/minibooks/java-explore](http://www.infoq.com/cn/minibooks/java-explore){:target="_blank"}

1. Java 类的加载  
Java类的加载是由类加载器来完成的。
一般来说,类加载器分成两类:启动类加载器(bootstrap)和用户自定义的类加载器(user-defined)。
两者的区别在于启动类 加载器是由JVM的原生代码实现的,
而用户自定义的类加载器都继承自Java中 的 java.lang.ClassLoader类  
类加载器需要完成的最终功能是定义一个Java类,即把Java字节代码转换成JVM中的 java.lang.Class类的对象  
类加载器的一个重要用途是在JVM中为相同名称的Java类创建隔离空间。
在JVM中, 判断两个类是否相同,不仅是根据该类的 二进制名称 ,还需要根据两个类的定义类 加载器。
只有两者完全一样,才认为两个类的是相同的  

2. Java 类的链接  
Java类的链接指的是将Java类的二进制代码合并到JVM的运行状态之中的过程  
类的链接包括验证、准备和解析等几个步骤。  
1.验证是用来确保Java类的二进制表示在结构上是完全正确的。如果验证过程出现错误 的话,会抛出 java.lang.VerifyError错误。  
2.准备过程则是创建Java类中的静态域,并将 这些域的值设为默认值。准备过程并不会执行代码。  
在一个Java类中会包含对其它 类或接口的形式引用,包括它的父类、所实现的接口、方法的形式参数和返回值的 Java类等。  
3.解析的过程就是确保这些被引用的类能被正确的找到。解析的过程可能 会导致其它的 Java类被加载。  

3. Java 类的初始化  
当一个 Java 类第一次被真正使用到的时候,JVM 会进行该类的初始化操作。
初始化 过程的主要操作是执行静态代码块和初始化静态域  

4. 可见性  
在多线程程序中,如果不使用一定的同步机制,就不能保证一个线程所写入的值对另 外一个线程是可见的。
造成这种情况的原因可能有下面几个:  
1.CPU 内部的缓存:现在的 CPU 一般都拥有层次结构的几级缓存。
CPU 直接操作 的是缓存中的数据,并在需要的时候把缓存中的数据与主存进行同步。
因此在 某些时刻,缓存中的数据与主存内的数据可能是不一致的。
某个线程所执行的 写入操作的新值可能当前还保存在 CPU 的缓存中,
还没有被写回到主存中。这 个时候,另外一个线程的读取操作读取的就还是主存中的旧值。  
2.CPU 的指令执行顺序:在某些时候,CPU 可能改变指令的执行顺序。
这有可能 导致一个线程过早的看到另外一个线程的写入操作完成之后的新值。  
3.编译器代码重排:出于性能优化的目的,编译器可能在编译的时候对生成的目 标代码进行重新排列。  

5. 类型系统  
通过继承机制而产生的类型体系结构，根据 Liskov替换原则 ,子类是可以替换父类的。
当需要Object类的引用的 时候,如果传入一个String对象是没有任何问题的。
但是反过来的话,即用父类的引 用替换子类引用 的时候,就需要进行强制类型转换  
引入泛型之后的类型系统增加了两个维度:一个是类型参数自身的继承体系结构, 
另外一个是泛型类或接口自身的继承体系结构。
第一个指的是对于 List<String>和 List<Object>这样的情况,
类型参数 String 是继承自 Object 的。
而第二种指的是 List 接口继承自 Collection 接口。
对于这个类型系统,有如下的一些规则:  
1.相同类型参数的泛型类的关系取决于泛型类自身的继承体系结构。
即 List<String> 是 Collection<String> 的 子 类 型 , 
List<String> 可 以 替 换 Collection<String>。
这种情况也适用于带有上下界的类型声明  
2.当泛型类的类型声明中使用了通配符的时候,其子类型可以在两个维度上分别 展开。
如对 Collection<? extends Number>来说,
其子类型可以在 Collection 这个 维度上展开,即 List<? extends Number>和 Set<? extends Number>等;
也可以在 Number 这个层次上展开,即 Collection<Double>和 Collection<Integer>等。
如此 循环下去,ArrayList<Long>和 HashSet<Double>等也都算是 Collection<? extends Number>的子类型  

6. 由于类型擦除机制,类型参 数并不能用来创建对象或是作为静态变量的类型  

