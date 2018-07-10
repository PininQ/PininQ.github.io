---
layout: post
title: Flask学习笔记6--Flask中使用数据库
categories: Flask
description: SQLAlchemy的基本使用
keywords: Python, Flask, SQLAlchemy
---


### Flask-SQLAlchemy扩展

- SQLAlchemy实际上是对数据库的抽象，让开发者不用直接和SQL语句打交道，而是通过Python对象来操作数据库，在舍弃一些性能的开销的同时，换来的是开发效率的较大提升。
- SQLAlchemy是一个关系型数据库框架，它提供了高层的ORM和底层的原生数据库的操作。flask-sqlalchemy是一个简化了SQLAlchemy操作的flask扩展。

### 安装flask-sqlalchemy

```python
pip install flask-sqlalchemy
```

如果连接的是mysql数据库，需要安装mysqldb
```python
pip install flask-mysqldb
```
### 安装flask-mysqldb过程中遇到问题解决方案
[windows下运行pip install Flask-MySQLdb出错---解决方案](https://blog.csdn.net/anyuexiazhuomicang/article/details/77611296)

需要安装两个文件：
- 在[https://www.lfd.uci.edu/~gohlke/pythonlibs/#mysql-python](https://www.lfd.uci.edu/~gohlke/pythonlibs/#mysql-python)下载对应版本的MySQL_python并到改文件的目录下使用`pip install MySQL_python-1.2.5-cp27-none-win_amd64.whl`进行安装
- 在[https://www.lfd.uci.edu/~gohlke/pythonlibs/#mysqlclient](https://www.lfd.uci.edu/~gohlke/pythonlibs/#mysqlclient)下载对应版本的mysqlclient并到改文件的目录下使用`pip install mysqlclient-1.3.12-cp27-cp27m-win_amd64.whl`进行安装


### 使用Flask-SQLAlchemy管理数据库

在Flask-SQLAlchemy中，数据库使用URL指定，而程序使用的数据库必须保存到Flask配置对象SQLALCHEMY_DATABASE_URI键中。

#### Flask的数据库设置

```python
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql://root:password@127.0.0.1:3306/test'
```

其他设置：
```python
# 动态追踪修改设置，如未设置只会提示警告，不建议开启
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
# 查询时会显示原始的SQL语句
app.config['SQLALCHEMY_ECHO'] = True
```

<table>
  <tr>
    <th width=249, bgcolor=yellow >名字</th>
    <th bgcolor=yellow>备注</th>
  </tr>
  <tr>
    <td bgcolor=#eeeeee> SQLALCHEMY_DATABASE_URI </td>
    <td> 用于连接的数据库URI。例如：sqlite:////tmp/test.dbmysql://username:password@server/db</td>

  </tr>
  <tr>
    <td bgcolor=#00FF00>SQLALCHEMY_BINDS </td>
    <td> 一个映射binds到链接URI的字典。更多binds的信息见用Binds操作多个数据库 </td>

  <tr>
    <td bgcolor=rgb(0,10,0)>SQLALCHEMY_ECHO </td>
    <td> 如果设置为True，SQLAlchemy会记录所有发给stderr的语句，这对调试有用。（打印sql语句）</td>

  </tr>
    <tr>
        <td>SQLALCHEMY_RECORD_QUERIES</td>
        <td>可以用于显示地禁用或启用查询记录。查询记录在调试或测试模式自动启用。更多信息见get_debug_queries()。</td>
    </tr>
    <tr>
        <td>SQLALCHEMY_NATIVE_UNICODE</td>
        <td>可以用于显示禁用原生Unicode支持。当使用不合适的指定无编码的数据库默认值时，这对于一些数据库适配器时必须的（比如Ubuntu上某些 版本的PostgreSQL）。</td>
    </tr>
    <tr>
        <td>SQLALCHEMY_POOL_SIZE</td>
        <td>数据库连接池大小。默认时引擎默认值（通常是5）</td>
    </tr>
    <tr>
        <td>SQLALCHEMY_POOL_TIMEOUT</td>
        <td>设定连接池的连接超时时间。默认是10.</td>
    </tr>
    <tr>
        <td>SQLALCHEMY_POOL_RECYCLE</td>
        <td>多少秒后自动回收连接。这对MySQL是必要的，它默认移除闲置多于8小时的连接。注意如果使用了MySQL，Flask-SQLAlchemy自动设定这个值为2小时。</td>
    </tr>
</table>

### 常见的SQLAlchemy字段类型


类型名 | python中类型  | 说明
---|--- |---
Integer | int | 普通整数，一般是32位
SmallInteger | int | 取值范围小的整数，一般是16位
BigInteger | int或long | 不限制精度的整数
Float | float | 浮点数
Numeric | decimal.Decimal|普通整数，一般是32位
String|str|变长的字符串
Text|str|变长字符串，对较长或不限长度的字符串做了优化
Unicode|unicode|变长Unicode字符串
UnicodeText|unicode|变长Unicode字符串，对较长或不限长度的字符串做了优化
Boolean | bool|布尔值
Date|datetime|时间
Time|datetime.datetime|日期和时间
LargeBinary|str|二进制文件

### 常用的SQLAlchemy列选项


选项名 | 说明
---|---
primary_key | 如果为True，代表表的主键
unique | 如果为True，代表这列不允许出现重复的值
index|如果为True，为这列创建索引，提高查询的效率
nullable|如果为True，允许有空值，如果为False，不允许有空值
default|为这列定义默认值


### 常用的SQLAlchemy关系选项


选项名 | 说明
---|---
backref | 在关系的另一模型中添加反向引用
primary join | 明确指定两个模型之间使用的联结条件
uselist|如果为False，不适用列表，而使用标量值
order_by|指定关系中记录的排序方式
secondary|指定多对多中记录的排序方式
secondary join|在SQLAlchemy中无法自行决定时，指定多对多关系中的二级联结条件

