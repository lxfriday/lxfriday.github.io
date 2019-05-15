---
title: mysql账户、权限、访问控制管理
comments: true
top: false
date: 2019-02-25 15:05:39
categories: 数据库
tags: mysql
img: https://qiniu1.lxfriday.xyz/image/20190128-mysql.jpeg
---

## MySQL 权限表

MySQL 通过权限表来控制用户对数据库的访问，权限表存放在 MySQL 数据库中， mysql_install_db 脚本初始化。存储账户权限的信息主要有 user、db、host、tables_priv、columns_priv、procs_priv。

user 表是 MySQL 中最重要的一个权限表，记录允许连接到服务器的账号信息，里面的权限是全局级别的。若一个用户在 user 表中被授予了 DELETE 权限，则该用户可以删除 MySQL 服务器上所有数据库中的任何记录。

user 表主要由四类列构成：
- 用户列：Host、User、authentication_string
- 权限列：Select_priv、Update_pirv、Create_priv...
- 安全列：ssl_type、ssl_cipher、x509_issuer...
- 资源控制列：max_questions、max_updates、max_connections...

```sql
> CREATE TABLE `user` (
  `Host` char(60) COLLATE utf8_bin NOT NULL DEFAULT '',
  `User` char(32) COLLATE utf8_bin NOT NULL DEFAULT '',
  `Select_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Insert_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Update_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Delete_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Create_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Drop_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Reload_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Shutdown_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Process_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `File_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Grant_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `References_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Index_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Alter_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Show_db_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Super_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Create_tmp_table_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Lock_tables_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Execute_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Repl_slave_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Repl_client_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Create_view_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Show_view_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Create_routine_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Alter_routine_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Create_user_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Event_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Trigger_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `Create_tablespace_priv` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `ssl_type` enum('','ANY','X509','SPECIFIED') CHARACTER SET utf8 NOT NULL DEFAULT '',
  `ssl_cipher` blob NOT NULL,
  `x509_issuer` blob NOT NULL,
  `x509_subject` blob NOT NULL,
  `max_questions` int(11) unsigned NOT NULL DEFAULT '0',
  `max_updates` int(11) unsigned NOT NULL DEFAULT '0',
  `max_connections` int(11) unsigned NOT NULL DEFAULT '0',
  `max_user_connections` int(11) unsigned NOT NULL DEFAULT '0',
  `plugin` char(64) COLLATE utf8_bin NOT NULL DEFAULT 'mysql_native_password',
  `authentication_string` text COLLATE utf8_bin,
  `password_expired` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  `password_last_changed` timestamp NULL DEFAULT NULL,
  `password_lifetime` smallint(5) unsigned DEFAULT NULL,
  `account_locked` enum('N','Y') CHARACTER SET utf8 NOT NULL DEFAULT 'N',
  PRIMARY KEY (`Host`,`User`)
) ENGINE=MyISAM DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='Users and global privileges'
```

## MySQL 账户管理
### 登录和退出 MySQL 服务器
```sql
mysql -h localhost -u root -p test
```

- -h 主机名，指定主机名或 ip，默认为 localhost
- -u 用户名
- -p 密码
- -P 端口，默认 3306
- 无参数数据库名。可以在命令的最后指定数据库名
- -esql。如果制定了该参数，将在登录后执行 -e 后面的命令或 sql 语句并退出

```sql
> mysql -h localhost -u root -p mysql -e "DESC person;"
```

### 新建普通用户
有两种方法可以创建新用户：一种是使用 CREATE USER 或 GRANT 语句；零一种是直接操作 MySQL 授权表。最好使用 GRANT 语句。

#### 使用 CREATE USER
```sql
> CREATE USER user_specification [, user_specification]...
    user_specification:
    user@host
    [
        IDENTIFIED BY [PASSWORD] 'password'
        [ IDENTIFIED WITH auth_plugin [AS 'auth_string']]
    ]
```

user 表示创建的用户名称；host 表示允许登录的用户主机名称；IDENTIFIED BY 表示用来设置用户的密码；[PASSWORD] 表示使用哈希值设置密码，该参数可选；password 表示用户登录时使用的普通明文密码；IDENTIFIED WITH 语句为用户指定一个身份验证插件；auth_plugin 是插件的名称，插件名称可以是一个带单引号的字符串，或者带双引号的字符串；auth_string 是可选的字符串参数，该参数传递给身份验证插件，由该插件解释该参数的意义。

使用 CREATE USER 语句的用户，必须有全局的 CREATE USER 权限或 MySQL 数据库的 INSERT 权限。CREATE USER 新创建的账户没有任何权限。如果新添加的账户已经存在，CREATE USER 语句会返回一个错误。

```sql
> CREATE USER 'jeffrey'@'localhost' IDENTIFIED BY 'mypass'
```

如果只指定用户名部分 jeffrey，则主机名部分默认为 % （即对所有的主机开放权限）。

为了避免指定明文密码，如果知道密码的哈希值，可以通过 PASSWORD 关键字使用密码的哈希值设置密码。

```sql
> SELECT PASSWORD('mypass');

> CREATE USER 'jeffrey'@'localhost' IDENTIFIED BY PASSWORD '*6C8989366EAF75BB670AD8EA7A7FC1176A95CEF4'
```

#### 使用 GRANT 语句创建新用户
CREATE USER 语句创建的新用户没有任何权限，还需要用 GRANT 语句赋予用户权限。GRANT 语句创建用户可以直接授权。GRANT 语句是添加新用户并授权他们访问数据库的首选方法。

```sql
> GRANT ALL PRIVILEGES ON db.table TO user@localhost [IDENTIFIED BY 'password'] [,user [IDENTIFIED BY 'password']] [WITH GRANT OPTION];
```

```sql
> GRANT SELECT,UPDATE ON *.* TO 'testUser'@'localhost' IDENTIFIED By 'testpwd';
```

#### 直接操作 MySQL 用户表
```sql
> INSERT INTO mysql.user (HOST,User,Password,[privilegelist]) VALUES ('host', 'username', PASSWORD('password'), privilegevaluelist);
```

### 删除普通用户
```sql
> DROP USER 'user'@'host'; # username + hostname 组合对应一个指定的用户
```

```sql
> DELETE FROM mysql.user WHERE host='hostname' AND user='username';
```

### root 用户修改自己的密码
#### 使用 mysqladmin 命令在命令行指定新密码
mysqladmin 命令的基本语法格式如下
```sql
> mysqladmin -u username -h localhost -p password 'newpwd';
```

更改 root 用户的密码
```sql
> mysqladmin -u root -p password "rootpwd"
```

#### 修改 mysql 数据库的 user 表
直接更改数据库中的 root 对应的密码字段的值
```sql
> UPDATE mysql.user SET Password=PASSWORD('rootpwd') WHERE User='root' and Host='localhost';
```

#### 使用 SET 语句修改 root 用户的密码

```sql
> SET PASSWORD=PASSWORD('rootpwd');
```

### root 用户修改普通用户密码
#### SET 语句

```sql
> SET PASSWORD FOR 'user'@'host' = PASSWORD('userpwd');
```
 
 #### UPDATE 语句修改
 
 ```sql
 > UPDATE mysql.user SET Password=PASSWORD('pwd') WHERE User='username' AND Host='localhost';
 ```
 
 #### GRANT 语句修改普通用户密码
 GRANT ... ON ... TO 'user'@'localhost'
 
 ```sql
 > GRANT USAGE ON *.* TO 'someuser'@'%' IDENTIFIED BY 'somepwd';
 ```

### 普通用户修改密码

```sql
> SET PASSWORD=PASSWORD('newpwd');
```

### root 用户密码丢失的解决办法
#### 使用 skip-grant-tables 选项启动 MySQL 服务

以 skip-grant-tables 选项启动的时候，MySQL 服务器将不加载权限判断，任何用户都能访问数据库。

```sql
> # 在 Linux 系统中，使用 /etc/init.d/mysql 来启动 MySQL 服务
> /etc/init.d/mysql start-mysqld --skip-grant-tables
```

#### 使用 root 用户登录，重新设置密码

```sql
> mysqld --skip-grant-tables
```

命令运行之后，用户无法输入指令，此时如果在任务管理器中看到名为 mysqld 的进程，则表示可以使用 root 用户登录 MySQL 了

```sql
> mysql -u root
> UPDATE mysql.user SET Password=PASSWORD('newpwd') WHERE User='root' AND Host='localhost'
> # 再加载权限表，密码修改完成之后，必须使用 FlUSH PRIVILEGES 语句加载权限表。加载权限表后，新的密码才会生效，同时 MySQL 服务器开始权限验证
> FLUSH PRIVILEGES;
> # 修改密码之后，将输入 mysqld --skip-grant-tables 命令的命令行窗口关闭，接下来就可以使用新设置的密码登录 MySQL 了。
```

### MySQL 权限管理
#### 授权
全局层级，全局权限适用于一个给定服务器中的所有数据库，这些权限存储在 mysql.user 表中。 `GRANT ALL ON *.*` 和 `REVOKEA LL ON db_name.* `

```sql
> GRANT priv_type [(columns)] [,priv_type[(columns)]] ...
    ON [object_type] table1,tabl2,...,tablen
    TO user [IDENTIFIED BY [PASSWORD] 'password']
    [, user [IDENTIFIED BY [PASSWORD] 'password']] ...
    [WITH GRANT OPTION]
```

使用 GRANT 语句创建一个新用户 grantUser，密码为 grantpwd ，用户 grantUser 对所有的数据有查询、插入权限，并授予 GRANT 权限。

```sql
> GRANT SELECT,INSERT ON *.* TO 'grantUseer'@'localhost' IDENTIFIED BY 'grantpwd' WITH GRANT OPTION;
```

#### 收回权限 REVOKE
REVOKE 语句必须和 FROM 一起使用，FROM 指明需要收回权限的账户
```sql
> REVOKE ALL PRIVILEGES, GRANT OPTION FROM 'user'@'host' [,'user'@'host' ...];
```

```sql
> REVOKE priv_type [(columns)] [,priv_type[(columns)]] ...
    ON table1,table2,...,tablen
    FROM 'user'@'host' [, 'user'@'host'];
``` 

#### 查看权限
```sql
> SHOW GRANTS FOR 'user'@'host';
```

直接从 user 表中查询数据
```sql
> SELECT Select_priv,Insert_priv FROM user WHERE user='grantUseer' AND host='localhost';
```











