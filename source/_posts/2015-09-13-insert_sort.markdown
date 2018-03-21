---
author: shellbye
comments: true
date: 2015-09-13 09:57:08+00:00
layout: post
slug: insert_sort
title: Insert sort 插入排序
categories:
- tech_world
tags:
- algorithms
- data structure
- job
- sort
---

### 插入排序
直接插入排序应该算法思想简单程度仅次于[冒泡排序](/blog/tech_world/bubble_sort/){:target="_blank"}的简单算法了。
但是想要一次性写对也是有一定的难度的。下面就记录下一些需要注意的地方：

{% highlight java %}
public static void InsertSort(List<Integer> list) {
    for (int i = 1; i < list.size(); i++) {
        if (less(list, i, i - 1)) {
            int j;
            int temp = list.get(i);
            for (j = i - 1; j >=0 && list.get(j) > temp; j--) {
                list.set(j + 1, list.get(j));
            }
            list.set(j + 1, temp);
        }
    }
}
{% endhighlight %}

其中的辅助函数`less()`见[这里](/blog/tech_world/bubble_sort/){:target="_blank"}  
看起来很简单，但是还是有一些需要注意的地方。  
1. 在内层循环中的条件判断中，一定要确保j不能为负。  
2. 在内层循环结束后，将temp赋值给第j＋1个位置，而不是j，
因为在内层循环中每次都是把j＋1这个位置空了出来。所以temp要赋值给j+1。  
3. 外循环的i一定要在temp中暂存，因为内循环的向后移动第一次就会覆盖掉i所在的值。  
4. 还有需要注意的就是外层循环是从第二个元素开始的，因为第一个元素自身是有序的。  

在《算法》这本书里，插入排序用的实现细节不是很一样，下面看代码：

{% highlight java %}
public static void InsertSortV1(int[] a) {
	int N = a.length;
	for (int i = 1; i < N; i++) {
		for (int j = i; j > 0 && less(a[j], a[j - 1]); j--) {
			exch(a, j, j - 1);
		}
	}
}
{% endhighlight %}

辅助函数如下：

{% highlight java %}
public static boolean less(int i, int j) {
	return i < j;
}

public static void exch(int[] c, int i, int j) {
	int temp = c[i];
	c[i] = c[j];
	c[j] = temp;
}
{% endhighlight %}

与原始的插入排序相比，V1版本的插入排序要简洁的多，看上去也更加优雅。
那么问题来了，为什么V1版本的要优雅的多呢？首先显而易见的是，
V1版本的内层循环是从i开始，而不是从i－1开始，这样的好处就是，
它可以不用设置temp哨兵，每次都是简单的前后交换。不存在一个覆盖的过程，
所以也就不需要temp来保存值。而且这样的交换还可以利用辅助函数`exch`，
而不像原始版本那样，交换过程还得手动完成。  

插入排序有个特别适用的场景，就是当数组部分有序时，它会特别的快。
倒置指的是数组中的两个顺序颠倒的元素。如果数组中倒置的数量小于数组的大小的某个倍数，
那么我们就说这个数组是部分有序的。下面是几种典型的部分有序数组：  
1. 数组中每个元素距离它的最终位置都不远；  
2. 一个有序的大数组接一个小数组；
3. 数组中只有几个元素的位置不正确  
插入排序对以上情况会进行的非常快。  

### 希尔排序
希尔排序是插入排序的升级版，其思想与插入排序是一样的，
不同的是它比较的不仅仅是前后相邻的，而是从一个很大的跨度上渐渐缩小。  

{% highlight java %}
public static void ShellSort(List<Integer> list) {
	int n = list.size() / 3;
	while (n >= 1) {
		for (int i = n; i < list.size(); i++) {
			if (less(list, i, i - n)) {
				for (int j = i; j >=n && less(list, j, j-n); j -= n) {
					exchange(list, j, j-n);
				}
			}
		}
		n = n/3;
	}
}
{% endhighlight %}

希尔排序需要注意的内层循环，循环遍历j的自减不再是以1为单位，而是以步长n为单位，
并且在比较大小时也不是比较前后两位，而是步长单位的前后两位，所以`less`的参数也是减步长n。  
希尔排序的外层循环也不是从0开始，而是从步长开始，因为它是从后往前模拟插入排序，
往前的逻辑是在内层循环中完成的，外层只是控制起始位置，与插入排序类似。  
