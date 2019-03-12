---
layout: post
title: Django 整体开发流程梳理
categories: Django
description: Django 整体开发流程梳理
keywords: Python, Django
---


## Django 项目主要文件

- `manage.py` 是一个命令行工具，可以是我们使用多种方式对 Django 项目进行交互
- `__init__.py` 一个空文件，他告诉 python 这个目录应该被看作一个 python 目录
- `setting.py` 项目的配置文件
- `urls.py` 项目的 url 声明
- `wsgi.py` 项目于 WSGI 兼容的 web 服务器入口

## Django 开发流程梳理

1. 创建工程：执行 `django-admin startproject project`

2. 创建项目：执行 `python manage.py startapp myApp`

3. 激活项目：修改 `setting.py` 中的 `INSTALLED_APPS`

4. 配置数据库：

   - 修改 `__init__.py` 文件

   - 修改 `setting.py` 文件中的 `DATABASES`

5. 创建模型类：在项目目录下的 `model.py` 文件中

6. 生成迁移文件：执行 `python manage.py makemigrations`

7. 执行迁移：执行 `python manage.py migrate`

8. 配置站点

9. 创建模板目录 / 项目模板目录

10. 在 `setting.py` 文件中 `TEMPLATES` 配置模板路径

11. 在 `project` 目录下修改 `urls.py`

12. 在项目目录下创建 `urls.py`




## Django 常用命令总结

- 创建 Django 项目：django-admin startproject project

- 创建 APP：python manage.py startapp myApp

- 生成迁移文件：python manage.py makemigrations

- 执行迁移：python manage.py migrate

- 进入到 Python shell：python manage.py shell

- 启动 Django 开发服务器：python manage.py runserver

- Django 帮助文档：python manage.py -h

- Django 站点超级用户创建：python manage.py createsuperuser

- Django 命令格式：python manage.py <command> [options]



## Clone Django 项目简单修改流程

1. 在 setting.py 里修改数据库名

2. 在 setting.py 里修改数据库密码

3. 删除迁移文件（在对应目录里鼠标右键删除）

4. 在数据库中创建对应的第一步的数据库名

5. 执行生成迁移文件

6. 执行迁移

7. 启动 Django 服务

8. 浏览器测试

> PS：如果有一个现成的 Django 项目，需要添加表，可以先在 `model.py` 中添加对应的类，然后再生成迁移文件，再然后执行迁移，`migrations` 目录下就会多出一个 `0002_类名.py` 的文件，相应的，数据库中也会新增加的一张新表。如果数据库需要修改字段或者字段设计不合理或者重新创建数据库等等，需要把迁移文件全部删除，数据库也删除，再重新生成迁移文件 --- 然后执行迁移，对应的数据库表就会自动生成了。当然，如果数据库数据较多，比较重要，也可以对 model.py 和数据库中的表字段一起修改，不需要删除数据库。



Django 简单项目链接：[https://github.com/pininq/django-learn/tree/master/project](https://github.com/pininq/django-learn/tree/master/project)


