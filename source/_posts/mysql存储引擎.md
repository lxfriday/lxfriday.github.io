---
title: mysql存储引擎
comments: true
date: 2019-02-19 13:03:04
categories: 数据库
tags: mysql
img: https://qiniu1.lxfriday.xyz/image/20190128-mysql.jpeg
---

使用 `SHOW ENGINES;` 查看当前版本的数据库支持的存储引擎

```sql
> # 查看当前的数据库版本
> SELECT VERSION();
+-----------+
| VERSION() |
+-----------+
| 5.7.23    |
+-----------+

> SHOW ENGINES;
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| Engine             | Support | Comment                                                        | Transactions | XA   | Savepoints |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| InnoDB             | DEFAULT | Supports transactions, row-level locking, and foreign keys     | YES          | YES  | YES        |
| MRG_MYISAM         | YES     | Collection of identical MyISAM tables                          | NO           | NO   | NO         |
| MEMORY             | YES     | Hash based, stored in memory, useful for temporary tables      | NO           | NO   | NO         |
| BLACKHOLE          | YES     | /dev/null storage engine (anything you write to it disappears) | NO           | NO   | NO         |
| MyISAM             | YES     | MyISAM storage engine                                          | NO           | NO   | NO         |
| CSV                | YES     | CSV storage engine                                             | NO           | NO   | NO         |
| ARCHIVE            | YES     | Archive storage engine                                         | NO           | NO   | NO         |
| PERFORMANCE_SCHEMA | YES     | Performance Schema                                             | NO           | NO   | NO         |
| FEDERATED          | NO      | Federated MySQL storage engine                                 | NULL         | NULL | NULL       |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+

> # 查看当前的默认存储引擎相关信息
> SHOW VARIABLES LIKE '%storage_engine%'; 
+----------------------------------+--------+
| Variable_name                    | Value  |
+----------------------------------+--------+
| default_storage_engine           | InnoDB |
| default_tmp_storage_engine       | InnoDB |
| disabled_storage_engines         |        |
| internal_tmp_disk_storage_engine | InnoDB |
+----------------------------------+--------+
```

## 1、InnoDB 存储引擎
InnoDB 是事物型数据的首选引擎，支持事务安全表（ACID），支持行锁定和外键。 MySQL 5.5.5 之后，__InnoDB作为默认存储引擎__。

1. InnoDB 给 MySQL 提供了具有提交、回滚和崩溃恢复能力的事务安全（ACID兼容）存储引擎。
1. InnoDB 是为提高处理巨大数据量的性能设计的。
1. InnoDB 存储引擎为在主内存中缓存数据和索引而维持它自己的缓冲池。
1. InnoDB 支持外键完整性约束（FOREIGN KEY）。
1. InnoDB 被用在众多需要高性能的大型数据库站点上。
    - InnoDB 不创建目录，使用时，MySQL 将在 MySQL 数据目录下创建一个名为 ibdata1 的 10MB 大小的自动扩展数据文件，以及名为 ib_logfile0 和 ib_logfile1 的两个 5MB 大小的日志文件。

## 2、MyISAM 存储引擎
MyISAM 是在 Web、数据仓库和其他应用环境下最常使用的存储引擎之一。MyISAM 拥有较高的插入、查询速度，但不支持事务。

1. 在支持大文件的文件系统和操作系统上，大文件（达 63 位文件长度）被支持。
1. 当把删除和更新及插入操作混合使用的时候，将产生更少的碎片。
1. 每个 MyISAM 表的最大索引数是 64 ，可以通过编译来改变，每个索引最大的列数是 16 。
1. 最大的键长度是 1000 字节，也可以通过编译来改变。
1. `BLOB` 和 `TEXT` 列可以被索引。
1. 被允许在索引的列中出现 `NULL` 值，这个值占每个键的 0 ~ 1 个字节。
1. 每个 MyISAM 类型的表都有一个 `AUTO_INCREMENT` 的内部列，当执行 `INSERT` 和 `UPDATE` 操作的时候， `AUTO_INCREMENT` 列将会被刷新。所以 MyISAM 类型表的 `AUTO_INCREMENT` 列更新比 InnoDB 类型的 `AUTO_INCREMENT` 更快。
1. 可以把数据文件和索引文件放在不同目录。
1. 每个字符列可以有不同的字符集。
1. `VARCHAR` 和 `CHAR` 列的长度可以多达 64KB。

使用 MyISAM 引擎创建数据库，将产生3个文件。文件的名字以表的名字开始，扩展名可以指出文件类型：表定义文件为 __.frm__ ，数据文件的扩展名为 __.MYD(MYData)__ ，索引文件的扩展名是 __.MYI(MYIndex)__。

## 3、MEMORY 存储引擎
MEMORY 存储引擎可以将表中的数据存储到内存中，为查询和引用其他表数据提供快速访问。

1. 可以有多达 32 个索引，每个索引有 16 列，以及 500 字节的最大键长度。
1. 可以执行 `HASH` 和 `BTREE` 索引。
1. 可以在 MEMORY 表中有非唯一键。
1. MEMORY 存储引擎不支持 `BLOB` 和 `TEXT` 列。
1. 支持 `AUTO_INCREMENT` 列和对可包含 `NULL` 值的列的索引。
1. MEMORY 表的内容被存放在内存中，内存是 MEMORY 表和服务器在查询处理时的空闲中创建的内部表共享。
1. 当不再需要 MEMORY 表的内容时，要释放表占用的内存，应该执行 `DELETE FROM` 或 `TRUNCATE TABLE`(清除表中的所有数据) ，或者`DROP TABLE`（删除整个表）。 

对比

|功能|MyISAM|MEMORY|InnoDB|
|:-:|:-:|:-:|:-:|:-:|
|存储限制|256TB|RAM|64TB|
|支持事务|N|N|Y|
|支持全文索引|Y|N|N|
|支持数据索引|Y|Y|Y|
|支持哈希索引|N|Y|N|
|支持数据缓存|N|N/A|Y|
|支持外键|N|N|Y|

注意：外键约束不能跨引擎使用。MySQL 支持多种存储引擎，每一个表都可以指定一个不同的存储引擎，但是，外键约束是用来保证数据的参照完整性的，如果表之间需要关联外键，却指定了不同的存储引擎，这些表之间是不能创建外键约束的。




