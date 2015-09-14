---
author: shellbye
comments: true
date: 2015-09-13 13:57:08+00:00
layout: post
slug: merge_quick_sort
title: Merge and Quick sort 归并排序与快速排序
categories:
- tech_world
tags:
- algorithms
- data structure
- job
- sort
---

### 归并排序
归并排序与其他简单排序的不同之处在于它用到了分治思想。即把一个大的问题分解为一个个小的问题。
通过求解小问题来解决大问题。在归并排序中，分治体现在它是先把一个个小的序列排好，
然后在将这些排好的小序列合并起来。归并的代码也用到了一个辅助辅助函数，
基本上高级的排序方法都会有自己的辅助函数。具体代码如下：

{% highlight java %}
public static void MergeSort(List<Integer> list, int low, int high) {
	if (high <= low)
		return;
	int mid = (low + high) / 2;
	MergeSort(list, low, mid);
	MergeSort(list, mid+1, high);
	merge(list, low, mid, high);
}

public static void merge(List<Integer> list, int low, int mid, int high) {
	int[] aux = new int[list.size()];
	int i = low, j = mid + 1;
	for (int k = low; k <= high; k++) {
		aux[k] = list.get(k);
	}

	for (int k = low; k <= high; k++) {
		if (i > mid) {
			// 左边用完
			list.set(k, aux[j++]);
		} else if (j > high) {
			// 右边用完
			list.set(k, aux[i++]);
		} else if (aux[j] < aux[i]) {
			// j对应的较小,先放j,然后j后移
			list.set(k, aux[j++]);
		} else {
			list.set(k, aux[i++]);
		}
	}
}
{% endhighlight %}

递归的程序需要注意的一点就是退出条件，在归并排序中，就是`MergeSort`中的前两行代码。

### 快速排序
一个排序算法得有多快才能让它的名字就叫做快速排序呢？
可以参考这篇文章来看看[快排为什么那样快](http://mindhacks.cn/2008/06/13/why-is-quicksort-so-quick/){:target="blank"}。
快排也是分治的算法，与归并不同的是，快排是先分割，然后排序。而归并是先排序，然后归并，提现在代码上就是sort方法的位置：

{% highlight java %}
public static void QuickSort(List<Integer> list, int low, int high) {
	if (low >= high)
		return;
	int mid = partition(list, low, high);
	QuickSort(list, low, mid - 1);
	QuickSort(list, mid + 1, high);
}

public static int partition(List<Integer> list, int low, int high) {
	int i = low, j = high;
	int value = list.get(i);
	while (i < j) {
		while (list.get(i) < value) {
			i++;
		}
		while (list.get(high) > value) {
			high--;
		}
		exchange(list, i, j);
	}
	return j;
}
{% endhighlight %}

快排的重点是`partition`函数，在内部的两个`while`循环中，是分别从前往后找比`value`大的，
和从后往前找比`value`小的，然后交换他们。之前在数据结构课上，每一个`while`循环之后，
都要`exchang`，但是这种方法显然没有上面的代码的简洁且高效，因为它只有一次交换操作。
现在回过头来想，教科书里的两次交换真的没有意义多此一举。  