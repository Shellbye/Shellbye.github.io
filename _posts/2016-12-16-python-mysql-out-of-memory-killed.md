---
author: shellbye
comments: true
date: 2016-12-16 00:00:08+00:00
layout: post
slug: python_mysql_out_f_memory_killed
title: Python been kill for out of memory when using mysql
categories:
- tech_world
tags:
- python
- mysql
---

# 场景描述  
在一个很大的mysql数据表中，将所有的数据全部读出来，
然后一条一条处理，将处理后的数据再一条一条的写入另一个表中，
大概的代码逻辑如下所示：

{% highlight python %}
import MySQLdb


conn = MySQLdb.connect(host=DB_HOST, user=DB_USER, db=DB_NAME,
                       passwd=DB_PASSWORD, charset="utf8")
cursor = conn.cursor()
cursor.execute("select * from very_big_table;")
rows = cursor.fetchall()
for row in rows:
    # do something about the row
    cursor.execute("insert into another_table");
    conn.commit()
{% endhighlight %}

# 问题出现  
整体的逻辑是比较简单的，mysql中的数据量也不是很大，不到百万而已，
但是对每一条数据做处理时，因为要进行一些图像操作，所以可以内存消耗比较大吧，
总之计划是四五天执行完毕的，结果第三天就被操作系统`Killed`了，
在查看系统日志（`/var/log/syslog`）时，发现以下段落，看样子是内存耗尽了：

{% highlight bash %}
ubuntu kernel: [12600693.883297] mysqld invoked oom-killer: gfp_mask=0x24201ca, order=0, oom_score_adj=0
...
...
ubuntu kernel: [12600693.883935] Out of memory: Kill process 89623 (python) score 930 or sacrifice child
{% endhighlight %}

重启了一下程序，发现起初内存占用并不多，但是会随着时间的推移慢慢增长，
看样子，是有内存使用不当的问题了。

# 解决过程  
经过一阵Google，发现了这个[SO]和这个[SO1],知道了原来还有`SSCursor`这种神器，
于是火速将代码重构成了这样：

{% highlight python %}
import MySQLdb
import MySQLdb.cursors as cursors


conn = MySQLdb.connect(host=DB_HOST, user=DB_USER, db=DB_NAME,
                       passwd=DB_PASSWORD, charset="utf8"，
                       cursorclass=cursors.SSCursor)
cursor = conn.cursor()
cursor.execute("select * from very_big_table;")
for row in cursor:
    # do something about the row
    cursor.execute("insert into another_table");
    conn.commit()
{% endhighlight %}

然后执行，Bang!!!，出错了，

{% highlight bash %}
`_mysql_exceptions.ProgrammingError: (2014, "Commands out of sync; you can't run this command now")`
{% endhighlight %}

读文档光是仔细是不行了，要用心，[官方文档]明确的写了，

{% highlight bash %}
You MUST retrieve the entire result set and close() the cursor before additional queries can be peformed on the connection.
{% endhighlight %}

原来是这个connection被神器占用了，那么好，单独再开一个connection，
于是代码变成了最终版如下：

{% highlight python %}
import MySQLdb
import MySQLdb.cursors as cursors


conn = MySQLdb.connect(host=DB_HOST, user=DB_USER, db=DB_NAME,
                       passwd=DB_PASSWORD, charset="utf8"，
                       cursorclass=cursors.SSCursor)
write_conn = MySQLdb.connect(host=DB_HOST, user=DB_USER, db=DB_NAME,
                             passwd=DB_PASSWORD, charset="utf8")
cursor = conn.cursor()
write_cursor = write_conn.cursor()
cursor.execute("select * from very_big_table;")
for row in cursor:
    # do something about the row
    write_cursor.execute("insert into another_table");
    write_conn.commit()
{% endhighlight %}

世界从此不会内存溢出，赞！

这里有我在我最爱的[stackoverflow.com]上的回答，
可以点击查看哦^_^._


[SO]:http://stackoverflow.com/questions/18193825/python-how-to-use-a-generator-to-avoid-sql-memory-issue
[SO1]:http://stackoverflow.com/questions/1808150/how-to-efficiently-use-mysqldb-sscursor
[官方文档]:http://mysql-python.sourceforge.net/MySQLdb-1.2.2/public/MySQLdb.cursors.CursorUseResultMixIn-class.html
[stackoverflow.com]:http://stackoverflow.com/questions/1811173/why-does-my-python-script-randomly-get-killed/#answer-41176365
