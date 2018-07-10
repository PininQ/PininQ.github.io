---
layout: post
title: Flask学习笔记7--数据库基本操作
categories: Flask
description: 数据库基本操作
keywords: Python, Flask, SQLAlchemy
---


# 数据库基本操作

## 一. 增删改操作

### 1. 基本概念
- 在Flask-SQLAlchemy中，插入、修改、删除操作，均由数据库会话管理。
    - 会话用db.sessioni表示。在准备把数据写入数据库前，要先将数据添加到会话中然后调用commit()方法提交会话。
- 在Flask-SQLAlchemy中，查询操作是返回表中所有数据。
    - 最基本的查询时返回表中所有数据，可以通过过滤器进行更精确的数据库查询。


```python
db.session.add(role)       # 添加到数据库的session中
db.session.add_all([user1, user2]) # 添加多个信息到session中
db.session.commit()        # 提交数据库的修改（包括增/删/改）
db.session.rollback()      # 数据库的回滚操作
db.session.delete(user)    # 删除数据库（需要跟上commit）
```


### 2. 示例

#### 2.1 在视图函数中定义模型类

- **Flask_SQLAlchemy_demo.py如下：**

```python
# -*- coding:utf-8 -*-
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)

# 配置数据库跟踪地址
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql://root:qiu66666@127.0.0.1:3306/flask_sql_demo'
# 跟踪数据库的修改 --> 不建议开启 未来的版本中会移除
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

db = SQLAlchemy(app)

'''
两张表
角色（管理元 / 普通用户）
用户（角色ID）
'''


# 数据库的模型，需要继承db.Model
class Role(db.Model):
    # 定义表名
    __tablename__ = 'roles'

    # 定义字段
    # db.Column表示一个字段
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(16), unique=True)

    # 在一的一方，写关联
    # users = db.relationship('User'): 表示和User模型发生了关联，增加了一个users属性
    users = db.relationship('User', backref='role')

    # repr()方法显示一个可读字符串
    def __repr__(self):
        return '<Role: %s %s>' % (self.name, self.id)


class User(db.Model):
    __tablename__ = 'users'
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(16), unique=True)
    email = db.Column(db.String(32), unique=True)
    password = db.Column(db.String(32))
    # db.ForeignKey('roles.id')表示时外键，表名.id
    role_id = db.Column(db.Integer, db.ForeignKey('roles.id'))
    # User希望有role属性，但是这个属性的定义，需要在另一个模型中定义

    def __repr__(self):
        return '<User: %s %s %s %s>' % (self.name, self.id, self.email, self.password)


@app.route('/')
def hello_world():
    return 'Hello World!'


if __name__ == '__main__':
    # 删除表
    db.drop_all()

    # 创建表
    db.create_all()

    app.run(debug=True)

```
-  **在终端进行增删改操作：**

```
>>> from Flask_SQLAlchemy_demo import *

添加一条Role数据
>>> role = Role(name='admin')
>>> db.session.add(role)
>>> db.session.commit()

添加一条User数据，数据有误可以使用回滚，将add的对象从session移除
>>> user = User(name='qinbin')
>>> db.session.add(user)
>>> db.sessioon.rollback()
>>> user.role_id = 1
>>> db.session.add(user)
>>> db.session.commit()

修改数据
>>> user.name = 'qiuling'
>>> db.session.commit()

删除数据
>>> db.session.delete(user)
>>> db.session.commit()
```

### 3. 模型之间的关联

#### 3.1 一对多

```
class Role(db.Model):
    ...
    # 关键代码
    user = db.relationship('User', backref='role')
    ...


class User(db.Model):
    ...
    role_id = db.Colnum(db.Integer, db.ForeignKey('roles.id'))

```

- 其中relationship描述了Role和User的关系。在此文中，第一个参数为对应参照的类"User"
- 第二个参数backref为类User申明新属性的方法

```python

1.添加一个角色和两个用户

In [1]: from Flask_SQLAlchemy_demo import *

In [2]: role = Role(name='admin')

In [3]: db.session.add(role)

In [4]: db.session.commit()

In [5]: user1 = User(name='zs', role_id=role.id)

In [6]: user2 = User(name='ls', role_id=role.id)

In [7]: db.session.add_all([user1, user2])

In [8]: db.session.commit()

2.实现关系引用查询

In [9]: role.users
Out[9]: [<User: zs 1 None None>, <User: ls 2 None None>]

In [10]: user1.role
Out[10]: <Role: admin 1>

In [11]: user2.role
Out[11]: <Role: admin 1>

In [12]: user2.role.name
Out[12]: u'admin'

```
## 二. 查询操作

### 1. 基本概念

#### 1.1常用的SQLAlchemy查询过滤器

过滤器 | 说明
---|---
filter() | 把过滤器添加到原查询上，返回一个新查询
filter_by() | 把等值过滤器添加到原查询上，返回一个新查询
limit | 使用指定的值限定原查询返回的结果
offset() | 偏移原查询返回的结果，返回一个新查询
order_by() | 根据指定条件对原查询结果进行排序，返回一个新查询
group_by() | 根据指定条件对原查询结果进行分组，返回一个新查询


#### 1.2 常用的SQLAlchemy查询执行器


方法 | 说明
---|---
all() | 以列表形式返回查询的所有结果
first() | 返回查询的第一个结果，如果未查到，返回None
first_or_404() | 返回查询的第一个结果，如果未查到，返回404
get() | 返回指定主键对应的行，如不存在，返回NOne
get_or_404() | 返回指定主键对应的行，如不存在，返回404
count() | 返回查询结果的数量
paginate() | 返回一个Paginate对象，它包含指定范围内的结果

### 2.示例

#### 2.1 插入角色数据

```python
ro1 = Role(name='admin')
db.session.add(rol)
db.session.commit()
# 再次插入一条数据
ro2 = Role(name='user')
db.session.add(ro2)
db.session.commit()
```

#### 2.2 一次插入多条数据

```python
us1 = User(name='wang', email='wang@163.com', password='123456', role_id=ro1.id)
us2 = User(name='zhang', email='zhang@189.com', password='20153', role_id=ro2.id)
us3 = User(name='chen', email='chen@126.com', password='343224', role_id=ro2.id)
us4 = User(name='su', email='su@163.com', password='434323', role_id=ro1.id)
us5 = User(name='zhou', email='zhou@qinbin.com', password='666666', role_id=ro2.id)
us6 = User(name='wu', email='wu@gmail.com', password='473735', role_id=ro2.id)
us7 = User(name='qian', email='qian@qq.com', password='544454', role_id=ro1.id)
us8 = User(name='liu', email='liu@163.com', password='765756', role_id=ro1.id)
us9 = User(name='sun', email='sun@163.com', password='090909', role_id=ro1.id)
us10 = User(name='qin', email='qin@qq.com', password='888888', role_id=ro2.id)
us11 = User(name='lu', email='lu@163.com', password='972668', role_id=ro2.id)
us12 = User(name='tang', email='tang@qq.com', password='096536', role_id=ro2.id)
db.session.add_all([us1, us2, us3, us4, us5, us6, us7, us8, us9, us10, us11, us12])
db.session.commit()
```

#### 2.3 查询练习如下

```
1. 查询所有用户数据
# all()返回查询到的所有对象
User.query.all()


2. 查询有多少个用户
User.query.count()


3.查询第一个用户
User.query.first()


4.查询id为4的用户【3种方式】
# filter_by直接用属性名，比较用=，filter用类名.属性名，比较用==
# filter_by用于查询简单的列名，不支持比较运算符
# filter比filter_by的功能更加强大，支持比较运算符，支持or_、in_等语法
User.query.get(4)
User.query.filter_by(id=4).first()  # 属性 =
User.query.filter(User.id==4).first()    # 对象名.属性 ==
```

---

**代码如下：**
```
# -*- coding:utf-8 -*-
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)

# 配置数据库跟踪地址
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql://root:qiu66666@127.0.0.1:3306/flask_sql_demo'
# 跟踪数据库的修改 --> 不建议开启 未来的版本中会移除
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

db = SQLAlchemy(app)

'''
两张表
角色（管理元 / 普通用户）
用户（角色ID）
'''


# 数据库的模型，需要继承db.Model
class Role(db.Model):
    # 定义表名
    __tablename__ = 'roles'

    # 定义字段
    # db.Column表示一个字段
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(16), unique=True)

    # 在一的一方，写关联
    # users = db.relationship('User'): 表示和User模型发生了关联，增加了一个users属性
    users = db.relationship('User', backref='role')

    # repr()方法显示一个可读字符串
    def __repr__(self):
        return '<Role: %s %s>' % (self.name, self.id)


class User(db.Model):
    __tablename__ = 'users'
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(16), unique=True)
    email = db.Column(db.String(32), unique=True)
    password = db.Column(db.String(32))
    # db.ForeignKey('roles.id')表示时外键，表名.id
    role_id = db.Column(db.Integer, db.ForeignKey('roles.id'))

    # User希望有role属性，但是这个属性的定义，需要在另一个模型中定义

    def __repr__(self):
        return '<User: %s %s %s %s>' % (self.name, self.id, self.email, self.password)


@app.route('/')
def hello_world():
    return 'Hello World!'


if __name__ == '__main__':
    # 删除表
    db.drop_all()

    # 创建表
    db.create_all()

    ro1 = Role(name='admin')
    db.session.add(ro1)
    db.session.commit()
    # 再次插入一条数据
    ro2 = Role(name='user')
    db.session.add(ro2)
    db.session.commit()

    us1 = User(name='wang', email='wang@163.com', password='123456', role_id=ro1.id)
    us2 = User(name='zhang', email='zhang@189.com', password='20153', role_id=ro2.id)
    us3 = User(name='chen', email='chen@126.com', password='343224', role_id=ro2.id)
    us4 = User(name='su', email='su@163.com', password='434323', role_id=ro1.id)
    us5 = User(name='zhou', email='zhou@qinbin.com', password='666666', role_id=ro2.id)
    us6 = User(name='wu', email='wu@gmail.com', password='473735', role_id=ro2.id)
    us7 = User(name='qian', email='qian@qq.com', password='544454', role_id=ro1.id)
    us8 = User(name='liu', email='liu@163.com', password='765756', role_id=ro1.id)
    us9 = User(name='sun', email='sun@163.com', password='090909', role_id=ro1.id)
    us10 = User(name='qin', email='qin@qq.com', password='888888', role_id=ro2.id)
    us11 = User(name='lu', email='lu@163.com', password='972668', role_id=ro2.id)
    us12 = User(name='tang', email='tang@qq.com', password='096536', role_id=ro2.id)
    db.session.add_all([us1, us2, us3, us4, us5, us6, us7, us8, us9, us10, us11, us12])
    db.session.commit()

    app.run(debug=True)

```

**操作过程如下：**

```python
In [1]: from Flask_SQLAlchemy_demo import *

In [2]: User.query.all()
Out[2]:
[<User: wang 1 wang@163.com 123456>,
 <User: zhang 2 zhang@189.com 20153>,
 <User: chen 3 chen@126.com 343224>,
 <User: su 4 su@163.com 434323>,
 <User: zhou 5 zhou@qinbin.com 666666>,
 <User: wu 6 wu@gmail.com 473735>,
 <User: qian 7 qian@qq.com 544454>,
 <User: liu 8 liu@163.com 765756>,
 <User: sun 9 sun@163.com 090909>,
 <User: qin 10 qin@qq.com 888888>,
 <User: lu 11 lu@163.com 972668>,
 <User: tang 12 tang@qq.com 096536>]

In [3]: User.query.count()
Out[3]: 12L

In [4]: User.query.first()
Out[4]: <User: wang 1 wang@163.com 123456>

In [5]: User.query.get(4)
Out[5]: <User: su 4 su@163.com 434323>

In [6]: User.query.filter_by(id=4).first()
Out[6]: <User: su 4 su@163.com 434323>

In [7]: User.query.filter(User.id==4).first()
Out[7]: <User: su 4 su@163.com 434323>


filter_by: 属性=
filter: 对象.属性==
filter功能更强大，可以实现更多的一些查询，支持比较运算符
```

**flask_sql_demo数据库中的数据**
```
mysql> select * from roles;
+----+-------+
| id | name  |
+----+-------+
|  1 | admin |
|  2 | user  |
+----+-------+
2 rows in set (0.00 sec)

mysql> select * from users;
+----+-------+-----------------+----------+---------+
| id | name  | email           | password | role_id |
+----+-------+-----------------+----------+---------+
|  1 | wang  | wang@163.com    | 123456   |       1 |
|  2 | zhang | zhang@189.com   | 20153    |       2 |
|  3 | chen  | chen@126.com    | 343224   |       2 |
|  4 | su    | su@163.com      | 434323   |       1 |
|  5 | zhou  | zhou@qinbin.com | 666666   |       2 |
|  6 | wu    | wu@gmail.com    | 473735   |       2 |
|  7 | qian  | qian@qq.com     | 544454   |       1 |
|  8 | liu   | liu@163.com     | 765756   |       1 |
|  9 | sun   | sun@163.com     | 090909   |       1 |
| 10 | qin   | qin@qq.com      | 888888   |       2 |
| 11 | lu    | lu@163.com      | 972668   |       2 |
| 12 | tang  | tang@qq.com     | 096536   |       2 |
+----+-------+-----------------+----------+---------+
12 rows in set (0.00 sec)

```

