---
layout: post
title: MySQL 之修改远程访问权限「转」
categories: MySQL
description: MySQL 之修改远程访问权限
keywords: MySQL, MySQL 权限
---

### 1. 登陆 MySQL 数据库

登陆数据库：`mysql -u root -p`

查看 user 表


```
mysql> use mysql;
Database changed
mysql> select host,user,password from user;
+--------------+------+-------------------------------------------+
| host         | user | password                                  |
+--------------+------+-------------------------------------------+
| localhost    | root | *A731AEBFB621E354CD41BAF207D884A609E81F5E |
| 192.168.1.1  | root | *A731AEBFB621E354CD41BAF207D884A609E81F5E |
+--------------+------+-------------------------------------------+
2 rows in set (0.00 sec)
```

可以看到在 user 表中已创建的 root 用户。host 字段表示登录的主机，其值可以用 IP，也可用主机名，有时想用本地 IP 登录，那么可以将以上的 Host 值改为自己的 IP 即可。

### 2. 实现远程连接 (授权法)

将 host 字段的值改为 % 就表示在任何客户端机器上能以 root 用户登录到 MySQL 服务器，建议在开发时设为 %。

```
update user set host = ’%’ where user = ’root’;
```


将权限改为 ALL PRIVILEGES

```
mysql> use mysql;
Database changed
mysql> grant all privileges  on *.* to root@'%' identified by "password";
Query OK, 0 rows affected (0.00 sec)

mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)

mysql> select host,user,password from user;
+--------------+------+-------------------------------------------+
| host         | user | password                                  |
+--------------+------+-------------------------------------------+
| localhost    | root | *A731AEBFB621E354CD41BAF207D884A609E81F5E |
| 192.168.1.1  | root | *A731AEBFB621E354CD41BAF207D884A609E81F5E |
| %            | root | *A731AEBFB621E354CD41BAF207D884A609E81F5E |
+--------------+------+-------------------------------------------+
3 rows in set (0.00 sec)
```
这样机器就可以以用户名 root 密码 root 远程访问该机器上的 MySQL。

### 3. 实现远程连接（改表法）


```
use mysql;

update user set host = '%' where user = 'root';
```

这样在远端就可以通过 root 用户访问 MySQL。

### 原文地址
[开启 MySQL 远程访问权限 允许远程连接](https://www.cnblogs.com/weifeng1463/p/7941625.html)