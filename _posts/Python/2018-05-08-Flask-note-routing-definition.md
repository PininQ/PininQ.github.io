---
layout: post
title: Flask学习笔记2--路由定义的基本方式
categories: Flask
description: 路由定义的基本方式
keywords: Python, Flask, 路由
---

### 请求方式限定

使用methods参数指定可接受的请求方式，可以是多种

```
@app.route('/', methods=['GET','POST'])
def hello():
    return 'hello,world'
```

### 给路由传参示例

有时我们需要将同一类URL映射到同一个视图函数处理，比如：使用同一个视图函数来显示不同用户的订单信息。

路由传递的参数默认当作string处理
```
@app.route('/orders/<order_id>')
def hello_itheima(order_id):
    # 此处的逻辑：去查询数据库改用户的订单信息，并返回
    print(type(order_id)) # 类型为unicode
    return 'hello itcast %d' % order_id
```

这里指定int，会调用系统的路由转换进行匹配和转换。

- 大致原理是将参数强制转换为int，如果成功，则可以进行路由匹配
- 如果参数无法转换成功，就无法匹配该路由


**代码如下：**

```
# -*- coding:utf-8 -*-

# 1.导入Flask扩展
from flask import Flask

# 2.创建Flask应用程序实例
# 需要传入__name__，作用是为了确定资源所在的路径
app = Flask(__name__)


# 3.定义路由及视图函数
# Flask中定义路由是通过装饰器实现的
# 路由默认只支持GET，如果需要增加，需要自行指定
@app.route('/', methods=['GET', 'POST'])
def index():
    return 'hello flask'


# 使用同一个视图函数，来显示不同用户的订单信息
# <定义路由的参数>。<>内需要起个名字
@app.route('/orders/<int:order_id>')
def get_order_id(order_id):
    # 参数类型，默认是字符串，Unicode编码
    print(type(order_id))

    # 有时候，需要对路由做访问优化。订单ID应该是int类型

    # 需要在视图函数的()内填入参数名，那么后面的代码才能去使用
    return 'order_id %s' % order_id


# 4.启动程序
if __name__ == '__main__':
    # 执行了app.run就会将Flask程序运行在一个简易的服务器(Flask提供的，用于测试的)
    app.run()

```

程序运行结果如下：

![路由传递的参数类型](/images/posts/flask/flaskdemo.png)


![访问127.0.0.1:8000](/images/posts/flask/flaskdemorun.png)
