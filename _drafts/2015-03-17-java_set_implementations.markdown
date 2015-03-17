---
author: shellbye
comments: true
date: 2015-03-17 11:27:08+00:00
layout: post
slug: java-set-implementations
title: java set implementations
categories:
- 技术世界
tags:
- jekyll
- java
---

昨天迎来了人生中的“第一次”电话面试，阿里的JAVA面试，可惜因为我的毫无准备，面试简直一团糟，最后自然是悲剧了。
不过好在这只是实习面试，问题暴露的越多越好，这样可以及早的开始弥补，这不，就有了今天的这篇博客，来自我面试的
第一题：

    请说一下Set这个接口的几个常用实现

说实话我对JAVA着实了解不多，要不是面试官说我都不知道Set是接口而不是类，囧。俗话说在哪里跌倒就在哪里爬起来，回
来之后我研究了一下Set，以下就是笔记喽。

首先是JAVA的面向对象的思想，其概念主要有（都是些自己的大致理解，可能有误）：
0.抽象：总结对象的共同特征
1.封装：尽量对外隐藏细节，可以通过private,protected,public等进行控制
2.继承：子类拥有父类的全部
3.多态：同一方法对不同调用给出不同的反应

其实呢，面向对象编程的一个体现就是面向接口，因为JAVA的单继承，使得继承不够灵活，相比之下接口就灵活得多。我在
这里要讨论的Set就是一个接口。

Set实现了Collection和Iterable两个接口，它的实现包含但不限于HashSet, TreeSet, LinkedHashSet. Set是JAVA中
[Collections framework](http://docs.oracle.com/javase/7/docs/technotes/guides/collections/overview.html)的一个接口，
从下表Collection Implementations中可以看到它的实现对应的不同数据结构关系：

Interface |	Hash Table | Resizable Array | Balanced Tree | Linked List | Hash Table + Linked List
Set       | HashSet    |                 | TreeSet       |             | LinkedHashSet
List      |            | ArrayList       |               | LinkedList  |
Deque     |            | ArrayDeque      |               | LinkedList  |
Map       | HashMap    |                 | TreeMap       |             | LinkedHashMap


当然到这里其实面试的问题基本就回答了，但是我就很奇怪为啥Set有这么多的不同的实现，于是从
[http://www.tutorialspoint.com/](http://www.tutorialspoint.com/)找到了这三种不同的实现的代码，跑了下看了看

{% highlight java %}
package interview;

import java.util.HashSet;
import java.util.LinkedHashSet;
import java.util.TreeSet;

/**
 * User: shellbye.com@gmail.com
 * Date: 2015/3/17
 */
public class ImplementationsOfSet {
    public static void main(String args[]) {
        //The three Set below indicate that they are
        //almost the same,only the TreeSet is ordered.

        //http://www.tutorialspoint.com/java/java_hashset_class.htm
        // create a hash set
        HashSet hs = new HashSet();
        // add elements to the hash set
        hs.add("B");
        hs.add("A");
        hs.add("D");
        hs.add("E");
        hs.add("C");
        hs.add("F");
        System.out.println(hs);//[D, E, F, A, B, C]
        //http://www.tutorialspoint.com/java/java_treeset_class.htm
        // Create a tree set
        TreeSet ts = new TreeSet();
        // Add elements to the tree set
        ts.add("C");
        ts.add("A");
        ts.add("B");
        ts.add("E");
        ts.add("F");
        ts.add("D");
        System.out.println(ts);//[A, B, C, D, E, F]
        //http://www.tutorialspoint.com/java/java_linkedhashset_class.htm
        // create a hash set
        LinkedHashSet lhs = new LinkedHashSet();
        // add elements to the hash set
        lhs.add("B");
        lhs.add("A");
        lhs.add("D");
        lhs.add("E");
        lhs.add("C");
        lhs.add("F");
        System.out.println(lhs);//[B, A, D, E, C, F]
    }
}
{% endhighlight %}

看到TreeSet的打印我突然明白了，这不同的实现不是为了别的，只是为了不同的使用场景，比如如果是为了方便查找，
那么TreeSet或许是一种比较有利的方式，同样，不同的实现都有其存在的意思，具体在实践中选择谁则是由程序员自己决定的。