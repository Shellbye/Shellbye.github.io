---
author: shellbye
comments: true
date: 2015-09-06 11:57:08+00:00
layout: post
slug: mysql_note
title: MySQL note
categories:
- tech_world
tags:
- mysql
- job
---

### MySQL

1. [MySQL中的NULL](https://github.com/cncounter/translation/blob/master/tiemao_2015/10_Understanding_SQL_Null/10_Understanding_SQL_Null.md){:target="_blank"}

1. 乐观锁与悲观锁  
悲观锁：假定会发生并发冲突，屏蔽一切可能违反数据完整性的操作。悲观锁假定其他用户企图访问或者改变你正在访问、更改的对象的概率是很高的，
因此在悲观锁的环境中，在你开始改变此对象之前就将该对象锁住，并且直到你提交了所作的更改之后才释放锁。悲观的缺陷是不论是页锁还是行锁，
加锁的时间可能会很长，这样可能会长时间的限制其他用户的访问，也就是说悲观锁的并发访问性不好。  
乐观锁：假设不会发生并发冲突，只在提交操作时检查是否违反数据完整性。乐观锁不能解决脏读的问题。    
乐观锁则认为其他用户企图改变你正在更改的对象的概率是很小的，因此乐观锁直到你准备提交所作的更改时才将对象锁住，
当你读取以及改变该对象时并不加锁。可见乐观锁加锁的时间要比悲观锁短，乐观锁可以用较大的锁粒度获得较好的并发访问性能。
但是如果第二个用户恰好在第一个用户提交更改之前读取了该对象，那么当他完成了自己的更改进行提交时，
数据库就会发现该对象已经变化了，这样，第二个用户不得不重新读取该对象并作出更改。这说明在乐观锁环境中，
会增加并发用户读取对象的次数。  
参考：[http://www.cnblogs.com/Bob-FD/p/3352216.html](http://www.cnblogs.com/Bob-FD/p/3352216.html){:target="_blank"}  

1. 存储引擎的选择  
1）Myisam：数据库并发不大，读多写少,而且都能很好的用到索引，sql语句比较简单的应用，TB数据仓库，
不支持事务，也不支持外键，特点是访问速度快，对事务完整性没有要求或者以SELECT、INSERT为主的应用基本都可以使用这个引擎来创建表。  
2）Innodb：并发访问大，写操作比较多，有外键、事务等需求的应用，系统内存较大。
MySQL支持外键的存储引擎只有InnoDB，在创建外键的时候，父表必须有对应的索引，子表在创建外键的时候也会自动创建对应的索引。  
MySQL5.5以后默认使用InnoDB存储引擎，其中InnoDB和BDB提供事务安全表，其它存储引擎都是非事务安全表。

2. 只分配满足需求的最小字符数，不要太慷慨。  
原因：更小的字段类型更小的字符数占用更少的内存，占用更少的磁盘空间，占用更少的磁盘IO，以及占用更少的带宽。  

3. 对于varchar和char的选择要根据引擎和具体情况的不同来选择，主要依据如下原则:  
1)如果列数据项的大小一致或者相差不大，则使用char。  
2)如果列数据项的大小差异相当大，则使用varchar。  
3)对于MyISAM表，尽量使用Char，对于那些经常需要修改而容易形成碎片的myisam和isam数据表就更是如此，
它的缺点就是占用磁盘空间。  
4)对于InnoDB表，因为它的数据行内部存储格式对固定长度的数据行和可变长度的数据行不加区分（所有数据行共用一个表头部分，
这个标头部分存放着指向各有关数据列的指针），所以使用char类型不见得会比使用varchar类型好。
事实上，因为char类型通常要比varchar类型占用更多的空间，所以从减少空间占用量和减少磁盘i/o的角度，
使用varchar类型反而更有利。  
5)表中只要存在一个varchar类型的字段，那么所有的char字段都会自动变成varchar类型，因此建议定长和变长的数据分开。  

4. 主键选择原则（注：这里说的主键设计主要是针对INNODB引擎）  
1）能唯一的表示行。  
2）显式的定义一个数值类型自增字段的主键，这个字段可以仅用于做主键，不做其他用途。  
3）MySQL主键应该是单列的，以便提高连接和筛选操作的效率。  
4）主键字段类型尽可能小，能用SMALLINT就不用INT，能用INT就不用BIGINT。
5）尽量保证不对主键字段进行更新修改，防止主键字段发生变化，引发数据存储碎片，降低IO性能。  
6）MySQL主键不应包含动态变化的数据，如时间戳、创建时间列、修改时间列等。  
7）MySQL主键应当有计算机自动生成。  
8）主键字段放在数据表的第一顺序。  
推荐采用数值类型做主键并采用auto_increment属性让其自动增长。  

5. 其他需要注意的地方  
尽可能设置每个字段为NOT NULL，除非有特殊的需求，原因如下：  
1）使用含有NULL列做索引的话会占用更多的磁盘空间，因为索引NULL列需要而外的空间来保存。  
2）进行比较的时候，程序会更复杂。  
3）含有NULL的列比较特殊，SQL难优化，如果是一个组合索引，那么这个NULL 类型的字段会极大影响整个索引的效率。  

6. 索引  
索引的优点：极大地加速了查询，减少扫描和锁定的数据行数。  
索引的缺点：占用磁盘空间，减慢了数据更新速度，增加了磁盘IO。  
添加索引有如下原则：  
1)选择唯一性索引。  
2)为经常需要排序、分组和联合操作的字段建立索引。  
3)为常作为查询条件的字段建立索引。  
4)限制索引的数据，索引不是越多越好。  
5)尽量使用数据量少的索引，对于大字段可以考虑前缀索引。  
6)删除不再使用或者很少使用的索引。  
7)结合核心SQL优先考虑覆盖索引。  
8)忌用字符串做主键。  

7. 范式  
范式是“符合某一种级别的关系模式的集合，表示一个关系内部各属性之间的联系的合理化程度”。
实际上可以理解为一张数据表的表结构所符合的某种设计标准的级别。  
1NF的定义为：符合1NF的关系中的每个属性都不可再分。实际上，1NF是所有关系型数据库的最基本要求  
2NF在1NF的基础之上，消除了非主属性对于码的**部分**函数依赖  
3NF在2NF的基础之上，消除了非主属性对于码的**传递**函数依赖  
反范式设计  
适当的使用冗余的反范式设计，以空间换时间有的时候会很高效。  
参考：[http://www.zhihu.com/question/24696366](http://www.zhihu.com/question/24696366){:target="_blank"}  

8. Mysql语句级优化  
1)性能查的读语句，在innodb中统计行数,建议另外弄一张统计表，采用myisam，定期做统计.
一般的对统计的数据不会要求太精准的情况下适用。  
2)尽量不要在数据库中做运算。  
3)避免负向查询和%前缀模糊查询。  
4)不在索引列做运算或者使用函数。  
5)不要在生产环境程序中使用select * from 的形式查询数据。只查询需要使用的列。  
6)查询尽可能使用limit减少返回的行数，减少数据传输时间和带宽浪费。  
7)where子句尽可能对查询列使用函数，因为对查询列使用函数用不到索引。  
8)避免隐式类型转换，例如字符型一定要用’’，数字型一定不要使用’’。   
9)所有的SQL关键词用大写，养成良好的习惯，避免SQL语句重复编译造成系统资源的浪费。  
10)联表查询的时候，记得把小结果集放在前面，遵循小结果集驱动大结果集的原则。  
11)开启慢查询，定期用explain优化慢查询中的SQL语句。  

9. 尽量避免SELECT *命令  
从表中读取越多的数据，查询会变得更慢。他增加了磁盘需要操作的时间，
还是在数据库服务器与WEB服务器是独立分开的情况下。你将会经历非常漫长的网络延迟，
仅仅是因为数据不必要的在服务器之间传输。始终指定你需要的列，这是一个非常良好的习惯  

10. 在新建临时表时，如果一次性插入数据量很大，
那么可以使用 select into 代替 create table,避免造成大量 log ,以提高速度  

11. 尽量使用数字型字段，若只含数值信息的字段尽量不要设计为字符型，这会降低查询和连接的性能，并会增加存储开销。
这是因为引擎在处理查询和连接时会逐个比较字符串中每一个字符，而对于数字型而言 只需要比较一次就够了。  

12. 索引并不是越多越好，索引固然可以提高相应的 select 的效率，但同时也降低了 insert 及 update 的效率，
因为 insert 或 update 时有可能会重建索引，所以怎样建索引需要慎重考虑，视具体情况而定。
一个表的索引数最好不要超过 6 个，若太多则应考虑一些不常使用到的列上建的索引是否有必要。  

13. 使用连接（JOIN）来代替子查询(Sub-Queries)  
MySQL从4.1开始支持SQL的子查询。这个技术可以使用SELECT语句来创建一个单列的查询结果，
然后把这个结果作为过滤条件用在另一个查询中。  
使用子查询可以一次性的完成很多逻辑上需要多个步骤才能完成的SQL操作，同时也可以避免事务或者表锁死，
并且写起来也很容易。但是，有些情况下，子查询可以被更有效率的连接（JOIN）替代。  
连接（JOIN）之所以更有效率一些，是因为 MySQL不需要在内存中创建临时表来完成这个逻辑上的需要两个步骤的查询工作。  
参考：[http://www.tutorialspoint.com/sql/sql-using-joins.htm](http://www.tutorialspoint.com/sql/sql-using-joins.htm){:target="_blank"}  

3. 在建有索引的字段上尽量不要使用函数进行操作。否则会放弃索引全盘扫描。  

http://www.tutorialspoint.com/sql/index.htm  
1.DDL - Data Definition Language:  
Command	Description  
CREATE	Creates a new table, a view of a table, or other object in database  
ALTER	Modifies an existing database object, such as a table.  
DROP	Deletes an entire table, a view of a table or other object in the database.  

2.DML - Data Manipulation Language:
Command	Description
SELECT	Retrieves certain records from one or more tables
INSERT	Creates a record
UPDATE	Modifies records
DELETE	Deletes records

3.DCL - Data Control Language:
Command	Description
GRANT	Gives a privilege to user
REVOKE	Takes back privileges granted from user

4.SQL Constraints:
NOT NULL Constraint: Ensures that a column cannot have NULL value.
DEFAULT Constraint: Provides a default value for a column when none is specified.
UNIQUE Constraint: Ensures that all values in a column are different.
PRIMARY Key: Uniquely identified each rows/records in a database table.
FOREIGN Key: Uniquely identified a rows/records in any another database table.
CHECK Constraint: The CHECK constraint ensures that all values in a column satisfy certain conditions.
INDEX: Use to create and retrieve data from the database very quickly.

5.Data Integrity:
Entity Integrity: There are no duplicate rows in a table.
Domain Integrity: Enforces valid entries for a given column by restricting the type, the format, or the range of values.
Referential integrity: Rows cannot be deleted, which are used by other records.
User-Defined Integrity: Enforces some specific business rules that do not fall into entity, domain or referential integrity.

6.An operator is a reserved word or a character used primarily in an SQL statement's 
WHERE clause to perform operation(s), such as comparisons and arithmetic operations.

7.Expressions:
	SELECT column1, column2, columnN 
	FROM table_name 
	WHERE [CONDITION|EXPRESSION];
7.1.Boolean Expressions:
	SELECT column1, column2, columnN 
	FROM table_name 
	WHERE SINGLE VALUE MATCHTING EXPRESSION;
	SELECT * FROM CUSTOMERS WHERE SALARY = 10000;
	
7.2.Numeric Expression:
	SELECT numerical_expression as  OPERATION_NAME
	[FROM table_name
	WHERE CONDITION];
7.3.Date Expressions:
	SELECT CURRENT_TIMESTAMP;
	
8.常用SQL语句：
{% highlight sql %}
CREATE DATABASE DatabaseName;
DROP DATABASE DatabaseName;
USE DatabaseName;
CREATE TABLE CUSTOMERS(
	ID   INT              NOT NULL,
	NAME VARCHAR (20)     NOT NULL,
	AGE  INT              NOT NULL,
	ADDRESS  CHAR (25) ,
	SALARY   DECIMAL (18, 2),       
	PRIMARY KEY (ID)
);
DESC CUSTOMERS;
DROP TABLE table_name;
INSERT INTO CUSTOMERS (ID,NAME,AGE,ADDRESS,SALARY)
VALUES (1, 'Ramesh', 32, 'Ahmedabad', 2000.00 );
INSERT INTO first_table_name [(column1, column2, ... columnN)] 
SELECT column1, column2, ...columnN 
FROM second_table_name
[WHERE condition];
SELECT column1, column2, columnN FROM table_name;
UPDATE table_name
SET column1 = value1, column2 = value2...., columnN = valueN
WHERE [condition];
DELETE FROM table_name
WHERE [condition];
SELECT FROM table_name
WHERE column LIKE '_XXXX%';//The '%' represents zero, one, or multiple characters. 
//The '_' represents a single number or character. The symbols can be used in combinations.
SELECT * FROM CUSTOMERS
LIMIT 3;
SELECT column-list 
FROM table_name 
[WHERE condition] 
[ORDER BY column1, column2, .. columnN] [ASC | DESC];
SELECT column1, column2
FROM table_name
WHERE [ conditions ]
GROUP BY column1, column2
SELECT NAME, SUM(SALARY) FROM CUSTOMERS
GROUP BY NAME;
SELECT DISTINCT column1, column2,.....columnN 
FROM table_name
WHERE [condition];
ALTER TABLE EMPLOYEES DROP CONSTRAINT EMPLOYEES_PK;
SELECT C.ID, C.NAME, C.AGE, O.AMOUNT 
FROM CUSTOMERS AS C, ORDERS AS O
WHERE  C.ID = O.CUSTOMER_ID;
SELECT  ID AS CUSTOMER_ID, NAME AS CUSTOMER_NAME
FROM CUSTOMERS
WHERE SALARY IS NOT NULL;
CREATE INDEX index_name
ON table_name (column_name);
DROP INDEX index_name;
TRUNCATE TABLE  table_name;
CREATE VIEW view_name AS
SELECT column1, column2.....
FROM table_name
WHERE [condition];
SELECT * FROM CUSTOMERS_VIEW;
SELECT column_name [, column_name ]
FROM   table1 [, table2 ]
WHERE  column_name OPERATOR
	(SELECT column_name [, column_name ]
	FROM table1 [, table2 ]
	[WHERE]);
{% endhighlight %}

9.高阶SQL语句：  
9.1	Having Clause：The HAVING clause enables you to specify conditions
 that filter which group results appear in the final results.
The WHERE clause places conditions on the selected columns, 
whereas the HAVING clause places conditions on groups created by the GROUP BY clause.
The HAVING clause must follow the GROUP BY clause in a query and must also precede the ORDER BY clause if used.
{% highlight sql %}
SELECT column1, column2
FROM table1, table2
WHERE [ conditions ]
GROUP BY column1, column2
HAVING [ conditions ]
ORDER BY column1, column2;
SELECT ID, NAME, AGE, ADDRESS, SALARY
FROM CUSTOMERS
GROUP BY age
HAVING COUNT(age) >= 2;
{% endhighlight %}
9.2 The COMMIT command is the transactional command used to save changes invoked by a transaction to the database.
The COMMIT command saves all transactions to the database since the last COMMIT or ROLLBACK command.
{% highlight sql %}
DELETE FROM CUSTOMERS
WHERE AGE = 25;
COMMIT;
{% endhighlight %}
9.3 The ROLLBACK command is the transactional command used to undo transactions that have not already been saved to the database.
The ROLLBACK command can only be used to undo transactions since the last COMMIT or ROLLBACK command was issued.	
{% highlight sql %}
DELETE FROM CUSTOMERS
WHERE AGE = 25;
ROLLBACK;
{% endhighlight %}
9.4 A SAVEPOINT is a point in a transaction when you can roll the transaction back to a 
certain point without rolling back the entire transaction.
{% highlight sql %}
SAVEPOINT SAVEPOINT_NAME;
ROLLBACK TO SAVEPOINT_NAME;
RELEASE SAVEPOINT SAVEPOINT_NAME;
{% endhighlight %}
10.Properties of Transactions:
Transactions have the following four standard properties, usually referred to by the acronym ACID:
Atomicity: ensures that all operations within the work unit are completed successfully; otherwise, 
the transaction is aborted at the point of failure, and previous operations are rolled back to their former state.
Consistency: ensures that the database properly changes states upon a successfully committed transaction.
Isolation: enables transactions to operate independently of and transparent to each other.
Durability: ensures that the result or effect of a committed transaction persists in case of a system failure.

11.Temporary Tables are a great feature that lets you store and process intermediate results by using the same selection, 
update, and join capabilities that you can use with typical SQL Server tables.
The temporary tables could be very useful in some cases to keep temporary data. 
The most important thing that should be known for temporary tables is that they will be deleted 
when the current client session terminates.
