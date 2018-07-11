---
layout: post
title: Flask学习笔记4--过滤器的基本使用
categories: Flask
description: 过滤器的基本使用
keywords: Python, Flask, 过滤器
---


### 过滤器

过滤器的本质就是函数。有时候我们不仅仅只是需要输出变量的值，我们还需要修改变量的显示，甚至格式化、运算等等，而在模板中是不能直接调用 Python 中的某些方法，那么这就用到了过滤器。

使用方式：

- 过滤器的使用方式为：变量名 | 过滤器
```
{{ variable | filter_name(*args) } }
```

- 如果没有任何参数传递给过滤器，则可以把括号省略掉
```
{ { variable | filter_name } }
```

- 如：`{ { 'hello' | capitalize } }`，这个过滤器的作用：把变量 variable 的值的首字母转换为大写，其他字母转换为小写

### 链式调用

在 jinja2 中，过滤器是可以支持链式调用的，示例如下：
```
{ { "hello world" | reverse | upper } }
```

### 常见的内建过滤器

#### 字符串操作

- safe：禁止转义

```
<p>{ { '<em>hello</em>' | safe } }</p>
```

- capitalize：把变量值的首字母转成大写，其余字母转小写

```
<p>{ { 'hello' | capitalize } }</p>
```

- lower：把值转成小写

```
<p>{ { 'HELLO' | lower } }</p>
```

- upper：把值转成大写

```
<p>{ { 'hello' | upper } }</p>
```

- title：把值中的每个单词大的首字母转换成大写

```
<p>{ { 'hello hello world' | title } }</p>
```

- reverse：字符串反转

```
<p>{ { 'olleh' | reverse } }</p>
```

- format：格式化输出

```
<p>{ { '%s is %d' | format('name', 17) } }</p>
```

- striptags：渲染之前把所有的 HTML 标签都删掉

```
<p>{ { '<em>hello</em>' | striptags } }</p>
```
- truncate：字符串截断

```
<p>{ { 'hello every one' | truncate(9) } }</p>
```

#### 列表操作

- first：取第一个元素

```
<p>{ { [1,2,3,4,5,6] | first } }</p>
```

- last：取最后一个元素

```
<p>{ { [1,2,3,4,5,6] | last } }</p>
```

- length：获取列表长度

```
<p>{ { [1,2,3,4,5,6] | length } }</p>
```

- sum：列表求和

```
<p>{ { [1,2,3,4,5,6] | sum } }</p>
```

- sort：列表排序

```
<p>{ { [6,4,3,5,1,2] | sort } }</p>
```

#### 语句块过滤

```html
{ % filter upper % }
    一大堆文字
{ % endfiletr % }
```
