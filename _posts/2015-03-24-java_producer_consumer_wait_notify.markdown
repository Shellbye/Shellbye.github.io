---
author: shellbye
comments: true
date: 2015-03-24 11:27:08+00:00
layout: post
slug: java-producer-consumer-wait-notify
title: java 生产者 消费者 以及wait()和notify()分析
categories:
- 技术世界
tags:
- java
---

[生产者消费者](http://en.wikipedia.org/wiki/Producer%E2%80%93consumer_problem)
模型几乎是所有操作系统课程上都会提到的一个模型，它不光可以说明操作系统的信号量机制，
可以拿来说明多线程的一些工作机制。

下面的代码是我在看[这个](http://javarevisited.blogspot.sg/2012/02/why-wait-notify-and-notifyall-is.html)
Java面试题目时自己搜了搜，然后结合代码中一些[参考](http://www.tutorialspoint.com/javaexamples/thread_procon.htm)
写的一个生产者消费者的模型。

关于下面的代码，有以下几点得提前强调下：

[wait()](http://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#wait())方法在官网有如下说明

    Causes the current thread to wait until another thread invokes the notify() method or the notifyAll() method for this object
    
由代码的中stackoverflow上的这个[问题](http://stackoverflow.com/questions/2536692/a-simple-scenario-using-wait-and-notify-in-java)
下面的回答，已经一些相应的连接可以知道，wait()会在没有任何notify的情况下自己awake，所以在检查状态时，一定要用while
而不是if，否则会错误的进入，然后导致出错。还有一种情况就是可能有较多的thread在wait，那么当有notifyAll()调用时，
所有的wait都会awake,这个时候如果大家都真的awake，势必会引发错误，所以正确的做法还是要醒来后继续检查条件。

[notifyAll()](http://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#notifyAll())在官网的简介如下

    Wakes up all threads that are waiting on this object's monitor.
    
这里的```this object's```指的当前调用notifyAll()函数的对象，即本例中的Msg的实例。notifyAll()会给所有Msg的正在wait的实例
发送awake信号，这里就更需要上面的while（而不是if）了，否则就会出错。比如有三个消费者在wait，但是目前只有一个生产者生产了
一个消息，而它就去notifyAll，那肯定就会出错。

为了更好的表示每个线程的wait和awake，我在每一个wait()后面添加了线程被唤醒时的打印，下面是某次执行的输出：

    get wait
    get wait
    +++++++[p]++++++++
    get awake
    -------[]-------
    get awake
    get wait
    +++++++[p]++++++++
    get awake
    -------[]-------
    get wait
    +++++++[p]++++++++
    get awake
    -------[]-------
    get wait
    +++++++[p]++++++++
    get awake
    -------[]-------
    +++++++[p]++++++++
    -------[]-------
    +++++++[p]++++++++
    -------[]-------
    +++++++[p]++++++++
    +++++++[p, p]++++++++
    -------[p]-------

因为故意在main函数中先启动了两个消费者Consumer，所以最初可以看到两个get wait（前两行），
紧接着生产者生产了一条数据（第三行），并发送了notifyAll消息，于是两个消费者都醒了（第4、6行），
但是只有一个消费者成功的获取了消息（打印了第五行），另一个消费者醒了发现没东西，遍继续睡觉了（第七行）。
后面以此类推。

好了，Talk is cheap, 看代码吧。


{% highlight java %}
package interview;

import java.lang.*;
import java.lang.Override;
import java.util.ArrayList;

/**
 * User: shellbye.com@gmail.com
 * Date: 2015/3/23
 * ref:http://www.programcreek.com/2009/02/notify-and-wait-example/
 */
public class WaitAndNotify0 {

    public static void main(String[] args) throws Exception {
        Msg msg = new Msg();

        Producer0 p = new Producer0(msg);
        Consumer0 c = new Consumer0(msg);
        p.start();
        c.start();
    }
}

class Msg {
    ArrayList<String> m = new ArrayList<>();

    public synchronized void putMsg(String msg) {
        try {
            // changed from if to while by this 
            // ref:http://stackoverflow.com/questions/2536692/a-simple-scenario-using-wait-and-notify-in-java
            while (m.size() > 10) {
                System.out.println("put wait");
                wait();
                System.out.println("put awake");
            }
            m.add(msg);
            // when delete oen of the notifyAll() or delete both of them,,
            // the program dead with 'put wait' and 'get wait'
            notifyAll();
            System.out.println("+++++++" + m + "++++++++");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public synchronized void getMsg() {
        try {
            while (m.size() < 1) {
                System.out.println("get wait");
                wait();
                System.out.println("get awake");
            }
            m.remove(0);
            notifyAll();
            System.out.println("-------" + m + "-------");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

class Producer0 extends Thread {

    Msg m;

    Producer0(Msg m) {
        this.m = m;
    }


    @Override
    public void run() {
        try {
            while (true) {
                sleep((int) (Math.random() * 1000));
                m.putMsg("p");
            }
        } catch (Exception e) {
            e.printStackTrace();
        }

    }
}


class Consumer0 extends Thread {

    Msg m;

    Consumer0(Msg m) {
        this.m = m;
    }

    @Override
    public void run() {
        try {
            while (true) {
                sleep((int) (Math.random() * 1000));
                m.getMsg();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }


    }
}
{% endhighlight %}

一些参考：
[0].[http://javarevisited.blogspot.sg/2012/02/why-wait-notify-and-notifyall-is.html](http://javarevisited.blogspot.sg/2012/02/why-wait-notify-and-notifyall-is.html)
[1].[http://javarevisited.blogspot.sg/2011/05/wait-notify-and-notifyall-in-java.html](http://javarevisited.blogspot.sg/2011/05/wait-notify-and-notifyall-in-java.html)
[2].[http://stackoverflow.com/questions/2536692/a-simple-scenario-using-wait-and-notify-in-java](http://stackoverflow.com/questions/2536692/a-simple-scenario-using-wait-and-notify-in-java)
[3].[http://stackoverflow.com/questions/3362303/whats-a-monitor](http://stackoverflow.com/questions/3362303/whats-a-monitor)