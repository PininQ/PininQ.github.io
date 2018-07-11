---
layout: post
title: Flask学习笔记5--WTF表单
categories: Flask
description: WTF表单的基本了解和使用
keywords: Python, Flask, 过滤器
---


### Web表单

web表单是web应用程序的基本功能。

它是HTML页面中负责数据采集的不见。表单由三个部分组成：表单标签、表单域、表单按钮。表单允许用户输入数据，负责HTML页面数据采集，通过表单将用户输入的数据提交给服务器。

在Flask中，为了处理web表单，我们一般使用Flask-WTF扩展，它封装了WTForms，并且它有验证表单数据的功能。


### WTForms支持的HTML标准字段


字段对象 | 说明
---|---
StringField | 文本字段
TextAreaField | 多行文本字段
PasswordField | 密码文本字段
HiddenField | 隐藏文本字段
DateField | 文本字段，值为datetime.date文本格式
DateTimeField | 文本字段，值为datetime.datetime文本格式
IntegerField | 文本字段，值为整数
DecimalField | 文本字段，值为decimal.Decimal
FloatDield | 文本字段，值为浮点数
BooleanField | 复选框，值为True和False
RadioField | 一组单选框
SelectField | 下拉列表框
SelectMutipleField | 下拉列表，可选择多个值
FileField | 文件上传字段
SubmitField |  表单提交按钮
FormField | 把表单作为字段嵌入另一个表单
FieldList | 一组指定类型的字段


### WTForms常用验证函数


验证函数| 说明
---|---
DataRequried | 确保字段中有数据
EqualTo | 比较两个字段的值，常用于比较两次密码输入
Length | 验证输入的字符串长度
NumberRange | 验证输入的值在数字范围内
URL | 验证URL
AnyOf | 验证输入值在可选列表中
NoneOf | 验证输入值不在可选列表中
使用Flask-WTF需要配置参数SECRET_KEY。

CSRF_ENABLED是为了CSRF（跨站请求伪造）保护。SECRET_KEY用来生成加密令牌，当CSRF激活的时候，该设置会根据设置的密匙生成加密令牌。在HTML页面中直接写form表单：


### 示例-使用普通方式实现表单

**在HTML页面中直接写form表单：**

```html
<form method="post">
    <label>用户名:</label><input type="text" name="username"> <br>
    <label>密码:</label><input type="password" name="password"> <br>
    <label>确认密码:</label><input type="password" name="password2"> <br>
    <input type="submit" value="提交"> <br>
    { % for message in get_flashed_messages() % }
        {{ message }}
    { % endfor % }
</form>
```
**视图函数中获取表单数据：**
```Python
# -*- coding:utf-8 -*-

from flask import Flask, render_template, request, flash

app = Flask(__name__)

app.secret_key = 'qinbin'
'''
目的：实现一个简单的登录的逻辑处理
1.路由需要有get和post两种请求方式 --> 需要判断请求方式
2.获取请求的参数
3.判断参数是否填写 & 密码是否相同
4.如果判断都没有我呢提，就返回一个success
'''

'''
给模板传递消息
flash --> 需要对内容加密。因此需要设置secret_key，做加密消息的混淆
'''Python
@app.route('/', methods=['GET', 'POST'])
def index():
    # request:请求对象 --> 获取请求方式、数据

    # 1.判断请求方式
    if request.method == 'POST':

        # 2.获取请求参数
        username = request.form.get('username')
        password = request.form.get('password')
        password2 = request.form.get('password2')

        # 3.判断参数是否填写 & 密码是否相同
        if not all([username, password, password2]):
            # print '参数不完整'
            flash(u'参数不完整') # flash消息闪现
        elif password != password2:
            # print '密码不一致'
            flash(u'密码不一致')
        
        # 4.没有问题就返回'success'
        else:
            print password
            return 'success'
    return render_template('index.html')


if __name__ == '__main__':
    app.run(debug=True)
```
### 示例-使用Flask-WTF实现表单

**模板页面**
```
<form method="post">
    {#设置csrf_token#}
    {{ form.csrf_token() }}
    {{ form.username.label }}{{ form.username }}<br>
    {{ form.password.label }}{{ form.password }}<br>
    {{ form.password2.label }}{{ form.password2 }}<br>
</form>
```
**视图函数**

```Python
# -*- coding: utf-8 -*-
__author__ = 'QB'

from flask import Flask, render_template, request, flash

# 导入wtf扩展的表单类
from flask_wtf import FlaskForm

# 导入自定义表单需要的字段
from wtforms import SubmitField, StringField, PasswordField

# 导入wtf扩展高蹄的表单验证器
from wtforms.validators import DataRequired, EqualTo

# 解决编码问题
import sys
reload(sys)
sys.setdefaultencoding("utf-8")

app = Flask(__name__)
app.config['SECRE_KEY'] = 'qinbin'


# 自定义表单类，文本字段、密码字段、提交按钮
# 需要自定义一个表单类
class RegisterForm(FlaskForm):
    username = StringField('用户名:', validators=[DataRequired()])
    password = PasswordField('密码:', validators=[DataRequired()])
    password2 = PasswordField('确认密码:', validators=[DataRequired(), EqualTo('password', input=SubmitField('提交'))])


# 定义根路由视图函数，生成表单对象，获取表单数据，进行表单数据验证
@app.route('/form', methods=['GET', 'POST'])
def form():
    register_form = RegisterForm()

    if request.method == 'POST':
        # 调用validate_on_submit方法，可以一次性执行完所有验证函数的逻辑
        if register_form.validate_on_submit():
            # 进入这里就表示所有的逻辑都验证成功
            username = request.form.get('username')
            password = request.form.get('password')
            password2 = request.form.get('password2')
            print username
            return 'success'

        else:
            # message = register_form.errors.get('password2')[0]
            # flash(message)
            flash('参数有误')
    return render_template('wtf.html', form=register_form)
```


