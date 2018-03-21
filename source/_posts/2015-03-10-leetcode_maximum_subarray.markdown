---
author: shellbye
comments: true
date: 2015-03-10 11:27:08+00:00
layout: post
slug: leetcode-maximum-subarray
title: LeetCode Maximum Subarray 
categories:
- tech_world
tags:
- java
- algorithms
---

Recently I start to do some algorithm practice and I chose the well-known 
[leetcode](https://leetcode.com/problemset/algorithms/) to start.
All my practice code are available at my [github]().

[Maximum Subarray](https://leetcode.com/problems/maximum-subarray/) is the 
first one that I think need me to do some note for I really have a bad memory.

>- Find the contiguous subarray within an array (containing at least one number) 
which has the largest sum.
 
>- For example, given the array [−2,1,−3,4,−1,2,1,−5,4],
the contiguous subarray [4,−1,2,1] has the largest sum = 6.

I find this [article] really helpful and here is some understanding of mine.

There are two conclusions,

1. For array[1...n], if array[i...j] is the maximum subarray, then for any 
k(i<=k<=j), the sum of array[i...k] is positive, or array[k+1...j] will 
be the maximum subarray.

2. If we divide array[1...n] from left to right into several subarray,
and all the sum of the subarray is negative except the last one. And for
any subarray array[i...j] and any k(i<=k<=j), the sum of array[i...k] is positive.

What I want to noted is the Conclusion 2. We have such a hypothesis that the 
maximum subarray is the prefix array of one of the several subarray in Conclusion 2.
And it isn't consist of more than one subarray.

Let's assume the maximum subarray array[p...q] is consist of subarray array[i...j] and 
array[j+1...k]. According to Conclusion 2, there is an m(i<=m<j) that makes the sum of 
array[i...m] is the biggest sum of array[i...j]; there is also an n(j+1<=n<k) that 
makes the sum of array[j+1...n] is the biggest sum of array[j+1...k]. Now we can compare
like this:

if sum(array[i...m]) > sum(array[j+1...n]) > sum(array[j+1...q]), 
since sum(array[p...j]) < 0, 
so sum(array[i...m]) > sum(array[p...j]) + sum(array[j+1...q]) = sum(array[p...q])

else if sum(array[j+1...n]) > sum(array[i...m]), we can conclude to
sum(array[j+1...n]) > sum(array[p...q]) the same way.
   
So, the maximum subarray isn't consist of subarray.