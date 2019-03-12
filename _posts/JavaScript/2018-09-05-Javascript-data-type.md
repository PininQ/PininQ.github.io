---
layout: post
title: JavaScript 之数据类型
categories: JavaScript
description: JavaScript 数据类型相关
keywords: JavaScript, Data Type
---

## 常用数据类型

JavaScript 是弱类型脚本语言，声明变量时无需指定变量的数据类型。

JavaScript 是解释时动态决定的，数据保存在内存中时也是有数据类型的。

JavaScript 常用数据类型如下：

- 数值类型 (number): 包含整数和浮点数，比如：4, 4.5

- 布尔类型 (boolean): 只有 true 或 false 两个值，比如：true，false

- 字符串类型 (string): 字符串必须用双引号或单引号括起来。

- 未定义类型 (undefined): 专门用来确定一个已经创建但是没有初值的变量

- 空类型 (null): 用于表明某个变量的值为空。

- 对象类型 (object):javacsript 中的对象、数组等。

其中，

number、string、boolean、null、undefined 属于原始类型

object(Function、Array、Date、...) 属于对象类型

> PS：javascript 没有字符型（char）数据的概念

## 数据类型转换

JavaScript 和其它编程语言一样有进行类型转换的能力。

- 通过 “+” 号或 toString() 方法将数值转换成字符串

- 通过 parseInt() 将字符串转换成整形

- 通过 parseFloat() 将字符串转换成浮点型


## 隐式转换

### + 和 -

![Javascript-Data-Type-02](https://raw.githubusercontent.com/PininQ/MarkdownPhotos/master/Javascript/Javascript-Data-Type/Javascript-Data-Type-01.png)

```javascript
"45" + 45  // 字符串拼接，结果为 4545
"45" - 45  // 两个数相减，结果为 0
```
利用 `+` 和 `-` 得规则可以巧妙的转换类型，
```javascript
num - 0   // 字符串类型的 num 将被隐式转换成数值类型
num + ''  // 数值类型的 num 将被隐式转换成字符串类型
```
> PS：加法运算，除了算数意义，还表示着字符串拼接。

### a == b
`==` 先判断类型是否相同，类型不同则对一些类型做转换，再判断值是否相同，如果是对象还需要判断引用是否相同。

```javascript
"1.23" == 1.23  // true，"1.23" 转换成 1.23 再比较
0 == false // true，false 转换成 0 再比较
null == undefined  // true，undefined 值是派生自 null 值
new Object() == new Object()  //  false，JS 对象比较是比较引用，而不是值
[1, 2] == [1, 2]  // false，比较引用，所以不相等
```
`==` 比较时，有如下特点：

- 类型相同：
    - NaN ≠ NaN
    - new Object ≠ new Object
    - null == null
    - undefined == undefined
- 类型不同，尝试类型转换和比较:
    - null == undefined 相等  // true
    - number == string 转 number     1 == '1.0' // true
    - boolean == ?  转 number        1 == true  // true
    - object == number | string 尝试对象转为基本类型  new String('hi') == 'hi' // true
    - 其它：false


### a === b

`===` 先判断类型是否相同，再判断值是否相同，如果是对象还需要判断引用是否相同。

`===` 比较时，有如下特点：

- 类型不同，返回 false
- 类型相同
    - NaN ≠ NaN
    - new Object ≠ new Object
    - null === null
    - undefined === undefined

## 类型检测

- **typeof**

适合基本类型及 function 检测，遇到 null 失效。

**实例：**
```javascript
typeof 100   // number
typeof true   // boolean
typeof function () {}   // function
typeof(undefined) )   // undefined
typeof(new Object() )   // object
typeof([1， 2] )   // object
typeof(NaN)   // number
typeof(null)   // object，
```
- **instanceof**

通过 `{}.toString` 拿到，适合内置对象和基元类型，遇到 `null` 和 `undefined` 失效 (IE678 等返回 [object Object])。

**实例：**

![Javascript-Data-Type-02](https://raw.githubusercontent.com/PininQ/MarkdownPhotos/master/Javascript/Javascript-Data-Type/Javascript-Data-Type-02.png)

- **Object.prototype.toString**

适合自定义对象，也可以用来检测原生对象，在不同 iframe 和 window 间检测时失效。

**实例：**
```javascript
Object.prototype.toString.apply([]); === "[object Array]"
Object.prototype.toString.apply(function(){}); === "[object Function]"
Object.prototype.toString.apply(null); === "[object Null]"
Object.prototype.toString.apply(undefined); === "[object Undefined]"

IE6/7/8 Object.prototype.toString.apply(null) 返回 "[object Object]"
```

- **constructor**

- **duck type**