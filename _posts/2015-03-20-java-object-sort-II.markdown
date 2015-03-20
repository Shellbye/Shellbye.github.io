---
author: shellbye
comments: true
date: 2015-03-20 03:32:19+00:00
layout: post
slug: java-object-sort
title: JAVA Object Sort
categories:
- tech_world
tags:
- java
---

关于JAVA 对象比较，[之前](/blog/tech_world/java-object-sort/)写过一个通过实现Comparable接口的方式，今天记录一下通过实现接口Comparator的方法。

实现了Comparable和实现了Comparator的对象在排序时稍有不同。

    1.实现了Comparable的对象是自身从此就有了排序的功能，比如int这个类型，本身就有顺序，是可以排序的
    2.实现了Comparator的对象本身还是不能排序的，它只是变成了一个“裁判”，可以比较其他对象的顺序

这两种不同可以用如下的两行代码进行简要的表示


{% hightlight java %}
    Collections.sort(objectNeedToSort);
    Collections.sort(objectNeedToSort, new objectCanCompareObjectNeedToSort());
{% endhighlight %}


下面是实现了Comparator的排序方法：


{% hightlight java %}
package interview;

import java.util.*;

/**
 * User: shellbye.com@gmail.com
 * Date: 2015/3/20
 */
public class ComparatorPersonById implements Comparator {
    public int compare(Object o1, Object o2) {
        Person p1 = (Person) o1;
        Person p2 = (Person) o2;
        return p1.getPersonId() - p2.getPersonId();
    }

    public static void main(String[] args) {
        List<Person> p = new ArrayList<>();
        p.add(new Person(0));
        p.add(new Person(40));
        p.add(new Person(10));
        for (Person a : p) {
            System.out.println(a.getPersonId());
        }
        Collections.sort(p, new ComparatorPersonById());
        for (Person a : p) {
            System.out.println(a.getPersonId());
        }
    }
}

class Person {
    private int personId;

    Person(int id) {
        this.personId = id;
    }

    public int getPersonId() {
        return personId;
    }

    public void setPersonId(int personId) {
        this.personId = personId;
    }
}
{% endhighlight %}



输出如下：

    
    0
    40
    10
    0
    10
    40



参考资料：
0.[http://javarevisited.blogspot.sg/2011/06/comparator-and-comparable-in-java.html](http://javarevisited.blogspot.sg/2011/06/comparator-and-comparable-in-java.html)
