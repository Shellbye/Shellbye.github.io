---
author: shellbye
comments: true
date: 2015-09-13 08:57:08+00:00
layout: post
slug: bubble_sort
title: Bubble sort 冒泡排序
categories:
- tech_world
tags:
- algorithms
- data structure
- job
- sort
---

冒泡排序几乎是所有排序里被认为最简单的，但是其实冒泡排序也是有不少坑的，我今天大致折腾了一下，发现了不少坑。  
首先看《大话数据结构》给出的冒泡排序的第一版（作者说了，这个其实不是正宗冒泡，因为它没有两两对比，
下面的算法其实是简单交换排序）：
{% highlight java %}
public static void BubbleSortV0(List<Integer> list) {
    for (int i = 0; i < list.size(); i++) {
        for (int j = i + 1; j < list.size(); j++) {
            if (less(list, j, i))
                exchange(list, j, i);
        }
    }
}
{% endhighlight %}

其中辅助函数如下：

{% highlight java %}
public static boolean less(List<Integer> list, int i, int j) {
    return list.get(i) <= list.get(j);
}

public static void exchange(List<Integer> list, int i, int j) {
    int temp = list.get(i);
    list.set(i, list.get(j));
    list.set(j, temp);
}
{% endhighlight %}

那么，为啥说这个排序不是正宗的冒泡排序呢？因为它其实并没有体现冒泡的思想，所谓冒泡，是前后两两比对，一轮排好一位。
上面的V0算法也是每次排好一位，但是它的排序方法不是两两比对，而是用i后面的每一个和i比较，如果小于i，则交换。
它在思想上其实更接近简单选择排序。  
那么，正宗的冒泡排序是什么样的呢？请看下面的程序：  

{% highlight java %}
public static void BubbleSort(List<Integer> list) {
    for (int i = 0; i < list.size(); i++) {
        for (int j = 0; j < list.size() - 1 - i; j++) {
            if (less(list, j + 1, j))
                exchange(list, j + 1, j);
        }
    }
}
{% endhighlight %}

正宗的冒泡排序和V0版本的差别有以下几点：  
1. 内层循环从0开始，而不是从i＋1开始，这是因为每次排好之后，只是后面的最大的排好了，前面的较小的还没有排好，所以必须继续从零开始。
内层循环的判断条件中后面的－i，意思是后面的i位已经排好，不需要再进行遍历（后面的是较大的，最先排好）。  
2. 比较大小的值所在位置不同。正宗冒泡的比较体现了冒泡的思想：前后两两比较；而V0版本则是内层循环的每一个都和外层的第i位比较。  

当然，冒泡排序也可以先排好较小的，然后再排较大的，只要修改内层循环的方向即可，代码如下：

{% highlight java %}
public static void BubbleSortSmallFirst(List<Integer> list) {
    for (int i = 0; i < list.size(); i++) {
        for (int j = list.size() - 1; j > i; j--) {
            if (less(list, j, j - 1))
                exchange(list, j, j - 1);
        }
    }
}
{% endhighlight %}

这里内层循环的判断条件j>i也是为了比较多余的比较，其实j>0也可以正确排序，但是这样就浪费了不少时间。  

冒泡排序之所以费事，不仅仅因为它每次只排好一位，而且还在于它把很多比较的结果都浪费掉了。
快排之所以快，就是因为每次的比较都被充分的利用了起来。对冒泡的优化，主要是避免对已经有序的序列进行比较。
代码如下，优化的方法就是判断每一次的排序中是否有元素交换，如果没有，则证明数组已经有序，所以可以提前退出。  

{% highlight java %}
public static void BubbleSortWithFlag(List<Integer> list) {
    boolean needSort = true;
    for (int i = 0; i < list.size() && needSort; i++) {
        needSort = false;
        for (int j = 0; j < list.size() - 1 - i; j++) {
            if (less(list, j + 1, j)) {
                needSort = true;
                exchange(list, j + 1, j);
            }
        }
    }
}
{% endhighlight %}
