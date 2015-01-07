---
author: shellbye
comments: true
date: 2014-10-31 03:32:19+00:00
layout: post
slug: java-object-sort
title: JAVA Object Sort
wordpress_id: 1099
categories:
- 技术世界
tags:
- java
---

JAVA 对象比较其实就是自己写一个实现了Comparable的类就可以了，下面是要比较的对象Tag，即实现了Comparable类的对象

{% highlight java %}
package sort;

/**
 * Created by shellbye on 10/31/14.
 */
public class Tag implements Comparable {
    private String key;
    private int count;

    public Tag(int count, String key) {
        super();
        this.count = count;
        this.key = key;
    }

    public int compareTo(Tag compareTag) {
        // DES
        return compareTag.getCount() - this.getCount();

        // ASC
//        return this.getCount() - compareTag.getCount();
    }

    public int getCount() {
        return count;
    }

    public void setCount(int count) {
        this.count = count;
    }

    public String getKey() {
        return key;
    }

    public void setKey(String key) {
        this.key = key;
    }
}

{% endhighlight %}


下面的测试的函数：

{% highlight java %}
package sort;

import java.util.Arrays;

/**
 * Created by shellbye on 10/31/14.
 */
public class SortObject {
    public static void main(String[] arg){
        // 1.创建比较对象
        Tag[] tags = new Tag[3];
        tags[0] = new Tag(0, "a");
        tags[1] = new Tag(1, "a");
        tags[2] = new Tag(2, "a");

        // 2.对象排序
        Arrays.sort(tags);

        // 3.存储排序结果
        int j = 0;
        for (Tag t : tags){
            System.out.println(j + ": " + " Count " + t.getCount() + " Key " + t.getKey());
            j++;
        }
    }
}

{% endhighlight %}


输出如下：

{% highlight YAML %}
0:  Count 2 Key a
1:  Count 1 Key a
2:  Count 0 Key a
{% endhighlight %}


参考资料：
0.[http://www.mkyong.com/java/java-object-sorting-example-comparable-and-comparator/](http://www.mkyong.com/java/java-object-sorting-example-comparable-and-comparator/)
