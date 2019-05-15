---
title: mysql查询数据
comments: true
date: 2019-02-24 10:45:36
categories: 数据库
tags: mysql
img: https://qiniu1.lxfriday.xyz/image/20190128-mysql.jpeg
---

<input type="hidden" value="20190220" />

MySQL 中查询数据的基本语句为 SELECT 语句，基本用法如下

```sql
> SELECT 
    {* | <字段列表>}
    [
        FROM <表1>,<表2>...
        [
            WHERE <表达式>
            [GROUP BY <group by definition>]
            [HAVING <expression> [{<operator> <expression>}...]]
            [ORDER BY <order by definition>]
            [LIMIT [<offset>,] <row count>]
        ]
    ]
    
> SELECT [字段1,字段2,...,字段n]
    FROM [表或视图]
    WHERE [查询条件];
```

### WHERE 条件判断符

|操作符|说明|
|:-:|:-:|
|=|等于|
|<>,!=|不相等|
|<|小于|
|<=|小于或等于
|>|大于|
|>=|大于或等于|
|BETWEEN|位于两值之间|

### 带 IN 关键字查询
`IN (x, y, z)`
IN 操作符用来查询满足指定范围的记录，使用时，将所有检索条件用括号括起来，检索条件之间用逗号分隔，只要满足条件范围内的一个值即为匹配项。

`IN`
```sql
> SELECT a,b,c FROM alpha WHERE a IN (1, 2, 3);
```

`NOT IN`
```sql
> SELECT a,b,c FROM alpha WHERE a NOT IN (1, 2, 3);
```

### 带 BETWEEN AND 的范围查询
`BETWEEN AND`
BETWEEN AND 用来查询某个范围内的值，该操作符需要两个参数，即范围的开始值和结束值。如果字段满足置顶的范围查询条件，则这些记录被返回。

```sql
> SELECT a,b,c FROM alpha WHERE a BETWEEN 2 AND 10;
```

### 查询空值
`IS NULL`
```sql
> SELECT * FROM alpha WHERE a IS NULL;
```

### 带 AND 的多条件查询

```sql
> SELECT a,b,c from alpha WHERE a='1' AND b > 1000;
```

### 带 OR 的多条件查询

```sql
> SELECT a,b,c from alpha WHERE a='1' OR b > 1000;
```

### 查询结果不重复
`DISTINCT`
```sql
> SELECT DISTINCT a from alpha;
```

### 对查询结果排序
`ORDER BY`
```sql
> SELECT * FROM alpha ORDER BY a;
```

### 多列排序
`ORDER BY a, b`
在对多列进行排序的时候，首先排序的第一列必须有相同的列值，才会对第二列进行排序，如果第一列数据中的所有制都是唯一的，将不再对第二列进行排序。

```sql
> SELECT a, b, c FROM alpha ORDER BY a ASC, b DESC;

> select DISTINCT * from selecttest1;
  +------+------+------+
  | a    | b    | c    |
  +------+------+------+
  |    1 | a    |    1 |
  |    2 | b    |    2 |
  |    2 | b    |    3 |
  +------+------+------+

>  SELECT DISTINCT * from selecttest1 ORDER BY a, c DESC;
  +------+------+------+
  | a    | b    | c    |
  +------+------+------+
  |    1 | a    |    1 |
  |    2 | b    |    3 | <- 现在排在前面了
  |    2 | b    |    2 |
  +------+------+------+
```

### 分组查询
`[GROUP BY 字段] [HAVING <条件表达式>]`

```sql
> SELECT a, COUNT(*) as a_count FROM alpha GROUP BY a;
```

条件查询

```sql
> SELECT a, COUNT(*) as a_count FROM alpha GROUP BY a HAVING a > 20 ORDER BY a_count DESC;
```

__HAVING 和 WHERE 的区别__

`HAVING` 在数据分组之后进行过滤来选择分组

`WHERE` 在分组之前用来选择记录

### 在 GROUP BY 子句中使用 WITH ROLLUP
使用 `WITH ROLLUP` 关键字之后，在所有查询出的分组记录之后添加一条记录，该记录计算查询出的所有记录的综合，即统计记录数量。

```sql
> SELECT COALESCE(c, 'total') as c,COUNT(*) as a_count FROM selecttest1 GROUP BY c WITH ROLLUP;

+-------+---------+
| c     | a_count |
+-------+---------+
| 1     |       5 |
| 2     |       4 |
| 3     |       1 |
| total |      10 | <- 没有 COALESCE 则 total 为 NULL
+-------+---------+
```

### GROUP BY 和 ORDER BY 一起使用

```sql
> SELECT o_num,SUM(quantity * item_price) AS orderTotal
    FROM orderitems
    GROUP BY o_num
    HAVING SUM(quantity * item_price) >= 100
    ORDER BY orderTotal;
```

__使用 ROLLUP 时，不能同时使用 ORDER BY 子句进行结果排序，即 ROLLUP 和 ORDER BY 是相互排斥的__

### 使用 LIMIT 限制查询结果的数量
`LIMIT [位置偏移量x,] 行数y` 从第 x + 1 条记录开始，总查询 y 条记录

```sql
> SELECT * FROM alpha LIMIT 4, 3; # 从第5条开始，查询3条记录
```

### 连接查询
当查询数据时，通过连接操作可以查询出存放在多个表中的不同实体的信息。当两个或多个表中存在相同意义的字段时，便可以通过这些字段对不同的表进行连接查询。

#### 内连接查询 INNER JOIN

```sql
> select * from testjoin1; # 表1 testjoin1
+----+--------------+----------+-------------------------+
| id | isbn         | bookname | url                     |
+----+--------------+----------+-------------------------+
|  1 | aaaaaaaaaaaa | a 书     | http://book.com?q=a 书  |
|  2 | bbbbbbbbbbbb | b 书     | http://book.com?q=b 书  |
|  3 | cccccccccccc | c 书     | http://book.com?q=c 书  |
|  4 | dddddddddddd | d 书     | http://book.com?q=d 书  |
|  5 | eeeeeeeeeeee | e 书     | http://book.com?q=e 书  |
|  6 | ffffffffffff | f 书     | http://book.com?q=f 书  |
+----+--------------+----------+-------------------------+

select * from testjoin2; # 表2 testjoin2
+----+----------+--------------+
| id | username | isbn         |
+----+----------+--------------+
|  1 | lx1      | aaaaaaaaaaaa |
|  2 | lx1      | bbbbbbbbbbbb |
|  3 | lx1      | cccccccccccc |
|  4 | lx2      | eeeeeeeeeeee |
+----+----------+--------------+

> # LEFT JOIN
> select a.bookname,a.url,b.username,a.isbn from testjoin1 a LEFT JOIN testjoin2 b ON a.isbn =b.isbn;
+----------+-------------------------+----------+--------------+
| bookname | url                     | username | isbn         |
+----------+-------------------------+----------+--------------+
| a 书     | http://book.com?q=a 书  | lx1      | aaaaaaaaaaaa |
| b 书     | http://book.com?q=b 书  | lx1      | bbbbbbbbbbbb |
| c 书     | http://book.com?q=c 书  | lx1      | cccccccccccc |
| e 书     | http://book.com?q=e 书  | lx2      | eeeeeeeeeeee |
| d 书     | http://book.com?q=d 书  | NULL     | dddddddddddd |
| f 书     | http://book.com?q=f 书  | NULL     | ffffffffffff |
+----------+-------------------------+----------+--------------+

> # RIGHT JOIN
> select a.bookname,a.url,b.username,a.isbn from testjoin1 a RIGHT JOIN testjoin2 b ON a.isbn =b.isbn;
+----------+-------------------------+----------+--------------+
| bookname | url                     | username | isbn         |
+----------+-------------------------+----------+--------------+
| a 书     | http://book.com?q=a 书  | lx1      | aaaaaaaaaaaa |
| b 书     | http://book.com?q=b 书  | lx1      | bbbbbbbbbbbb |
| c 书     | http://book.com?q=c 书  | lx1      | cccccccccccc |
| e 书     | http://book.com?q=e 书  | lx2      | eeeeeeeeeeee |
+----------+-------------------------+----------+--------------+

> # INNER JOIN
> select a.bookname,a.url,b.username,a.isbn from testjoin1 a INNER JOIN testjoin2 b ON a.isbn =b.isbn;
+----------+-------------------------+----------+--------------+
| bookname | url                     | username | isbn         |
+----------+-------------------------+----------+--------------+
| a 书     | http://book.com?q=a 书  | lx1      | aaaaaaaaaaaa |
| b 书     | http://book.com?q=b 书  | lx1      | bbbbbbbbbbbb |
| c 书     | http://book.com?q=c 书  | lx1      | cccccccccccc |
| e 书     | http://book.com?q=e 书  | lx2      | eeeeeeeeeeee |
+----------+-------------------------+----------+--------------+
```



连接查询将查询多个表中相互关联的行，内连接时，只返回符合查询条件和连接条件的行。如果要返回没有关联的行中数据，即返回查询结果集合中不仅包含符合连接条件的行而且还包括左表（左外连接或左连接）、右表（右外连接或右连接）或两个边连接（全外连接）中的所有数据行，这时就要用到外连接查询。
 
外连接分为：左外连接、右外连接
- LEFT JOIN（左连接）：返回左表中的所有记录和右表中与连接字段相等的记录。
- RIGTH JOIN（右连接）：返回右表中的所有记录和右表中与连接字段相等的记录。 

### 子查询
子查询是指一个查询语句嵌套在另一个查询语句内部的查询。在 SELECT 子句中先计算子查询，子查询结果作为外层另一个查询的过滤条件，查询可以基于一个表或者多个表。子查询中常用的操作符有 `ANY`(`SOME`)、`ALL`、`IN`、`EXISTS`；

#### ANY(SOME)
ANY 和 SOME 是同义词，表示满足其中任一条件，他们允许创建一个表达式对子查询的返回值进行比较，只要满足内层子查询中的任何一个比较条件，就返回一个结果作为外层查询的条件。

```sql
> CREATE TABLE tb1 (num1 INT NOT NULL);
> CREATE TABLE tb2 (num2 INT NOT NULL);
> INSERT INTO tb1 VALUES (1),(5),(13),(27);
> INSERT INTO tb2 VALUES (6),(14),(11),(20);

> SELECT num1 FROM tb1 WHERE num1 > ANY (SELECT num2 from tb2);
+------+
| num1 |
+------+
|   13 |
|   27 |
+------+
```

#### ALL
使用 ALL 关键字时，需要同时满足所有内层查询的条件。

ALL 关键字位于比较操作符的后面，表示与子查询返回的所有值比较为 TRUE ，则返回 TRUE；

```sql
> SELECT num1 FROM tb1 WHERE num1 > ALL (SELECT num2 from tb2);
+------+
| num1 |
+------+
|   27 |
+------+
```

#### EXISTS
EXISTS 关键字后面的参数是一个任意的子查询，系统对子查询进行运算以判断它是否返回行，如果至少返回一行，则 EXISTS 的结果为 TRUE，此时外层查询语句将进行查询；如果没有返回任何行，那么 EXISTS 返回的结果是 false，此时外层语句将不进行查询。

#### IN
用 IN 关键字进行子查询时，内层查询语句只返回一个数据列，这个数据列里的值将提供给外层查询语句进行比较操作。

```sql
> SELECT c_id FROM orders WHERE o_num IN (SELECT o_num FROM orderitems WHERE f_id='c0');
```

#### 带比较运算的子查询

```sql
> SELECT s_id,f_name FROM fruits
    WHERE s_id=
    (SELECT s1,s_id FROM suppliers AS s1 WHERE s1.s_city='Tianjin');
```

### 合并查询结果 UNION
使用 UNION 关键字，可以给出多条 SELECT 语句，并将它们的结果组合成一个结果集。合并时，两个表对应的列数和数据类型必须相同。SELECT 语句之间使用 UNION 或者 UNION ALL 关键字分隔，不使用关键字 ALL，执行的时候会删除重复的记录，所有返回的行都是唯一的。__使用关键字 ALL 的作用是不删除重复行也不对结果进行自动排序。__  

```sql
> SELECT isbn FROM testjoin1 UNION ALL SELECT isbn FROM testjoin2;;
  +--------------+
  | isbn         |
  +--------------+
  | aaaaaaaaaaaa |
  | bbbbbbbbbbbb |
  | cccccccccccc |
  | dddddddddddd |
  | eeeeeeeeeeee |
  | ffffffffffff |
  | aaaaaaaaaaaa |
  | bbbbbbbbbbbb |
  | cccccccccccc |
  | eeeeeeeeeeee |
  +--------------+

> SELECT isbn FROM testjoin1 UNION  SELECT isbn FROM testjoin2;
+--------------+
| isbn         |
+--------------+
| aaaaaaaaaaaa |
| bbbbbbbbbbbb |
| cccccccccccc |
| dddddddddddd |
| eeeeeeeeeeee |
| ffffffffffff |
+--------------+
```

### 使用正则表达式查询
使用关键字 REGEXP
```sql
> SELECT * FROM fruits WHERE f_name REGEXP '^b'; # 查询 f_name 以b开头的字符串
```
