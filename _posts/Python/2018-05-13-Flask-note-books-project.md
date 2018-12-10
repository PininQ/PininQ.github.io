---
layout: post
title: Flask 学习笔记 8--Flask 的综合小案例
categories: Flask
description: Flask 的综合小案例，包含基本的 CRUD 操作
keywords: Python, Flask
---

### Flask 的综合小案例

1. 配置数据库
    1. 导入 SQLAlchemy 扩展
    1. 创建 db 对象，并配置参数
    1. 终端创建数据库
1. 添加书和作者模型
    1. 模型继承 db.Model
    1. \_\_tablename\_\_：表名
    1. db.Column：字段
    1. db.relationship：关系引用
1. 添加数据
1. 使用模板显示数据库查询的数据
    1. 查询所有的作者信息，让信息传递给模板
    1. 模板中按照格式，依次 for 循环作者和书籍即可(作者获取书籍，用的是关系引用)
1. 使用 WTF 显示表单
    1. 自定义表单类
    1. 模板中显示
    1. secret_key / 编码问题 / csrf_token
1. 实现相关的增删逻辑
    1. 增加数据
    1. 删除书籍 --> 网页中删除 --> 点击需要发送书籍的 ID 给删除书籍的路由 --> 路由需要接收参数
    1. 删除书籍 url_for 的使用 / for else 的使用 / redirect 的使用
    1. 删除作者

**`Flask_books_project.py` 文件**

```python
# -*- coding:utf-8 -*-

from flask import Flask, render_template, flash, request, redirect, url_for
from flask_sqlalchemy import SQLAlchemy
from flask_wtf import FlaskForm
from wtforms import StringField, SubmitField
from wtforms.validators import DataRequired

import sys

reload(sys)
sys.setdefaultencoding('utf-8')

app = Flask(__name__)

# 数据库配置：数据库地址 / 关闭自动跟踪修改
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql://root:qiu66666@127.0.0.1/flask_books'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
app.secret_key = 'qinbin'

# 创建数据库对象
db = SQLAlchemy(app)
'''
1. 配置数据库
    a. 导入 SQLAlchemy 扩展
    b. 创建 db 对象，并配置参数
    c. 终端创建数据库
2. 添加书和作者模型
    a. 模型继承 db.Model
    b. __tablename__: 表名
    c. db.Column: 字段
    d. db.relationship: 关系引用
3. 添加数据
4. 使用模板显示数据库查询的数据
    a. 查询所有的作者信息，让信息传递给模板
    b. 模板中按照格式，依次 for 循环作者和书籍即可(作者获取书籍，用的是关系引用)
5. 使用 WTF 显示表单
    a. 自定义表单类
    b. 模板中显示
    c. secret_key / 编码问题 / csrf_token
6. 实现相关的增删逻辑
    a. 增加数据
    b. 删除书籍 --> 网页中删除 --> 点击需要发送书籍的 ID 给删除书籍的路由 --> 路由需要接收参数
       删除书籍 url_for 的使用 / for else 的使用 / redirect 的使用
    c. 删除作者
'''


# 定义书和作者模型
# 作者模型
class Author(db.Model):
    # 表名
    __tablename__ = 'authors'

    # 字段
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(16), unique=True)

    # 关系引用
    # books 是给自己 (Author 模型) 使用的，author 是给 Book 模型用的
    books = db.relationship('Book', backref='author')

    def __repr__(self):
        return 'Author: %s' % self.name


# 书籍模型
class Book(db.Model):
    __tablename__ = 'books'

    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(16), unique=True)
    author_id = db.Column(db.Integer, db.ForeignKey('authors.id'))

    def __repr__(self):
        return 'Book: %s %s' % (self.name, self.author_id)


# 自定义表单类
class AddBookForm(FlaskForm):
    author = StringField('作者：', validators=[DataRequired()])
    book = StringField('书名：', validators=[DataRequired()])
    submit = SubmitField('提交')


# 删除书籍 --> 网页中删除 --> 点击需要发送书籍的 ID 给删除书籍的路由 --> 路由需要接收参数
@app.route('/delete_book/<book_id>')
def delete_book(book_id):
    # 1. 查询当前数据库，是否有该 ID 的书，如果有就删除，没有就提示错误
    book = Book.query.get(book_id)

    # 2. 如果有就删除
    if book:
        try:
            db.session.delete(book)
            db.session.commit()
        except Exception as e:
            print e
            flash('删除书籍出错')
            db.session.rollback()
    else:
        # 3. 没有就提示错误
        flash('没有该书籍')

    # redirect: 重定向，需要传入网络 / 路由地址
    # url_for('index'): 需要传入视图函数名，返回该视图函数对应的路由地址
    print url_for('index')
    return redirect(url_for('index'))

    # 如何返回当前网址 --> 重定向
    # return redirect('www.baidu.com')
    # return redirect('/')


# 删除作者
@app.route('/delete_author/<author_id>')
def delete_author(author_id):

    # 查询当前数据库，是否有该 ID 的作者，如果有就删除(先删书，再删作者)，没有就提示错误
    # 1. 查询当前数据库
    author = Author.query.get(author_id)

    # 2. 如果有就删除(先删书，再删作者)
    if author:
        # 删除某一作者的书籍的方式 1: 先查询出所有书籍放到列表中再遍历删除
        # books = Book.query.filter_by(author_id=author.id)
        # for book in books:
        #     if book:
        #         try:
        #             db.session.delete(book)
        #             db.session.commit()
        #         except Exception as e:
        #             print e
        #             flash('删除书籍出错')
        #             db.session.rollback()
        #     else:
        #         # 3. 没有就提示错误
        #         flash('没有该书籍')
        try:
            # 删除某一作者的书籍的方式 2: 查询之后直接删除
            Book.query.filter_by(author_id=author.id).delete()

            # 删除作者
            db.session.delete(author)
            db.session.commit()
        except Exception as e:
            print e
            flash('删除作者出错')
            db.session.rollbback()
    else:
        flash('没有该作者')
    return redirect(url_for('index'))


@app.route('/', methods=['GET', 'POST'])
def index():
    # 创建自定义的表单类
    add_book_form = AddBookForm()

    '''
    验证逻辑：
    1. 调用 WTF 的函数实现验证
    2. 验证通过获取数据
    3. 判断作者是否存在
    4. 如果作者存在，判断书籍是否存在，没有重复书籍就添加数据，如果重复就提示错误
    5. 如果作者不存在，添加作者和书籍
    6. 验证不通过就提示错误
    '''

    # 1. 调用 WTF 的函数实现验证
    if add_book_form.validate_on_submit():

        # 2. 验证通过获取数据
        author_name = add_book_form.author.data
        book_name = add_book_form.book.data

        # 3. 判断作者是否存在
        author = Author.query.filter_by(name=author_name).first()

        # 4. 如果作者存在
        if author:
            # 判断书籍是否存在
            book = Book.query.filter_by(name=book_name).first()

            # 如果存在就提示错误
            if book:
                flash('已存在同名书籍')

            # 没有重复书籍就添加数据
            else:
                try:
                    new_book = Book(name=book_name, author_id=author.id)
                    db.session.add(new_book)
                    db.session.commit()
                except Exception as e:
                    print e
                    flash('书籍添加失败')
                    db.session.rollback()
        else:
            # 5. 如果作者不存在，添加作者和书籍
            try:
                new_author = Author(name=author_name)
                db.session.add(new_author)
                db.session.commit()

                new_book = Book(name=book_name, author_id=new_author.id)
                db.session.add(new_book)
                db.session.commit()
            except Exception as e:
                print e
                flash('作者和书籍添加失败')
                db.session.rollback()
    else:
        # 6. 验证不通过就提示错误
        if request.method == 'POST':
            flash('参数不全')

    # 查询所有的作者信息，让信息传递给模板
    authors = Author.query.all()

    return render_template('books.html', authors=authors, form=add_book_form)


if __name__ == '__main__':
    db.drop_all()
    db.create_all()

    # 生成作者数据
    au1 = Author(name='老王')
    au2 = Author(name='老伶')
    au3 = Author(name='老秦')
    au4 = Author(name='黑狗')
    # 把数据提交给用户会话
    db.session.add_all([au1, au2, au3, au4])
    # 提交会话
    db.session.commit()
    # 生成书籍数据
    bk1 = Book(name='老王回忆录', author_id=au1.id)
    bk2 = Book(name='我读书少，你别骗我', author_id=au1.id)
    bk3 = Book(name='如何才能让自己更骚', author_id=au2.id)
    bk4 = Book(name='如何征服美丽少女', author_id=au3.id)
    bk5 = Book(name='如何政府美丽少男', author_id=au3.id)
    bk6 = Book(name='论怎么与菜鸡交流', author_id=au3.id)
    bk7 = Book(name='欢欢乐乐就是我', author_id=au4.id)
    # 把数据提交给用户会话
    db.session.add_all([bk1, bk2, bk3, bk4, bk5, bk6, bk7])
    # 提交会话
    db.session.commit()
    app.run(debug=True)

```

**`books.html` 文件**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<form method="post">
    { { form.csrf_token() } }
    { { form.author.label } }{ { form.author } } <br>
    { { form.book.label } }{ { form.book } } <br>
    { { form.submit } } <br>
    {#  显示消息闪现的内容  #}
    { % for message in get_flashed_messages() % }
        { { message } }
    { % endfor % }

</form>

<hr>

{# 先遍历作者，然后在作者里面遍历书籍 #}
<ul>
    { % for author in authors % }
        <li>{ { author.name } }/<a href="{ { url_for('delete_author', author_id=author.id) } }">删除</a></li>
        <ul>
            { % for book in author.books % }
                <li>《{ { book.name } }》/<a href="{ { url_for('delete_book', book_id=book.id) } }">删除</a></li>
            { % else % }
                <li > 无</li>
            { % endfor % }
        </ul>
    { % endfor % }

</ul>

</body>
</html>

```