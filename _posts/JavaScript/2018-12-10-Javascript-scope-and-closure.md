---
layout: post
title: JavaScript - 作用域和闭包
categories: JavaScript
description: 作用域和闭包的一些知识点以及面试时可能的提问方式
keywords: JavaScript, 作用域, 闭包
---

## 知识点

- 执行上下文
- this
- 作用域
- 作用域链
- 闭包

### 执行上下文

Javascript 中有一个执行上下文 (execution context) 的概念，它定义了变量或函数有权访问的其它数据，决定了他们各自的行为。每个执行环境都有一个与之关联的变量对象，环境中定义的所有变量和函数都保存在这个对象中。

```js
console.log(a) // undefined
var a = 100

fn('zhangsan')
function fn(name) {
  age = 29
  console.log(name, age) // zhangsan 29
  var age
}
```

- 范围：一段 `<script>` 或者一个函数
- 全局：变量定义、函数声明
- 函数：变量定义、函数声明、`this`、`arguments`

> PS：函数声明和函数表达式的区别。

### this

`this` 要在执行时才能确认值，定义时无法确认。


```js
var a = {
  name: 'A',
  fn: function() {
    console.log(this.name)
  }
}
a.fn() // this === a
a.fn.call({ name: 'B' }) // this === {name: 'B'}
var fn1 = a.fn
fn1() // this === window
```
#### this 的使用场景

- 作为构造函数执行
- 作为对象属性执行
- 作为普通函数执行（this === window）
- call apply bind

```js
// 作为构造函数执行
function Foo(name) {
  this.name = name
}
var f = new Foo('zhangsan')

// 作为对象属性执行
var obj = {
  name: 'A',
  printName: function() {
    console.log(this.name)
  }
}
obj.printName()

// 作为普通函数执行
function fn() {
  console.log(this)
}
fn()

// call apply bind
function fn1(name, age) {
  alert(name)
  console.log(this)
}
fn1.call({ x: 100 }, 'zhangsan', 20) // zhangsan

function fn1(name, age) {
  alert(name)
  console.log(this)
}
fn1.apply({ x: 100 }, ['zhangsan', 20]) // zhangsan

// bind
var fn2 = function(name, age) {
  alert(name)
  console.log(this)
}.bind({ y: 200 })
fn2('zhangsan', 20) // zhangsan
```

### 作用域

- 没有块级作用域
- 只有全局作用域和函数作用域

```js
// 无块级作用域
if (true) {
  var name = 'zhangsan'
}
console.log(name) // zhangsan

// 函数和全局作用域
var a = 100
function fn() {
  var a = 200
  console.log('fn', a)
}
console.log('global', a) // global 100
fn() // fn 200
```

### 作用域链

当访问一个变量时，解释器会首先在当前作用域查找标识符，如果没有找到，就去父级作用域找，直到找到该变量的标识符或者不在父级作用域中，这就是作用域链。

作用域链的顶端是全局对象，在全局环境中定义的变量就会绑定到全局对象中。

作用域链和原型继承查找时的区别：

+ 如果查找的属性在作用域链中不存在的话会抛出 `ReferenceError`
+ 如果去查找一个普通对象的属性，但是在当前对象和其原型中都找不到时，会返回 `undefined`。

```js
var a = 100
function fn() {
  var b = 200

  // 当前作用域没有定义的变量，即 “自由变量”
  console.log(a)
  console.log(b)
}
fn()
```

```js
var a = 100
function F1() {
  var b = 200
  function F2() {
    var c = 300
    console.log(a) // a 是自由变量
    console.log(b) // b 是自由变量
    console.log(c)
  }
  F2()
}
F1()
```
### 闭包

红宝书 (p178) 上对于闭包的定义：**闭包是指有权访问另外一个函数作用域中的变量的函数**

关键在于下面两点：

- 是一个函数
- 能访问另外一个函数作用域中的变量

闭包的三个特性：

+ 闭包可以访问当前函数以外的变量

```js
function getOuter() {
  var date = '1112'

  function getDate(str) {
    console.log(str + date) // 访问外部的 date
  }
  return getDate('今天是：') // "今天是：1112"
}
```

+ 即使外部啊还能输已经返回，闭包仍能访问外部函数定义的变量

```js
function getOuter() {
  var date = '1112'

  function getDate(str) {
    console.log(str + date) // 访问外部的 date
  }
  return getDate // 外部函数返回 getDate
}
var today = getOuter()
today('今天是：'); //"今天是：1112"
today('明天不是：'); //"明天不是：1112"
```

+ 闭包可以更新外部变量的值

```js
function updateCount() {
  var num = 0

  function getCount(val) {
    num = val
    console.log('更新后的外部变量：' + num)
  }
  return getCount // 外部函数返回 getCount
}
var count = updateCount()
count(1111) // 更新后的外部变量：1111
count(1112) // 更新后的外部变量：1112
```


```js
function F1() {
  var a = 100
  // 返回一个函数（函数作为返回值）
  return function() {
    console.log(a)
  }
}
// f1 得到一个函数
var f1 = F1()
var a = 200
f1() // 100
```
#### 闭包的使用场景

- 函数作为返回值（上面的 Demo）
- 函数作为参数传递（下面的 Demo）

```js
function F1() {
  var a = 100
  return function() {
    console.log(a) // 自由变量，父作用域寻找
  }
}
var f1 = F1()

function F2(fn) {
  var a = 200
  fn()
}
F2(f1) // 100
```

## 解题

### 1. 说一下对变量提升的理解

在全局作用域或者函数作用域内使用 `var` 声明的变量会被提升到该作用域的最顶部，
被提升的变量值为 `undefined`，比如，在声明之前使用某一个变量，该变量的值
为 `undefined`，for 循环中声明的变量会被提升，使得 for 循环外也可以使用
循环变量。在 es6 中，使用 let 和 const 声明的变量自带块级作用域，不会被提升到
作用域的顶部。
---
- 变量定义
- 函数声明（注意和函数表达式的区别）


### 2. 说明 this 几种不同的使用场景

(1) 初始化一个对象的时候使用，this 指向当前对象的实例。

---
- 作为构造函数执行
- 作为对象属性执行
- 作为普通函数执行（this === window）
- call apply bind

### 3. 创建 10 个 <a> 标签，点击的时候弹出来对应的序号

```js
// 这是一个错误的写法！！！
var i, a
for (i = 0; i < 10; i++) {
  a = document.createElement('a')
  a.innerHTML = i + '<br>'
  a.addEventListener('click', function(e) {
    e.preventDefault()
    // 自由变量，要去父作用域获取值
    alert(i)
  })
  document.body.appendChild(a)
}
```
```js
// 这是一个正确的写法！！！
var i
for (i = 0; i < 10; i++) {
    // 函数作用域
  ;(function(i) {
    var a = document.createElement('a')
    a.innerHTML = i + '<br>'
    a.addEventListener('click', function(e) {
      e.preventDefault()
      // 自由变量，要去父作用域获取值
      alert(i)
    })
    document.body.appendChild(a)
  })(i)
}
```

### 4. 如何理解作用域

在 js 中，作用域分为 全局作用域和 局部作用域（也就是函数作用域），在全局作用域中定义
的变量，局部作用域中也可以访问，而在局部作用域中定义的变量，在全局作用域中时无法访问
的。但在 es6 之前，for 循环，forEach 循环， while 循环等循环或者其它用花括号包裹起来
的区域中声明的变量是被当做全局变量， es6 增加了块级作用域，使得在这些循环体或者花括号包裹
起来的作用域中定义的变量不会被外部所访问，减少了命名冲突，提升了代码的可读性。

---
- 自由变量
- 作用域链，即自由变量的查找
- 闭包的两个场景（返回值为函数和函数作为参数）



### 5. 实际开发中闭包的应用

工厂函数，单例模式，

---
```js
// 闭包实际应用中主要用于封装变量，收敛权限
function isFirstLoad() {
  var _list = [] // 私有变量
  return function(id) {
    if (_list.indexOf(id) >= 0) {
      return false
    } else {
      _list.push(id)
      return true
    }
  }
}
// 使用
var firstLoad = isFirstLoad()
firstLoad(10) // true
firstLoad(10) // false
firstLoad(20) // true
firstLoad(20) // false

// 在 isFirstLoad 函数外面是无法修改 _list 的值的
```


