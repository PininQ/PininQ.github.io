---
layout: post
title: Flask学习笔记3--Jinja2模板引擎
categories: Flask
description: Jinja2模板引擎
keywords: Python, Flask, Jinja2
---

### 模板

在前面的示例中，视图函数的主要作用是生成请求的响应，这是最简单的请求。实际上，视图函数有两个作用：处理业务逻辑和返回响应内容。在大型应用中，把业务逻辑和表现内容放在一起，会增加代码的复杂度和维护成本。而模板引擎的作用是承担视图函数的了一个作用，即返回响应内容。

- 模板其实是一个包含响应文本的文件，其中用占位符（变量）表示动态部分，告诉模板引擎其具体的值需要从使用的数据中获取；
- 使用真实值替换变量，再返回最终得到的字符串，这个过程称为“渲染”
- Flask是使用`Jinja2`这个模板引擎来渲染模板
 

使用模板的好处：

- 视图函数只负责业务逻辑和数据处理（业务逻辑方面）
- 而模板则渠道视图函数的数据结果进行展示（视图展示方面）
- 代码结构清晰，耦合度低

### Jinja2

#### 两个概念：

- Jinja2：是Python下一个被广泛应用的模板引擎，是由Python实现的模板语言，他的设计思想来源于Django的模板引擎，并扩展了其语法和一系列强大的功能，其是Flask内置的模板语言。
- 模板语言：是一种被设计来自生成文档的简单文本格式，在模板语言中，一般都会把一些变量传给模板，替换模板的特定位置上预先定义好的占位变量名。


#### 渲染模板函数

- Flask提供的`render_template`函数封装了该模板引擎
- `render_template`函数的第一个参数是模板的文件名，后面的参数都是键值对，表示模板中变量对应的真实值。


#### 使用

**注释**

- 使用{# #}进行注释

```
{# {{ name }} #}
```

**变量代码块**

- {{}}来表示变量名，这种语法叫做`变量代码块`
 
```
{ { post.title } }
```

Jinja2模板中的变量代码块可以是任意Python类型或者对象，只要它能够被Python的str()方法转换为一个字符串就可以，比如，可以通过下面的方式显示一个字典或者列表中的某个元素：

```
{ { your_dict['key'] } }
{ { your_list[0] } }
 ```
 
**控制代码块**
- 用{ %% }定义的`控制代码块`，可以实现一些语言层次的功能，比如循环或者if语句

```HTML
{ % if user % }
    {{ user }}
{ % endif % }
    hello!

{ % for index in indexs % }
    {{ index }}
{ % endfor % }
```


**Flask_test_demo.py代码如下：**
```python
# -*- coding:utf-8 -*-

from flask import Flask, render_template

app = Flask(__name__)


# 1.如何返回一个网页(模板)
# 2.如何给模板填充数据
@app.route('/')
def index():
    # 比如需要传入网址
    url_str = 'www.baidu.com'

    my_list = [1, 3, 5, 7, 9]

    my_dict = {'name': 'qiuqiu', 'age':22}

    my_int = 48
    return render_template('index.html',
                           url_str=url_str,
                           my_list=my_list,
                           my_dict=my_dict,
                           my_int=my_int)


if __name__ == '__main__':
    app.run(debug=True)

```


**index.html代码如下：**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
这是模板 <br>
这是首页 <br>


{#下面是变量代码块的使用#}
{{ url_str }} <br>

{#列表的使用#}
{{ my_list }} <br>
{{ my_list.2 }} <br>
{{ my_list[2] }} <br>

{#字典的使用#}
{{ my_dict }} <br>
{{ my_dict.name }} <br>
{{ my_dict['age'] }} <br>

{#整型的使用#}
{{ my_int }} <br>

<hr>

{#控制代码块 { %  % }#}
{#for循环的使用，建议先写for，然后按住tab键进行代码补全#}
{ % for num in my_list % }
    {#数据大于3，才能显示#}
    { % if num > 3 % }
        {{ num }} <br>
    { % endif % }
{ % endfor % }

</body>
</html>
```



程序运行结果如下：


![访问127.0.0.1:8000](/images/posts/flask/flaskJinja2.png)
