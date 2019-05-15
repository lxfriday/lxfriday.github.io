---
title: mysql索引
comments: true
date: 2019-02-22 11:10:02
categories: 数据库
tags: mysql
img: https://qiniu1.lxfriday.xyz/image/20190128-mysql.jpeg
---

索引是对数据库中一列或多列的值进行排序的一种结构。使用索引可以提高数据库特定数据的查询速度。

索引是一个单独的、存储在磁盘上的数据库结构，包括对数据表里所有记录的引用指针。使用索引可以快速找出在某个或多个列中有特定值的行，MySQL 中的所有列类型都可以被索引，对相关列使用索引是提高查询操作速度的最佳途径。

索引是在存储引擎中实现的，每种存储引擎的索引都不一定完全相同，并且一种存储引擎也不一定支持所有的索引类型。

MySQL 中索引的存储类型有两种：BTREE 和 HASH，其和表的存储引擎有关。MyISAM 和 InnoDB 存储引擎只支持 BTREE 索引；MEMORY 和 HEAP 存储引擎可以支持 HASH 和 BTREE 索引。

索引的优点主要有以下几条：
- 创建索引可以保证数据库表中每一行数据的唯一性。
- 大大加快数据的查询速度，这也是创建索引最主要的原因。

缺点：
- 创建索引和维护索引需要耗费时间，并且随着数据量的增加所耗费的时间也会增加。
- 除了数据表要占数据空间，每一个索引还要占一定的物理空间，如果有大量的索引，索引可能会使数据文件更快达到最大文件尺寸。
- 当对表中的数据进行增加、删除和修改的时候，索引也要动态地维护，这样就降低了数据的维护速度。

## 索引的分类
### 1、普通索引和唯一索引
普通索引是 MySQL 中的基本索引类型，允许定义索引的列中插入重复值和空值。

唯一索引是指索引列的值必须唯一，但允许有空值。如果是组合索引，则列值得组合必须唯一。__主键索引是一种特殊的唯一索引，不允许有空值。__

### 2、单列索引和组合索引
### 3、全文索引
全文索引的类型为 FULLTEXT，在定义索引的列上支持值的全文查找，允许在这些索引列中插入重复值和空值。全文索引可以在 CHAR、VARCHAR 或者 TEXT 类型的列上创建。__在 MySQL 中，只有 MyISAM 存储引擎支持全文索引。__
### 4、空间索引 
空间索引是对空间数据类型的字段建立的索引。 MySQL 中的空间数据类型有4种， GEOMETRY、POINT、LINESTRING 和 POLYGON。在 MySQL 中使用 SPATIAL 关键字进行扩展。__创建空间索引的列，必须将其声明为 NOT NULL。空间索引智能在存储引擎为 MyISAM 的表中创建__
## 索引的设计原则
- 索引会影响 INSERT、DELETE、UPDATE 等忽悠局的性能，当更改表中的数据时，索引也要进行调整和更新。
- 避免对经常更新的表进行过多的索引。
- 数据量小的表最好不要使用索引。
- 当唯一性是某种数据本身的特征时，指定唯一索引。
- 在频繁进行排序或分组(GROUP BY 或 ORDER BY)的列上建立索引，如果待排序的列有多个，可以在这些列上建立组合索引。
## 创建索引
- 在创建表的定义语句 `CREATE TABLE` 创建索引；
- 使用 `ALTER TABLE` 在已有的表上创建索引；
- 使用 `CREATE INDEX` 语句在已有的表上添加索引；
### 创建表的时候创建索引
```sql
> CREATE TABLE table_name [col_name data_type] [UNIQUE | FULLTEXT | SPATIAL] [INDEX | KEY] [index_name] (col_name[length]) [ASC | DESC]  
```
index 与 KEY 为同义词，两者作用相同，用来指定创建索引。indexname 指定索引的名称，默认 colname 为索引值。length 为可选参数，表示索引的长度。只有字符串类型的字段才能指定索引长度。

```sql
> CREATE TABLE testindex1 (
    bookname VARCHAR(100) NOT NULL,
    INDEX(bookname)
)
> SHOW CREATE TABLE testindex1\G;
*************************** 1. row ***************************
       Table: testindex1
Create Table: CREATE TABLE `testindex1` (
  `bookname` varchar(100) NOT NULL,
  KEY `bookname` (`bookname`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```

#### 创建唯一索引
```sql
> CREATE TABLE testindex2 (
    bookname VARCHAR(100) NOT NULL,
    UNIQUE INDEX UniqInd(bookname)
);
```

#### 创建单列索引
```sql
> CREATE TABLE testindex3 (
    bookname VARCHAR(100) NOT NULL,
    INDEX SingleIdx(bookname(20))
);
```

#### 创建组合索引
```sql
> CREATE TABLE testindex4 (
    id INT NOT NULL,
    name CHAR(30) NOT NULL,
    age INT NOT NULL,
    info VARCHAR(255),
    INDEX MultiIdx(id,name,age)
);

> SHOW CREATE TABLE testindex4\G;
*************************** 1. row ***************************
       Table: testindex4
Create Table: CREATE TABLE `testindex4` (
  `id` int(11) NOT NULL,
  `name` char(30) NOT NULL,
  `age` int(11) NOT NULL,
  `info` varchar(255) DEFAULT NULL,
  KEY `MultiIdx` (`id`,`name`,`age`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```
#### 创建全文索引
FULLTEXT 全文索引可用于全文搜索。只有 MyISAM 存储引擎支持，并且只能在数据类型为 CHAR、VARCHAR、TEXT 列上建立全文索引。

```sql
> CREATE TABLE testindex5 (
    id INT NOT NULL,
    name CHAR(30) NOT NULL,
    age INT NOT NULL,
    info VARCHAR(255),
    FULLTEXT INDEX FullTextIdx(info)
) ENGINE=MyISAM;

> # 查看表中的索引信息
> SHOW INDEX FROM testindex5\G;
*************************** 1. row ***************************
        Table: testindex5
   Non_unique: 1
     Key_name: FullTextIdx
 Seq_in_index: 1
  Column_name: info
    Collation: NULL
  Cardinality: NULL
     Sub_part: NULL
       Packed: NULL
         Null: YES
   Index_type: FULLTEXT
      Comment: 
Index_comment:
```

全文索引非常适合在大型数据集中使用，对于小的数据集，它的用处可能比较小。

### 在已有的表上创建索引
#### 使用 ALTER TABLE
```sql
> ALTER TABLE table_name ADD [UNIQUE | FULLTEXT | SPATIAL] [INDEX | KEY] [index_name] (col_name[length],...) [ASC | DESC];
```

```sql
> ALTER TABLE book ADD INDEX BkNameIdx(bookname(30));
```

#### 使用 CREATE INDEX 创建索引
```sql
> CREATE [UNIQUE | FULLTEXT | SPATIAL] INDEX index_name ON table_name (col_name[length],...) [ASC | DESC];
```

```sql
> CREATE INDEX BkNameIdx ON book(bookname);
```

### 删除索引
#### 使用 ALTER TABLE 语句删除索引
```sql
> ALTER TABLE table_name DROP INDEX index_name;
```

#### 使用 DROP INDEX
```sql
> DROP INDEX index_name ON table_name;
```
