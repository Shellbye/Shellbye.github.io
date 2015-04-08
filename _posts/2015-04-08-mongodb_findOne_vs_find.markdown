---
author: shellbye
comments: true
date: 2015-01-08 11:27:08+00:00
layout: post
slug: mongodb-findOne-vs-find
title: mongodb findOne vs find
categories:
- tech_world
tags:
- mongodb
- java
---

在使用mongodb时，常遇到这样一种场景，就是你需要查看一个数据是否在数据库中，
那么这个时候你的选择就是find或者findOne了。今天，结合一些实验以及网上查阅的一些资料，
我准备详细的记录下这两个方法的差别和使用场景。

1.```[public DBCursor find( DBObject ref )](http://api.mongodb.org/java/current/com/mongodb/DBCollection.html#find-com.mongodb.DBObject-)```
```find```函数内部实现比较简单，就是新建了一个```DBCursor```对象，源码如下：

{% highlight java %}
    /**
     * Queries for an object in this collection.
     *
     * @param ref A document outlining the search query
     * @return an iterator over the results
     * @mongodb.driver.manual tutorial/query-documents/ Query
     */
    public DBCursor find( DBObject ref ){
        return new DBCursor( this, ref, null, getReadPreference());
    }
{% endhighlight %}

2.```[public DBObject findOne( DBObject o )](http://api.mongodb.org/java/current/com/mongodb/DBCollection.html#findOne-com.mongodb.DBObject-)```
而在findOne内部，函数经历两次跳转后，最终的实现逻辑其实还是在内部调用了```find```函数，
找到所有符合条件的，然后返回第一个，源码如下：

{% highlight java %}
    /**
     * Get a single document from collection.
     *
     * @param o           the selection criteria using query operators.
     * @param fields      specifies which projection MongoDB will return from the documents in the result set.
     * @param orderBy     A document whose fields specify the attributes on which to sort the result set.
     * @param readPref    {@code ReadPreference} to be used for this operation
     * @param maxTime     the maximum time that the server will allow this operation to execute before killing it
     * @param maxTimeUnit the unit that maxTime is specified in
     * @return A document that satisfies the query specified as the argument to this method.
     * @mongodb.driver.manual tutorial/query-documents/ Query
     * @since 2.12.0
     */
    DBObject findOne(DBObject o, DBObject fields, DBObject orderBy, ReadPreference readPref,
                     long maxTime, TimeUnit maxTimeUnit) {

        QueryOpBuilder queryOpBuilder = new QueryOpBuilder().addQuery(o).addOrderBy(orderBy)
                                                            .addMaxTimeMS(MILLISECONDS.convert(maxTime, maxTimeUnit));

        if (getDB().getMongo().isMongosConnection()) {
            queryOpBuilder.addReadPreference(readPref);
        }

        Iterator<DBObject> i = find(queryOpBuilder.get(), fields, 0, -1, 0, getOptions(), readPref, getDecoder());
        
        DBObject obj = (i.hasNext() ? i.next() : null);
        if ( obj != null && ( fields != null && fields.keySet().size() > 0 ) ){
            obj.markAsPartialObject();
        }
        return obj;
    }
{% endhighlight %}

所以从理论的角度看，如果只是检查符合某条件的对象是否存在，还是建议使用```find().limit(1)```. 
那么接下来就从实践的角度来看一下。

下面是我测试写的一段代码，其中的col_name中包含大于300万的记录，

{% highlight java %}

{% endhighlight %}

程序输出如下：

    79
    47
    48
    50

可以看到在没有索引的情况下，```findOne```的性能要好于```find```,如果使用索引，则差别不大了。
事实证明，实践和理论推测还是有一点点距离的。

如果注释(comment)掉那8行包含```if```和```j++```的语句，程序的输出如下：

    2
    85
    0
    58

Refs:

1.[https://blog.serverdensity.com/checking-if-a-document-exists-mongodb-slow-findone-vs-find/](https://blog.serverdensity.com/checking-if-a-document-exists-mongodb-slow-findone-vs-find/)

2.[http://dba.stackexchange.com/questions/7573/difference-between-mongodbs-find-and-findone-calls](http://dba.stackexchange.com/questions/7573/difference-between-mongodbs-find-and-findone-calls)