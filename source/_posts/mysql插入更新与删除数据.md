---
title: mysql插入更新与删除数据
comments: true
date: 2019-02-22 15:15:01
categories: 数据库
tags: mysql
img: https://qiniu1.lxfriday.xyz/image/20190128-mysql.jpeg
---

## 插入
规范
```sql
> INSERT INTO table_name (column_list) VALUES (value_list);

> # 同时插入多条数据
> INSERT INTO table_name (column_list) VALUES (value_list),(value_list),(value_list)...;
```
一个同时插入多行记录的 INSERT 语句等同于多个单行插入记录的 INSERT 语句，但是一次插入多行记录的 INSERT 语句在处理时，效率更高。

将查询结果插入表中

```sql
> # MySQL 不关心返回的列名，它根据列的位置进行插入
> INSERT INTO table_name1 (column_list1) SELECT (column_list2) FROM table_name2 WHERE (condition);
```

## 更新数据
```sql
> UPDATE table_name SET column_name1=value1,column_name2=value2,... where (condition);

> UPDATE person SET age=15,name='lx' WHERE id = 11;
> UPDATE person SET age=15,name='lx' WHERE id BETWEEN 19 AND 22;
```

## 删除数据
```sql
> DELETE FROM table_name [WHERE <condition>];
```

如果想要删除表中的所有记录，可使用 `TRUNCATE TABLE table_name`。TRUNCATE 会直接删除表并重建一张表。

