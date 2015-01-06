---
author: shellbye
comments: true
date: 2014-10-30 08:55:26+00:00
layout: post
slug: mongodb-document-field-names-cant
title: MongoDB Document field names can't have a . in them
wordpress_id: 1096
categories:
- 技术世界
tags:
- java
- MongoDB
---

在用JAVA往MongoDB里面插入数据时，当以域名作为field的时，报如下错误：


    
    java.lang.IllegalArgumentException: Document field names can't have a . in them



查找了一些资料（见文末）之后，发现解决办法如下：


    
    if (tag.contains(".")) {
        tagsInfo.put(tag.replace(".", "\\u002e"), "1");
    } else {
        tagsInfo.put(tag, "1");
    }



参考资料：

0.[http://stackoverflow.com/questions/8429318/how-to-use-dot-in-field-name](http://stackoverflow.com/questions/8429318/how-to-use-dot-in-field-name)
1.[http://docs.mongodb.org/manual/core/document/#dot-notation](http://docs.mongodb.org/manual/core/document/#dot-notation)
2.[http://docs.mongodb.org/manual/faq/developers/#faq-dollar-sign-escaping](http://docs.mongodb.org/manual/faq/developers/#faq-dollar-sign-escaping)
3.[http://www.fileformat.info/info/unicode/char/2e/index.htm](http://www.fileformat.info/info/unicode/char/2e/index.htm)
