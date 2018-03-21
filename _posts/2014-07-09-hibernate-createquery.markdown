---
author: shellbye
comments: true
date: 2014-07-09 09:26:53+00:00
layout: post
slug: hibernate-createquery
title: Hibernate createQuery()
wordpress_id: 965
categories:
- tech_world
---

种种迹象表明，Hibernate很牛逼，但是目前我不知道它又多牛逼，也不知道它为什么牛逼，只是最近在写的过程中遇到了一些问题，下面是一些笔记：

1.参数

我是在试错了很多次之后才知道原来session.createQuery()里面的HQL语句里不是使用表名，而是使用类名的。比如你的MySQL数据里有表叫social_friend，它对应的类是SocialFriend，那么，你得这样用：

    
    session.createQuery("from SocialAccount where userId= :id ").setParameter("id", id);


不得不说这个东西还是折腾了我很久了，因为我一直以为是其他地方的错（事实证明其他地方也也确实有错，详见后文）。

2.依赖的类构造方法

我还没有找到具体的证据，不过下面的错误信息多少已经说明了问题：

    
    org.hibernate.InstantiationException: No default constructor for entity: com.shellbye.XXXX.SocialAccount


是的，createQuery()看起来是需要一个默认的空构造方法的，于是在SocialAccount.java里面添加如下构造方法：

    
    	
    public SocialAccount() {}


于是问题就这样解决了。

注：session.createSQLQuery()是不需要默认的构造方法也可以执行的，但是它取出来的数据是Object数组，没有美妙的键值对，会很不方便。

更新：session.createSQLQuery()也可以取出model，只是需要加一个参数如下：

    
    session.createSQLQuery(
    "select * from social_account").addEntity(SocialAccount.class);


这样就相当于告诉了Hibernate把取回来的东西封装一下。
