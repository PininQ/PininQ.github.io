---
layout: post
title: JavaScript - 原型规则和示例
categories: JavaScript
description: JavaScript - 原型规则和示例
keywords: JavaScript, 原型
---


- 所有的引用类型（数组、对象、函数），都具有对象的特性，即可以自由扩展属性（除了 `null` 以外）

- 所有的引用类型（数组、对象、函数），都有一个 `__proto__` 属性（隐式原型），属性值是一个普通的对象。
- 所有的函数，都有一个 `prototype` 属性（显示原型），属性值也是一个普通的对象。
- 所有的引用类型（数组、对象、函数），`__proto__` 属性值指向它的构造函数的 `prototype` 属性值。


```js
// 数组、对象、函数都有对象的特性（扩展属性）
// 对象
var obj = {}
obj.a = 100
// 数组
var arr = []
arr.a = 100
// 函数
function fn () {}
fn.a = 100

// 隐式原型
console.log(obj.__proto__) // {}
console.log(arr.__proto__) // []
console.log(fn.__proto__) // [Function]

// 显示原型
console.log(fn.prototype) // fn {}

// __proto__ 属性值指向其构造函数的 "prototype" 属性值
console.log(obj.__proto__ === Object.prototype) // true
```

- 当试图得到一个对象的某个属性时，如果这个对象b本身没有这个属性，那么会去它的 `__proto__` （即它的构造函数的 `prototype`）中寻找。


```js
// 构造函数
function Foo(name, age) {
  this.name = name
}
Foo.prototype.alertName = function() {
  alert(this.name)
}
// 创建实例
var f = new Foo('zhangsan')
f.printName = function() {
  console.log(this.name)
}
// 测试
f.printName()
f.printName() // 去原型链上查找该方法
```
