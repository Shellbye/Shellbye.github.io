---
author: shellbye
comments: true
date: 2016-12-21 00:00:08+00:00
layout: post
slug: mysql_master_slave_replication
title: mysql master slave replication 主从备份
categories:
- tech_world
tags:
- mysql
---

# 场景描述  
在官方的[replication]描述中，replication的优点有如下几个方面：

{% highlight %}
Advantages of replication in MySQL include:

- Scale-out solutions -
spreading the load among multiple slaves to improve performance.
In this environment, all writes and updates must take place on the master server.
Reads, however, may take place on one or more slaves.
This model can improve the performance of writes
(since the master is dedicated to updates),
while dramatically increasing read speed across an increasing number of slaves.

- Data security - because data is replicated to the slave,
and the slave can pause the replication process,
it is possible to run backup services on the slave without corrupting
the corresponding master data.

- Analytics - live data can be created on the master,
while the analysis of the information can take place on
the slave without affecting the performance of the master.

- Long-distance data distribution -
you can use replication to create a local copy of data for a remote site to use,
without permanent access to the master.

{% endhighlight %}

概括起来就是：可扩展性，数据安全，便捷分析，便于分发。

在我们团队目前的场景中，暂时需要的其实只有“便于分发”，
但是其顺带的其他特性也是“the more the better”。

mysql5.7之后，除了传统的给予二进制的日志的replication之外，
还支持基于global transaction identifiers ([GTIDs])的复制。

# 操作步骤
具体操作步骤在文章最后的参考文章有一些，下面我的笔记来自[官方文档]：

1. [Master Config]
第一步是设置主数据库，具体需要配置的信息比较简单，就是在`my.cnf`文件中，
修改以下配置（一般就是打开注释即可），

{% highlight %}

server-id = 1
log_bin = /var/log/mysql/mysql-bin.log

{% endhighlight %}

由于从数据库需要用户名和密码去链接主数据库，而且用于复制的用户名和密码是[明文存储]的，
所以需要在主数据库中新建专用的用户，新建的用户需要`REPLICATION SLAVE`权限:

{% highlight mysql %}

mysql> CREATE USER 'repl'@'%.mydomain.com' IDENTIFIED BY 'slavepass';
mysql> GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%.mydomain.com';

{% endhighlight %}

为了让从数据库知道从哪里开始执行二进制的日志，需要先获取主数据中目前的二进制文件的[位置]，

{% highlight mysql %}

mysql> FLUSH TABLES WITH READ LOCK;

{% endhighlight %}

注意，这里需要保持这个窗口打开（session open），否则`READ LOCK`就会失效，
然后新开一个窗口（也许你需要[tmux]），

{% highlight mysql %}

mysql > SHOW MASTER STATUS;
+------------------+----------+--------------+------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+------------------+----------+--------------+------------------+
| mysql-bin.000003 | 73       | test         | manual,mysql     |
+------------------+----------+--------------+------------------+

{% endhighlight %}

这里需要记录`File`和`Position`，稍后在slave的配置中会用到。

如果你只需要复制某一个或一些database，而不是整个数据库，
或者在复制中需要忽略某一个或一些database，那么在`my.cnf`中有以下配置项：

{% highlight %}

binlog_do_db = include_database_name
binlog_do_db = include_database_name2
binlog_ignore_db = include_database_name
binlog_ignore_db = include_database_name2
 # 通过不断添加即可实现多个database的配置

{% endhighlight %}

如果你的主数据库中没有数据，那么主数据库的配置工作到此为止，
可以进入从数据库的配置工作了。但是如果你的主数据库中此时已经有了大量的数据，
那么接下来就需要为你的数据库进行一次快照了。

mysql的快照一般都是通过`[mysqldump]`来实现的，具体的例子有以下几种：

{% highlight bash %}

shell> mysqldump -uroot -p database_name > db.sql
shell> mysqldump -uroot -p database_name table_name > db_tb.sql

{% endhighlight %}

至此，主数据库的配置就完成了，接下来就是从数据库的配置了。

2. [Slave Config]
首先与主数据库一样的配置，就是需要在`my.conf`中进行如下配置，
其中的`server-id`必须是全局唯一的

{% highlight %}

[mysqld]
server-id=2

{% endhighlight %}

然后在mysql中执行以下sql语句配置主数据库：

{% highlight mysql %}

mysql> CHANGE MASTER TO
    ->     MASTER_HOST='master_host_name',
    ->     MASTER_USER='replication_user_name',
    ->     MASTER_PASSWORD='replication_password',
    ->     MASTER_LOG_FILE='recorded_log_file_name',
    ->     MASTER_LOG_POS=recorded_log_position;

{% endhighlight %}

然后根据是否有已存在的数据来觉得是否需要先导入刚才导出的sql文件，
最后执行

{% highlight mysql %}

mysql> START SLAVE;

{% endhighlight %}

就搞定了。此时可以执行

{% highlight mysql %}

mysql> SHOW SLAVE STATUS\G

{% endhighlight %}

来查看从数据库的状态信息。

3. [Add slave]
通过以上的操作，至此就可以完成主从数据库的从无到有，如果是需要继续添加slave，
则需要继续下去。

官方推荐的做法比较简单，就是选中一个已经存在的slave，关闭掉其服务，

{% highlight mysql %}

shell> mysqladmin shutdown

{% endhighlight %}

然后拷贝整个slave的数据文件夹，然后修改新的slave的`my.cnf`

{% highlight %}

[mysqld]
server-id=3

{% endhighlight %}

至此，全部的相关配置工作就全部结束了。但是，因为我们的从数据库一般都是只读的，
所以如果你愿意的话，还有最后一步：

# Read Only

mysql支持[read_only]，在`my.cnf`中设置
{% highlight %}

read_only=1

{% endhighlight %}

就可以防止普通用户的写操作，但是这样并不能防止具有`SUPER privilege`的用户的写操作，
MySQL5.7添加了一个新的配置项[super_read_only]，顾名思义，
就是这样设置了之后，连具有`SUPER privilege`的用户也只能read了！


[replication]:http://dev.mysql.com/doc/refman/5.7/en/replication.html
[GTIDs]:http://dev.mysql.com/doc/refman/5.7/en/replication-gtids.html
[官方文档]:http://dev.mysql.com/doc/refman/5.7/en/replication-howto.html
[Master Config]:http://dev.mysql.com/doc/refman/5.7/en/replication-howto-masterbaseconfig.html
[明文存储]:http://dev.mysql.com/doc/refman/5.7/en/slave-logs-status.html
[位置]:http://dev.mysql.com/doc/refman/5.7/en/replication-howto-masterstatus.html
[tmux]:https://tmux.github.io/
[mysqldump]:http://dev.mysql.com/doc/refman/5.7/en/mysqldump.html
[Slave Config]:http://dev.mysql.com/doc/refman/5.7/en/replication-setup-slaves.html
[Add slave]:http://dev.mysql.com/doc/refman/5.7/en/replication-howto-additionalslaves.html
[read_only]:https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_read_only
[super_read_only]:https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_super_read_only
# 参考文章  
[https://www.digitalocean.com/community/tutorials/how-to-set-up-master-slave-replication-in-mysql](https://www.digitalocean.com/community/tutorials/how-to-set-up-master-slave-replication-in-mysql)
[https://www.toptal.com/mysql/mysql-master-slave-replication-tutorial](https://www.toptal.com/mysql/mysql-master-slave-replication-tutorial)
