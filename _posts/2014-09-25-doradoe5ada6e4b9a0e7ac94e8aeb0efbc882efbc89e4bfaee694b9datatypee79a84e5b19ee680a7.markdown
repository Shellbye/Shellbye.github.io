---
author: shellbye
comments: true
date: 2014-09-25 08:43:19+00:00
layout: post
slug: dorado%e5%ad%a6%e4%b9%a0%e7%ac%94%e8%ae%b0%ef%bc%882%ef%bc%89%e4%bf%ae%e6%94%b9datatype%e7%9a%84%e5%b1%9e%e6%80%a7
title: Dorado学习笔记（2）修改DataType的属性
wordpress_id: 1051
categories:
- tech_world
tags:
- Dorado
---

在Dorado官网的教程[Dorado7实战-企业通讯录（初级）](http://www.bsdn.org/projects/dorado7/study)中，在[4.2.8 准备Model ](http://wiki.bsdn.org/pages/viewpage.action?pageId=43909469)中“新建DataType”之后“修改DataType的属性”的属性时，“matchType”是不能直接通过复制字符来完成的，如果是复制字符的话，在eclipse中通过查看source会发现代码如下：

    
    
    <model>
      <datatype name="SlCompany">
        <property name="matchType">com.bstek.dorado.sample.standardlesso</property>
      </datatype>
    </model>


会发现第四行有明显的错误，正确的做法是点击matchType的值填写框中的选择按钮，然后通过弹出窗口定位到对应的类，此时生成的代码如下：

    
    
    <model>
      <datatype name="SlCompany">
        <property name="matchType">com.bstek.dorado.sample.standardlesson.entity.SlCompany</property>
      </datatype>
    </model>


上面的错误的做法会导致后面“生成PropertyDefs”时不起作用，因为它没有正确定位到类。
