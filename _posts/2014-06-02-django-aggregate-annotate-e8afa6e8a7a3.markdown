---
author: shellbye
comments: true
date: 2014-06-02 03:58:26+00:00
layout: post
slug: django-aggregate-annotate-%e8%af%a6%e8%a7%a3
title: Django aggregate annotate 详解
wordpress_id: 904
categories:
- tech_world
tags:
- Django
---

Django的[数据库API](https://docs.djangoproject.com/en/dev/ref/models/querysets/)可谓是相当的详细，几乎可以涵盖任意的数据库查询需求，可惜在很长一段时间内，我只会一些非常简单的诸如get,filter,exclude等比较简单也比较直接的查询，再加上自己数据库底子不是很好，遇到复杂的查询操作时常常不得不使用for，很多for......后来通过看别人的代码，接触到了[aggregate](https://docs.djangoproject.com/en/dev/topics/db/aggregation/)和annotate这两个函数，发现是在Django中比较常用的而且也貌似比较好用的，可惜一直搞得不是很多，今天打算好好研究下，做个笔记。

1.aggregate（英文原意：a sum total of many heterogenous things taken together，中文释义：合计；集合体；总计）

假设我现在有如下的model：

    
    class ProjectDetail(models.Model):
        financing_amount = models.IntegerField(default=0)


现在，我要从数据库里取出所有的ProjectDetail，然后计算他们的financing_amount的总和，该怎么算呢？如果是原来，我需要用如下方式：

    
        projects = ProjectDetail.objects.all()
        total = 0
        for p in projects:
            total += p.financing_amount


这个是肯定可以实现要求的功能的，但是且不说代码冗长，性能上也没有直接在数据库里操作来的快，如果使用aggregate函数，代码则变成了这样：

    
        total = ProjectDetail.objects.aggregate(Sum('financing_amount'))


通过在[上一篇文章](http://www.shellbye.com/blog/%E6%8A%80%E6%9C%AF%E4%B8%96%E7%95%8C/django-f-expression/)中介绍的方法，可以查看到sql语句如下：

    
    SELECT SUM("project_projectdetail"."financing_amount") AS "financing_amount__sum" FROM "project_projectdetail"


于是乎，不仅仅代码更加简洁了，性能也提升了，哇，生活真美好啊！

这里只是简单的使用了一下Sum这个aggregate函数，还有很多函数参见[这里](https://docs.djangoproject.com/en/dev/ref/models/querysets/#id5)。



2.annotate（英文原意：add explanatory notes to or supply with critical comments，中文释义：注释；给…作注释或评注）

假设有如下model：

    
    
    class ProjectDetail(models.Model):
        financing_amount = models.IntegerField(default=0)
        project_man = models.ManyToManyField('Man')
    class Man(models.Model):
        name = models.CharField(max_length=200)
    


现在，我要计算系统里所有的ProjectDetail中，他们各自有多少个project_man，该怎么算呢？如果按照最简单原始除暴的写法，大概是这样的：

    
        
        projects = ProjectDetail.objects.all()
        p_man_count = []
        for p in projects:
            man_count = p.project_man.count()
            item = {
                'project': p,
                'man_count': man_count
            }
            p_man_count.append(item)
    


之所以需要p_man_count这个东西，是因为你算出来的值需要个地方存储，可以看出来这个功能虽然很简单，但是代码却还是写了不少，同样，我们来看看用annotate会怎么样：

    
    man_count = ProjectDetail.objects.annotate(Sum('project_spokesman'))


通过在[上一篇文章](http://www.shellbye.com/blog/%E6%8A%80%E6%9C%AF%E4%B8%96%E7%95%8C/django-f-expression/)中介绍的方法，可以查看到sql语句如下：

    
    
    SELECT "project_projectdetail"."financing_amount", COUNT("project_projectdetail_project_man"."man_id") AS "project_man__sum" FROM "project_projectdetail" LEFT OUTER JOIN "project_projectdetail_project_man" ON ("project_projectdetail"."id" = "project_projectdetail_project_man"."projectdetail_id") GROUP BY "project_projectdetail"."financing_amount"
    



3.总结：
概括起来将，aggregate是只选择你感兴趣的，返回是一个字典，annotate是在返回原理的queryset的基础之上，在加上你额外需要的字段。
