---
author: shellbye
comments: true
date: 2014-10-29 07:02:06+00:00
layout: post
slug: mongodb-save-objectid
title: MongoDB save() ObjectId
wordpress_id: 1090
categories:
- tech_world
tags:
- java
- MongoDB
---

还是那句尽信书不如无书，在[http://www.tutorialspoint.com/](http://www.tutorialspoint.com/)上学MongoDB的时候，这个[save()方法](http://www.tutorialspoint.com/mongodb/mongodb_update_document.htm)运行这句时，

    
    db.user.save({"_id":ObjectId(545064725814bf818b423de4), "name":"cc"})


总是报错如下,

    
    SyntaxError: Unexpected token ILLEGAL


我就纳闷了，完全按照教程写的啊，格式一模一样，怎么还是错。结果最后在看了后面的几个教程之后发现可能是id需要加引号，加上引号一试，果然成功了。

正确的写法应该是给那串常常的id也加上引号，也有道理的嘛，毕竟是字符串。

    
    db.user.save({"_id":ObjectId('545064725814bf818b423de4'), "name":"cc"})
