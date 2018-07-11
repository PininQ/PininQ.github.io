---
layout: page
title: About
description: 打码改变世界
keywords: Bin Qin, 覃斌
comments: true
menu: 关于
permalink: /about/
---

我是覃斌，码而生，码而立。

仰慕「优雅编码的艺术」。

坚信熟能生巧，努力改变人生。

## 我的链接

{% for website in site.data.social %}
* {{ website.sitename }}：[@{{ website.name }}]({{ website.url }})
{% endfor %}

## Skill Keywords

{% for category in site.data.skills %}
### {{ category.name }}
<div class="btn-inline">
{% for keyword in category.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}

--- 
# 求职简历

- 手机：18878475439
- Email：2538323140@qq.com
- QQ / 微信号：2538323140/18878475439

## 个人信息

 - 覃斌 / 男 / 1994
 - 本科 / 贺州学院 · 数学与计算机学院 · 软件工程
 - 工作年限：1 年
 - GitHub：[https://github.com/qinbin52qiul](https://github.com/qinbin52qiul)
 - 技术博客（有道云笔记搬迁中）：[https://qinbin.top](https://qinbin.top)
 - CSDN 博客：[https://blog.csdn.net/qq_34081993](https://blog.csdn.net/qq_34081993)
 - 期望职位：Python 开发工程师
 - 期望薪资：税前月薪 5k~8k，特别喜欢的公司可例外
 - 期望城市：广州


## 工作经历

### 广州天源迪科信息技术有限公司（2017年8月~2018年2月）

#### 新指标开发

- 业务部门下发需求，进行需求分析，明确需求做什么；
- 梳理数据源、脚本、逻辑，和业务部门确认需求；
- job 程序开发，将逻辑转换成 DataStage 或 Informatica 开发，生成 IQ 入库文件；
- 测试 job（逻辑和数据测试），编写上线所需文档：元数据配置文档、测试报告、建表语句；
- 程序评审上线；
- 配置调度表和前置调度表，交给运维进行调度。


#### 存储过程改造和临时取数

- 存储过程改造是将Oracle中的存储过程的逻辑转换成 DataStage 或 Informatica 开发，降低Oracle数据库压力，提高跑数速度。
- 临时取数是业务部门需要的历史某几个月数据，按照需求开发job从各个数据库中提取数据，然后与业务部门确认数据的准确性，ftp到指定服务器。

#### 其他

实习期间在联通省分现场做ETL开发，负责过1个新指标开发，3个临时取数以及多个存储过程改造。帮助同事梳理数据源、编写序列job跑数并核对数据准备性，优化job，优化SQL，降低job跑数所需时间。对每天工作内容、工作期间遇到的问题和问题解决过程进行了记录。


## 项目经验


### 小花椒视频网站（2018年5月~2018年6月）

 - 开发工具：Pycharm + Navicat Premium 12 + RedisDesktopManager + SecureCRT
 - 所用技术：Python、Flask、Redis、MariaDB、HTML、CSS、Javascript、JQuery、Ajax、Bootstrap、Vue.js
 - 项目简介：小花椒网站是一个基于 Flask 框架而搭建的视频网站。网站前台使用了 DPlayer 和 jwplayer 两个播放器，可添加视频弹幕，弹幕存放于服务器中的 Redis 中。评论功能采用了 ueditor 编辑器。还可以收藏电影、查看 Ta 动态等等。网站后台主要是对 MariaDB 的 CRUD 操作。网站部署在 CentOS7 环境下，部署配置为 Flask + uwsgi + Nginx + Supervisor。
- 网站地址：[https://www.xhuajiao.top](https://www.xhuajiao.top)
- 项目总结：使得自己对 Flask 框架有了一个更深的了解。

### 桂林旅游路线的智能数据分析（2018年2月~2018年4月）

 - 开发工具：Pycharm + Robo 3T 1.2.1 + Excel + SecureCRT
 - 所用技术：Python、Scrapy、BeautifulSoup、MongoDB、MySQL、Echarts
 - 项目简介：该项目主要采集马蜂窝的部分景点评论和评分数据以及去哪儿网的景区经纬度、酒店以及景区的数据，然后进行数据分析。使用 BeautifulSoup 和 XPath 以及 scrapy 编写爬虫代码，将采集的数据存储到 MongoDB 和 Excel 中。使用 jieba 对评论数据进行分词，wordcloud 生成词云。主要进行的数据分析有游客评论数据的特征分析和情感倾向分析以及景区相关数据的描述性统计分析。撰写数据分析报告，对整个数据分析成果进行呈现。
- 存在的不足：景区评论数据较少，只有 3943 条，未对对图片评论数据进行分析，情感分析结果不太准确，研究的内容不够深入，并且没有专业的研究方法。
- 项目总结：使得自己对数据分析有了初步了解，学会使用 Python 数据分析和爬虫的相关工具和框架。

### 其他项目

- 使用 Flask 开发了一个简易的博客管理系统，主要作用是管理博客文章。
- 使用 Python 开发博客，自己编写最简单的 ORM 框架、Web 框架以及 MVC 框架。
- 使用 Java 开发过三个网站，分别是模仿天猫商城、学生信息管理系统以及 KTV 管理系统。



## 个人技能

- 熟悉 Python 开发环境，具备扎实的 Python 编程基础，掌握基本的数据结构和算法。
- 熟悉 Flask 框架，了解 Django 等 Web 框架，了解 restful API。
- 掌握 HTML/CSS/JavaScript 等 Web 页面技术，能完成精美的网页布局。
- 熟悉主流的前端框架 jQuery/Bootstrap/Vue.js/easyui，熟练 Chrome 调试工具。
- 掌握关系数据库的基本使用和常用的 SQL 优化技巧。
- 具有 NoSQL 数据库如 MongoDB、Redis 等使用经验。
- 熟悉 Linux 操作系统，熟练使用 shell 基本命令。
- 熟练使用 Pycharm、Eclipse 等开发工具，了解 nginx、git 等的配置与使用。
- 掌握 MVC 和 MVVC 开发模式来开发 Web 网站。
- 熟悉面向对象设计和常用设计模式，并具备良好的编程风格。
- 其他：掌握 PS 基础，有项目文档编写经验。


## 致谢
感谢您花时间阅读我的简历，期待能有机会和您共事。

