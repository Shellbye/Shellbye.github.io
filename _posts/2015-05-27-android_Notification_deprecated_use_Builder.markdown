---
author: shellbye
comments: true
date: 2015-05-27 11:27:08+00:00
layout: post
slug: android-notification-deprecated-use-builder
title: Android Notification deprecated use Builder
categories:
- tech_world
tags:
- android
---

最近看了点安卓的入门级读物，[第一行代码](http://book.douban.com/subject/25942191/){:target="_blank"} ，
感觉这本书不错，是2014年7月出的，我最近读已经发现不少deprecated的方法了，技术进步真是快啊。

我是在看到第八章的8.1.1节中，遇到下面的代码时

{% highlight java linenos %}
NotificationManager manager = (NotificationManager)
        getSystemService(NOTIFICATION_SERVICE);
Notification notification = new Notification(R.drawable.
        ic_launcher, "This is ticker text", System.currentTimeMillis());
notification.setLatestEventInfo(this, "This is content title",
        "This is content text", null);
manager.notify(1, notification);
{% endhighlight %}

其中第三行的```new Notification```和第五行的```notification.setLatestEventInfo```都是deprecated的，
看了下源码的提示，说是让用```Builder```,(```Use {@link Builder} instead.```)

可是要怎么用却没有说，于是Google了一下之后还是来到了[stackoverflow](http://stackoverflow.com/questions/6391870/how-exactly-to-use-notification-builder#answer-8869647){:target="_blank"},

结合书中的例子和这个回答，解决方案如下：

{% highlight java linenos %}
Notification.Builder builder = new Notification.Builder(ThirdActivity.this);
builder.setSmallIcon(R.drawable.ic_launcher)
        .setTicker("This is ticker text2")
        .setWhen(System.currentTimeMillis())
        .setAutoCancel(true)
        .setContentTitle("This is content title2")
        .setContentText("This is content text2");
Notification n = builder.build();

manager.notify(1, n);
{% endhighlight %}

需要注意的是上面的最后一行，在Android Studio中有个提示，就是它支持的最低API版本是16，
所以可能需要修改app目录下的build.gradle，将其中的minSdkVersion改为16.