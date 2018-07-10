---
layout: post
title: Flask学习笔记1--Flask程序Hello world例子
categories: Python
description: Hello world
keywords: Python, Flask
---


## Flask程序运行过程

1. 当客户端想要获取资源时，一般会通过浏览器发起HTTP请求。
2. 此时，Web服务器会把来自客户端的所有请求都交给Flask程序实例。
3. 程序实例使用Werkzeug来做路由分发（URL请求和视图函数之间的对应关系）。
4. 根据每个URL请求，找到具体的视图函数并进行调用。
    - 在Flask程序中，路由的实现一般是通过程序实例的装饰器实现。
5. Flask调用视图函数后，可以返回两种内容：
    - 字符串内容：将视图函数的返回值作为响应内容，返回内容客户端（浏览器）
    - HTML模板内容：获取到数据后，把数据传入HTML模板文件中，模板引擎负责渲染HTTP响应数据，然后返回响应数据给客户端（浏览器）

### 示例：
- 新建Flask项目
- 导入Flask类

```python
from flask import Flask
```

- Flask函数接收一个参数**name**，它会指向程序所在的模块


```python
app = Flask(__name__)
```

- 装饰器的作用是将路由映射到视图函数index

 



**代码如下：**
```python
# -*- coding:utf-8 -*-

# 1.导入Flask扩展
from flask import Flask

# 2.创建Flask应用程序实例
# 需要传入__name__，作用是为了确定资源所在的路径
app = Flask(__name__)


# 3.定义路由及视图函数
# Flask中定义路由是通过装饰器实现的
@app.route('/')
def index():
    return 'hello flask'


# 4.启动程序
if __name__ == '__main__':
    # 执行了app.run就会将Flask程序运行在一个简易的服务器(Flask提供的，用于测试的)
    app.run()
    
```

