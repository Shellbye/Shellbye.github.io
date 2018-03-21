---
author: shellbye
comments: true
date: 2015-09-15 12:57:08+00:00
layout: post
slug: sort_conclude
title: Sort conclude 排序总结
categories:
- tech_world
tags:
- algorithms
- data structure
- job
- sort
---

###稳定性
算法的稳定性指的在排序后的序列和原始序列相比，关键字相同的元素的位置是否会发生变化，
若不变，则算法是稳定的，反之则不稳定。  
稳定的算法：  
低级算法（平均时间复杂度O(n*n)）基本都稳定:冒泡排序，简单选择排序，直接插入排序；  
高级算法（平均时间复杂度O(nlgn)）中,只有归并排序是稳定的。  
不稳定的算法：  
都是高级算法：希尔排序，堆排序，快速排序。  

###时间复杂度
============平均情况=========最好情况===最坏情况  
冒泡排序======O(n*n)=========O(n)======O(n*n)  
简单选择排序===O(n*n)=========O(n*n)====O(n*n)  
直接插入排序===O(n*n)=========O(n)======O(n*n)  
希尔排序======O(nlgn)~O(n*n)==O(n^1.3)==O(n*n)  
堆排序========O(nlgn)========O(nlgn)===O(nlgn)  
归并排序=======O(nlgn)========O(nlgn)===O(nlgn)  
快速排序=======O(nlgn)========O(nlgn)===O(n*n)  

###空间复杂度
归并排序：O(n),快速排序：O(lgn)~O(n)  
其他都是原地排序，只需要O(1).
