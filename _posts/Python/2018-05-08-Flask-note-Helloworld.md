---
layout: post
title: Flask 学习笔记 1--Flask 程序 Hello world 例子
categories: Flask
description: Hello world
keywords: Python, Flask
---


## Flask 程序运行过程

1. 当客户端想要获取资源时，一般会通过浏览器发起 HTTP 请求。
2. 此时，Web 服务器会把来自客户端的所有请求都交给 Flask 程序实例。
3. 程序实例使用 Werkzeug 来做路由分发（ URL 请求和视图函数之间的对应关系）。
4. 根据每个 URL 请求，找到具体的视图函数并进行调用。
    - 在 Flask 程序中，路由的实现一般是通过程序实例的装饰器实现。
5. Flask 调用视图函数后，可以返回两种内容：
    - 字符串内容：将视图函数的返回值作为响应内容，返回内容客户端（浏览器）
    - HTML 模板内容：获取到数据后，把数据传入 HTML 模板文件中，模板引擎负责渲染 HTTP 响应数据，然后返回响应数据给客户端（浏览器）

**示例**
- 新建 Flask 项目
- 导入 Flask 类

```python
from flask import Flask
```

- Flask 函数接收一个参数 **name**，它会指向程序所在的模块


```python
app = Flask(__name__)
```

- 装饰器的作用是将路由映射到视图函数 index



**Flask_Demo.py 文件代码如下**
```python
# -*- coding:utf-8 -*-

# 1. 导入 Flask 扩展
from flask import Flask

# 2. 创建 Flask 应用程序实例
# 需要传入__name__，作用是为了确定资源所在的路径
app = Flask(__name__)


# 3. 定义路由及视图函数
# Flask 中定义路由是通过装饰器实现的
@app.route('/')
def index():
    return 'hello flask'


# 4. 启动程序
if __name__ == '__main__':
    # 执行了 app.run 就会将 Flask 程序运行在一个简易的服务器 (Flask 提供的，用于测试的)
    app.run()

```

程序运行结果如下：

![访问 127.0.0.1:8000](/images/posts/flask/flaskhelloworld.png)

