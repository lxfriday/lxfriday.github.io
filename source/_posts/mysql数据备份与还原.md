---
title: mysql数据备份与还原
comments: true
top: false
date: 2019-02-26 10:58:06
categories: 数据库
tags: mysql
img: https://qiniu1.lxfriday.xyz/image/20190128-mysql.jpeg
---

数据备份是数据库管理员非常重要的工作。系统以为崩溃或者硬件的损坏都可能导致数据丢失，因此 MySQL 管理员应该定期备份数据，使得在意外情况出现时，尽可能减少损失。

## 使用 mysqldump 命令备份
mysqldump 命令可以将数据备份成一个文本文件，该文件实际上包含多个 CREATE 和 INSERT 语句，使用这些语句可以重新创建表和插入数据。

基本语法格式

```sql
> mysqldump -u user -p password dbname [tbname[, tbname]] > filename.sql
```

- user 表示用户名称
- host 表示登录用户的主机名称
- password 为登录密码
- dbname 为需要备份的数据库名称
- tbname 为要备份的数据表
- filename.sql 为备份的目的文件

### 备份单个数据库中的所有表

```sql
> mysqldump -u root -p runoob > /User/lxxxxx/test.sql
Enter password: **
```
密码输入之后，将会自动将数据导出到 /User/lxxxxx/test.sql 文件

### 备份数据库中的某个表

```sql
> mysqldump -u user -h host -p dbname [tbname[ tbname]] > filename.sql;
```

```sql
> mysqldump -u root -p booksDB books > /user/lxxx/test.sql;
```

### 备份多个数据库

```sql
> mysqldump -u user -h host -p --databases [dbname [dbname]] > filename.sql;
```

### 备份所有数据库

```sql
> mysqldump -u user -h host -p --all-databases > filename.sql;
```

## 直接复制整个数据库目录
因为 MySQL 表保存为文件方式，所以可以直接复制 MySQL 数据库的存储目录及文件进行备份。

在 Windows 下，存放数据库的目录通常为

```bash
> C:\Documents and Settings\All Users\application Data\MySQL\MySQL Server 5.6\data
```

在 Linux 平台下，数据库目录位置通常为

```bash
> /var/lib/mysql
```

要想保持备份的一致性，备份前需要对相关表执行 `LOCK TABLES` 操作，然后对表执行 `FLUSH TABLES`。这样当复制数据库目录中的文件时，允许其他客户继续查询表。

__这种方法虽然简单，但并不是最好的方法。因为这种方法对 InnoDB 存储引擎的表不适用。使用这种方法的数据最容易还原到相同版本的服务器中，不同的版本可能不兼容__

在 MySQL 版本号中，第一个数字表示主版本号，主版本号码相同的 MySQL 数据库的文件格式相同。

## 使用 mysql 命令还原
对于已经备份的包含 CREATE、INSERT 语句的文本文件，可以使用 mysql 命令导入数据库中。

```sql
> mysql -u user -p [dbname] < filename.sql
```

filename.sql 如果是 mysqldump 工具创建的包含数据库语句的文件，执行的时候不需要指定数据库名。

使用 mysql 命令将 `C:\backup\booksdb.sql` 文件中的备份导入数据库中

```sql
> mysql -u root -p booksDB < C:\backup\booksdb.sql
```

执行该语句前，必须现在 MySQL 服务器中创建 booksDB 数据库，如果不存在此数据库，恢复过程将会出错。

如果已经登录 MySQL 服务器，可以使用 __source__ 命令导入 sql 文件。
```sql
> source filename
```

整个过程如下
```sql
> use booksDB;
> source C:\backup\books.sql;
```

命令执行之后，会列出备份文件 books 中每一条语句的执行结果。 source 命令执行成功之后， books.sql 文件中的语句会全部导入现有数据库中。

## 数据库迁移
### 相同版本的 MySQL 数据库之间的迁移
在主版本号相同的 MySQL 数据库之间进行数据库移动，迁移过程其实就是将源数据库备份移动到目标数据库中。

```sql
> mysqldump -h www.a.com -u root -p password dbname | mysql -h www.b.com -u root -p password;
```

通过管道符号 "|" ，传给 mysql 命令导入主机 www.b.com 的数据库中，dbname 为要迁移的数据库名称，如果要迁移全部的数据库，可使用参数 --all-databases。

### 不同版本的 MySQL 数据库之间的迁移
旧版本与新版本的 MySQL 可能使用不同的默认字符集。

新版本对旧版本有一定的兼容性，从旧版本的 MySQL 向新版本的 MySQL 迁移时，对于 MyISAM 引擎的表，可以直接复制数据库文件，也可以使用 mysqlhotcopy 和 mysqldump 工具。对于 InnoDB 引擎的表，一般只能使用 mysqldump 将数据导出，然后使用 mysql 命令导入目标服务器上。

## 表的导出和导入
### SELECT ... INTO OUTFILE 'filename'
在 MySQL 数据库中导出数据时，可以用 SELECT ... INTO OUTFILE 'filename' 形式的 SELECT 语句把被选择的行写入一个文件中，filename 不能是一个已经存在的文件。

```sql
> SELECT columnlist FROM table WHERE condition INTO OUTFILE 'filename' [OPTION]

    --OPTIONS 选项
    FIELDS TERMINATED BY 'value'
    FIELDS [OPTIONALLY] ENCLOSED BY 'value'
    FIELDS ESCAPED BY 'value'
    LINES STARTING BY 'value'
    LINES TERMINATED BY 'value'
```
- `FIELDS TERMINATED BY 'value'`：设置字段之间的分隔字符，可以为单个或多个字符
- `FIELDS [OPTIONALLY] ENCLOSED BY 'value'`：设置字段的包围字符，只能为单个字符
- `FIELDS ESCAPED BY 'value'`：设置如何写入或读取特殊字符，只能为单个字符，及设置转义字符，默认为 '\'
- `LINES STARTING BY 'value'`：设置每行数据开头的字符，可以为单个或多个字符，默认值为 '\n'
- `LINES TERMINATED BY 'value'`：设置每行数据皆为的字符，可以为单个或多个字符，默认为 '\n'

```sql
> SELECT * FROM test.person INTO OUTFILE '/User/lxxxx/test.sql';

1   Green   21  lawyer
```

```sql
> SELECT * FROM test.person INTO OUTFILE '/User/lxxxx/test.sql'
    FIELDS
        TERMINATED BY ','
        ENCLOSED BY '\"'
        ESCAPED BY '\''
    LINES
        TERMINATED BY '\r\n';

"1","Green","21","lawyer"
```

### 使用 mysqldump 命令导出文本文件
基本语法格式
```sql
> mysqldump -T path -u root -p dbname [tables] [OPTIONS]

    --OPTIONS 选项
    --fields-terminated-by=value
    --fields-enclosed-by=value
    --fields-optionally-enclosed-by=value
    --fields-escaped-by=value
    --lines-terminated-by=value
```

- 只有指定了 -T 参数才能导出纯文本文件
- path 表示导出数据目录
- tables 表示为指定要导出的表名称，如果不指定，将导出数据库 dbname 中所有的表

与 SELECT ... INTO OUTFILE 语句中的 OPTIONS 的各个参数设置不同，这里 OPTIONS 的各选项等号后面的 value 值不要用引号括起来。

```sql
> mysqldump -T /User/lxxx person -u root -p;
```

### 用 mysql 命令导出文本文件

```sql
> mysql -uroot -p --execute="SELECT * FROM person;" persondb > /Users/lxxxx/test.txt;

> mysql -uroot -p --xml --execute="SELECT * FROM person;" persondb > /Users/lxxxx/test.txt; # 导出 xml 格式

> mysql -uroot -p --html --execute="SELECT * FROM person;" persondb > /Users/lxxxx/test.txt; # 导出 html 格式

> mysql -uroot -p --vertical --execute="SELECT * FROM person;" persondb > /Users/lxxxx/test.txt; # 竖向排列
```
/Applications/MAMP/Library/bin/mysql -uroot -proot --vertical --execute="SELECT * FROM testjoin1 FIELDS TERMINATED BY '\,';" runoob  > /Users/lxfriday/test.sql

### 使用 LOAD DATA INFILE 方式导入文本文件

```sql
> LOAD DATA INFILE 'filename.txt' INTO TABLE tablename [OPTIONS] [IGNORE number LINES]
    --OPTIONS 选项
    FIELDS TERMINATED BY 'value'
    FIELDS [OPTIONALLY] ENCLOSED BY 'value'
    FIELDS ESCAPED BY 'value'
    LINES STARTING BY 'value'
    LINES TERMINATED BY 'value';
```
