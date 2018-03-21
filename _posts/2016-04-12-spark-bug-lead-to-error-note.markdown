---
author: shellbye
comments: true
date: 2016-04-12 10:27:08+00:00
layout: post
slug: spark-bug-lead-to-error-note
title: Spark bug导致的错误信息记录
categories:
- tech_world
tags:
- spark
---

这次不能怪[官方文档]{:target="_blank"}了，因为这次的问题也怪我，
在按照上面的例子运行Python版的Text Search时，一直有问题，
在经过排除各种不可能之后，最后锁定在了下面的这个报错信息：

{% highlight bash %}
  Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/Users/jzhang/github/spark/python/pyspark/sql/context.py", line 430, in createDataFrame
    jdf = self._ssql_ctx.applySchemaToPythonRDD(jrdd.rdd(), schema.json())
  File "/Users/jzhang/github/spark/python/pyspark/sql/context.py", line 691, in _ssql_ctx
    "build/sbt assembly", e)
Exception: ("You must build Spark with Hive. Export 'SPARK_HIVE=true' and run build/sbt assembly", Py4JJavaError(u'An error occurred while calling None.org.apache.spark.sql.hive.HiveContext.\n', JavaObject id=o34))
{% endhighlight %}

虽然是一个很明确的错误，但是在网上找了好久都没解决，最后居然发现是spark1.6.0的一个[bug]{:target="_blank"}，
更新到1.6.1就没这个问题了，我也是醉了，唉，还是要记得多多保持软件的更新啊！！！




[官方文档]:http://spark.apache.org/examples.html
[bug]:https://issues.apache.org/jira/browse/SPARK-12120
