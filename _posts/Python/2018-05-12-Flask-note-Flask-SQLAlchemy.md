---
layout: post
title: Flask学习笔记6--Flask中使用数据库
categories: Flask
description: SQLAlchemy的基本使用
keywords: Python, Flask, SQLAlchemy
---



### Flask-SQLAlchemy 扩展

- SQLAlchemy 实际上是对数据库的抽象，让开发者不用直接和 SQL 语句打交道，而是通过 Python 对象来操作数据库，在舍弃一些性能的开销的同时，换来的是开发效率的较大提升。
- SQLAlchemy 是一个关系型数据库框架，它提供了高层的 ORM 和底层的原生数据库的操作。flask-sqlalchemy 是一个简化了 SQLAlchemy 操作的 flask 扩展。

### 安装 flask-sqlalchemy

```python
pip install flask-sqlalchemy
```

如果连接的是 mysql 数据库，需要安装 mysqldb
```python
pip install flask-mysqldb
```
### 安装 flask-mysqldb 过程中遇到问题解决方案
[windows 下运行 pip install Flask-MySQLdb 出错 --- 解决方案](https://blog.csdn.net/anyuexiazhuomicang/article/details/77611296)

需要安装两个文件：
- 在 [https://www.lfd.uci.edu/~gohlke/pythonlibs/#mysql-python](https://www.lfd.uci.edu/~gohlke/pythonlibs/#mysql-python) 下载对应版本的 MySQL_python 并到改文件的目录下使用 `pip install MySQL_python-1.2.5-cp27-none-win_amd64.whl` 进行安装
- 在 [https://www.lfd.uci.edu/~gohlke/pythonlibs/#mysqlclient](https://www.lfd.uci.edu/~gohlke/pythonlibs/#mysqlclient) 下载对应版本的 mysqlclient 并到改文件的目录下使用 `pip install mysqlclient-1.3.12-cp27-cp27m-win_amd64.whl` 进行安装


### 使用 Flask-SQLAlchemy 管理数据库

在 Flask-SQLAlchemy 中，数据库使用 URL 指定，而程序使用的数据库必须保存到 Flask 配置对象 `SQLALCHEMY_DATABASE_URI` 键中。

**Flask 的数据库设置**

```python
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql://root:password@127.0.0.1:3306/test'
```

**其他设置**
```python
# 动态追踪修改设置，如未设置只会提示警告，不建议开启
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
# 查询时会显示原始的 SQL 语句
app.config['SQLALCHEMY_ECHO'] = True
```


名字 | 备注
---|---
SQLALCHEMY_DATABASE_URI | 用于连接的数据库 URI。例如：sqlite:////tmp/test.dbmysql://username:password@server/db
 SQLALCHEMY_BINDS    | 一个映射 binds 到链接 URI 的字典。更多 binds 的信息见用 Binds 操作多个数据库
  SQLALCHEMY_ECHO  | 如果设置为 True，SQLAlchemy 会记录所有发给 stderr 的语句，这对调试有用。（打印 sql 语句）
  SQLALCHEMY_RECORD_QUERIES  | 可以用于显示地禁用或启用查询记录。查询记录在调试或测试模式自动启用。更多信息见 get_debug_queries()。
  SQLALCHEMY_NATIVE_UNICODE  | 可以用于显示禁用原生 Unicode 支持。当使用不合适的指定无编码的数据库默认值时，这对于一些数据库适配器时必须的（比如 Ubuntu 上某些 版本的 PostgreSQL）。
   SQLALCHEMY_POOL_SIZE | 数据库连接池大小。默认时引擎默认值（通常是 5）
 SQLALCHEMY_POOL_TIMEOUT   | 设定连接池的连接超时时间。默认是 10。
   SQLALCHEMY_POOL_RECYCLE | 多少秒后自动回收连接。这对 MySQL 是必要的，它默认移除闲置多于 8 小时的连接。注意如果使用了 MySQL，Flask-SQLAlchemy 自动设定这个值为 2 小时。




### 常见的 SQLAlchemy 字段类型


类型名 | python 中类型  | 说明
---|--- |---
Integer | int | 普通整数，一般是 32 位
SmallInteger | int | 取值范围小的整数，一般是 16 位
BigInteger | int 或 long | 不限制精度的整数
Float | float | 浮点数
Numeric | decimal.Decimal | 普通整数，一般是 32 位
String|str | 变长的字符串
Text|str | 变长字符串，对较长或不限长度的字符串做了优化
Unicode|unicode | 变长 Unicode 字符串
UnicodeText|unicode | 变长 Unicode 字符串，对较长或不限长度的字符串做了优化
Boolean | bool | 布尔值
Date|datetime | 时间
Time|datetime.datetime | 日期和时间
LargeBinary|str | 二进制文件

### 常用的 SQLAlchemy 列选项


选项名 | 说明
---|---
primary_key | 如果为 True，代表表的主键
unique | 如果为 True，代表这列不允许出现重复的值
index | 如果为 True，为这列创建索引，提高查询的效率
nullable | 如果为 True，允许有空值，如果为 False，不允许有空值
default | 为这列定义默认值


### 常用的 SQLAlchemy 关系选项


选项名 | 说明
---|---
backref | 在关系的另一模型中添加反向引用
primary join | 明确指定两个模型之间使用的联结条件
uselist | 如果为 False，不适用列表，而使用标量值
order_by | 指定关系中记录的排序方式
secondary | 指定多对多中记录的排序方式
secondary join | 在 SQLAlchemy 中无法自行决定时，指定多对多关系中的二级联结条件

