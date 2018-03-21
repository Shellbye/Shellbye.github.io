---
author: shellbye
comments: true
date: 2015-03-26 11:27:08+00:00
layout: post
slug: C-struct-padding
title: C struct padding
categories:
- tech_world
tags:
- C
- data structure
---

常有这样的面试题，给出如下struct，并问test占多少字节

{% highlight c %}
struct example0  {  
    char   x1;  
    short  x2;  
    float  x3;  
    char   x4;  
};

struct example0 test = {1,1,1,1};
{% endhighlight %}

这是一种比较常见的面试题，今天看了不少资料，结合前任的分析，做一个小笔记。

首先[这篇文章](http://www.cnblogs.com/motadou/archive/2009/01/17/1558438.html)中的一些概念介绍的比较清楚，
结构体的对齐包含以下两层含义：

1.结构体内各个数据成员内存对齐（data alignment）

2.结构体总长度填充（data structure padding）

数据成员内存对齐指的是在内存中存放数据时，要在以字长的整数倍为起始位置，因为这样有利于CPU高效的读写内存。
现代CPU在读写内存时都是以字为单位进行读写，32为机器上一次读写4个字节，现在假设要读取一个int数据x=1234（4字节），
假设x在内存存储如下：

![align](/assets/align.jpg)

即x是内存对齐的，那么CPU在读它的时候，直接读取一次，只读0--3这四个字节就可以了，但是如果x在内存中这样存储：

![not_align](/assets/not_align.jpg)

那么，同样是读取x，这一次CPU不得不读两次，第一次0--3这个字节，第二次4--7，时间上的开销无形之中多了一倍。

关于结构体总长度填充，[这个](http://stackoverflow.com/questions/11906486/size-of-struct-containing-double-field)问答上说的
可能比较靠谱，即是为了结构数组中的每一个都能在开始的时候内存对齐，否则不论你内部对齐的多么牛逼，万一开始就在一个不对齐的
地方，那么内部的对齐也就没有意义了。我感觉这里之所以不得不在末尾填充，就是因为数组在内存中连续存储的。


介绍完概念，就该介绍方法了。[这篇博客](http://blog.csdn.net/yuucyf/article/details/7872502)在对齐的方法上给了我不少启发，
当然，想要获得结构体的总长度，首先需要知道各个基本类型的长度，下面的程序在我本机（64位windows）上调通并且执行结果在注释中：

{% highlight c %}
#include<stdio.h>
//#pragma  pack(2)
//这里注释掉的这句就是人工干预对齐模式，而不是编译器自动处理

int main()
{
    printf("sizeof(char) = %d\n", sizeof(char));
    printf("sizeof(char*) = %d\n", sizeof(char*));
    printf("sizeof(short) = %d\n", sizeof(short));
    printf("sizeof(int) = %d\n", sizeof(int));
    printf("sizeof(int*) = %d\n", sizeof(int*));
    printf("sizeof(float) = %d\n", sizeof(float));
    printf("sizeof(long) = %d\n", sizeof(long));
    printf("sizeof(unsigned long) = %d\n", sizeof(unsigned long));
    printf("sizeof(long long) = %d\n", sizeof(long long));
    printf("sizeof(double) = %d\n", sizeof(double));
    printf("sizeof(double*) = %d\n", sizeof(double*));
    printf("sizeof(long double) = %d\n", sizeof(long double));
    

    system("pause");
    /* output
    sizeof(char) = 1
    sizeof(char*) = 4
    sizeof(short) = 2
    sizeof(int) = 4
    sizeof(int*) = 4
    sizeof(float) = 4
    sizeof(long) = 4
    sizeof(unsigned long) = 4
    sizeof(long long) = 8
    sizeof(double) = 8
    sizeof(double*) = 4
    sizeof(long double) = 12
    */
}
{% endhighlight %}

接着就是下面的不同的结构体的计算了，在下面的程序中，用-表示用无用字符填充，即占位，\| 则表示是当前类型占用，是实际使用的字节

{% highlight c %}
#include<stdio.h>

struct example0  {  
    char   x1;  
    short  x2;  
    float  x3;  
    char   x4;  
};  

struct example1  
{  
    short a;  
    long b;  
};

struct naturalalign  
{  
    char a;  
    short b;  
    char c;  
}; 

struct naturalalign2
{
    char a;
    int b;
    char c;
};

struct example2{
    char a;   
    int b;
    float c;
    double d;
};

struct example3{
    char a;
    double b;
};

struct example4{
    char a;
    double b;
    int c;
};

int main()
{
    // ref:http://www.cnblogs.com/motadou/archive/2009/01/17/1558438.html
    // ref:http://blog.csdn.net/yuucyf/article/details/7872502
    // - means fulfilled by useless byte
    // | means in use.
    struct example0 e0 = {1,1,1,1};
    /*
    struct example0  {  
        char   x1;  
        short  x2;  
        float  x3;  
        char   x4;  
    };  
    
    0      x1
    1      -
    2      x2
    3      |
    4      x3
    5      |
    6      |
    7      |
    8      x4
    9      -
    10     -
    11     -
    12     
    */
    printf("sizeof(e0)=%d\n", sizeof(e0));
    
    struct example1 e1 = {1,1};
    /*
    struct example1  
    {  
        short a;  
        long b;  
    };
    
    0      a
    1      |
    2      -
    3      -
    4      b
    5      |
    6      |
    7      |
    8      
    9      
    10     
    11     
    12     
    */
    printf("sizeof(e1)=%d\n", sizeof(e1));
    
    struct naturalalign na = {1,1,1};
    /*
    struct naturalalign  
    {  
        char a;  
        short b;  
        char c;  
    }; 
    
    0      a
    1      |
    2      b
    3      |
    4      c
    5      |
    6
    7
    8      
    9      
    10     
    11     
    12     
    */
    printf("sizeof(na)=%d\n", sizeof(na));
    
    struct naturalalign2 na2 = {1,1,1};
    /*
    struct naturalalign2
    {
        char a;
        int b;
        char c;
    };
    
    0      a
    1      |
    2      -
    3      -
    4      b
    5      |
    6      |
    7      |
    8      |
    9      c
    10     -
    11     -
    12     -
    */
    printf("sizeof(na2)=%d\n", sizeof(na2));
    
    struct example2 e2 = {1,1,1,1};
    /*
    struct example2{
        char a;   
        int b;
        float c;
        double d;
    };
    0      a
    1      -
    2      -
    3      -
    4      b
    5      |
    6      |
    7      |
    8      c
    9      |
    10     |
    11     |
    12     -
    13     -
    14     -
    15     -
    16     d
    17     |
    18     |
    19     |
    20     |
    21     |
    22     |
    23     |
    24
    
    */
    printf("sizeof(e2)=%d\n", sizeof(e2));
    
    struct example3 e3 = {1,1};
    /*
    struct example3{
        char a;
        double b;
    };
    0       a   
    1       -
    2       -
    3       -
    4       -
    5       -
    6       -
    7       -
    8       b
    9       |
    10      |
    11      |
    12      |
    13      |
    14      |
    15      |
    16      -
    */
    printf("sizeof(e3)=%d\n", sizeof(e3));
    
    struct example4 e4 = {1,1,1};
    /*
    struct example4{
        char a;
        double b;
        int c;
    }
    0       a   
    1       -
    2       -
    3       -
    4       -
    5       -
    6       -
    7       -
    8       b
    9       |
    10      |
    11      |
    12      |
    13      |
    14      |
    15      |
    16      -
    17      c
    18      |
    19      |
    20      |
    21      -
    22      -
    23      -
    24      
    
    */
    printf("sizeof(e4)=%d\n", sizeof(e4));
    /* output
    sizeof(e0)=12
    sizeof(e1)=8
    sizeof(na)=6
    sizeof(na2)=12
    sizeof(e2)=24
    sizeof(e3)=16
    sizeof(e4)=24
    */
    system("pause");
}
{% endhighlight %}