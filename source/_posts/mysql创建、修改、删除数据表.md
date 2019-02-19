---
title: mysql创建、修改、删除数据表
comments: true
date: 2019-02-19 15:15:33
categories: 数据库
tags: mysql
img: http://qiniu1.lxfriday.xyz/image/20190128-mysql.jpeg
---

### 主键约束

```sql
> # 单主键
> CREATE TABLE test1
(
  id INT(11),
  name VARCHAR(30),
  PRIMARY KEY(id)
)

> # 多字段联合主键
> CREATE TABLE test2 
(
  id INT(10),
  name VARCHAR(30),
  PRIMARY KEY(id,name)
)
```

### 外键约束

外键可以在两个表的数据之间建立连接，它可以是一列或者多列，一个表可以有一个或者多个外键。外键对应的是参展完整性，一个表的外键可以为空值，若不为空值，则每一个外键值必须等于另一个表中主键的值。

1. 外键：__首先它是表中的一个字段，它可以不是本表的主键，但对应另外一个表的主键。外键的主要作用是保证数据引用的完整性，定义外键后，不允许删除在另一个表中具有关联关系的行。__
1. 主表（父表）：对于具有关联关系的两个表而言，相关连字段中主键所在的那个表为主表。
1. 从表（子表）：外键所在的那个表为从表。

创建外键的语法

```sql
[CONSTRAINT <外键名>] FOREIGN KEY 字段名1 [,字段名2,...] REFERENCE <主表名> 主键列1 [,主键列2]
```

创建具有外键约束的两张表

1、创建一个部门表 tb_dept1 
```sql
> CREATE TABLE tb_dept1
(
    id INT(10) PRIMARY KEY,
    name VARCHAR(22) NOT NULL,
    location VARCHAR(50)
);
```

2、 定义数据表 tb_emp5 ，并在 tb_emp5 表上创建外键约束
```sql
> CREATE TABLE tb_emp5
(
    id INT(11) PRIMARY KEY,
    name VARCHAR(25),
    deptId INT(11),
    salary FLOAT,
    CONSTRAINT fk_emp_dept1 FOREIGN KEY(deptId) REFERENCES tb_dept1(id)
);
```

当 tb_emp5 表中的记录关联到 tb_dept1 中的 id 时，无法删除 tb_dept1 对应 id 的记录，需要先删除子表外键的记录，之后才能删除主表的记录

### 查看数据表结构

```sql
> # 以下四种方法结果一致
> DESC test1;
> DESCRIBE test1;
> SHOW COLUMNS FROM test1;
> EXPLAIN test1;
```

### 查看表的详细结构

```sql
> SHOW CREATE TABLE test1;
```

### 修改表名

```sql
> ALTER TABLE testapp RENAME TO test1;
```

### 修改字段的数据类型
语法规则
```sql
> # 这种修改方式会覆盖掉该字段原有的所有属性
> ALTER TABLE <表名> MODIFY <字段名> <数据类型>;
```
例子
```sql
> ALTER TABLE test111 MODIFY iid INT(11) UNSIGNED NOT NULL DEFAULT 100;
+-------+------------------+------+-----+---------+----------------+
| Field | Type             | Null | Key | Default | Extra          |
+-------+------------------+------+-----+---------+----------------+
| id    | int(10) unsigned | NO   | PRI | NULL    | auto_increment |
| iid   | int(11) unsigned | NO   | PRI | 100     |                |
+-------+------------------+------+-----+---------+----------------+
```

### 修改字段名
语法规则，新数据类型也会完全覆盖原有数据类型
```sql
> ALTER TABLE <表名> CHANGE <旧字段名> <新字段名> <新数据类型>;
```
```sql
> ALTER TABLE test111 CHANEG iid iiid INT(10);
```

### 添加字段
```sql
> ALTER TABLE <表名> ADD <新字段名> <数据类型> [约束条件] [FIRST | AFTER 已存在的字段];
```
```sql
> ALTER TABLE test111 ADD name CHAR(11) DEFAULT NULL;
```

### 删除字段
```sql
> ALTER TABLE <表名> DROP <字段名>;
```

### 修改存储引擎
```sql
> ALTER TABLE test111 ENGINE=MyISAM;
```
### 删除外键约束
语法规范
```sql
> ALTER TABLE <表名> DROP FOREIGN KEY <外键约束名>;
```
子表的外键名为 fk_emp_dept1
```sql
> ALTER TABLE tb_emp5 DROP FOREIGN KEY fk_emp_dept1;
```
