---
layout: post
title: JavaScript 之表达式和运算符
categories: JavaScript
description: JavaScript 表达式和运算符
keywords: JavaScript, Expression, Operator
---

## 表达式

表达式是一组代码的集合，它返回一个值。——[《JavaScript 权威指南》](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Expressions_and_Operators#%E8%A1%A8%E8%BE%BE%E5%BC%8F)

JavaScript 有以下表达式类型（摘自 [《JavaScript 权威指南》](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Expressions_and_Operators#%E8%A1%A8%E8%BE%BE%E5%BC%8F)）：

- 算数: 得出一个数字, 例如 3.14159. (通常使用 `arithmetic operators`.)
- 字符串: 得出一个字符串, 例如, "Fred" 或 "234". (通常使用 `string operators`.)
- 逻辑值: 得出 true 或者 false. (经常涉及到 `logical operators`.)
- 基本表达式: JavaScript 中基本的关键字和一般表达式。
- 左值表达式: 分配给左值。


### 原始表达式

原始表达式是最简单的表达式，是表达式的最小单位，不再包含其他表达式。

JavaScript 的原始表达式包含常量或直接量、关键字和变量。

**示例**
```javascript
(1) 常量、直接量    3.14, "test"

(2) 关键字          true、false、null、this

(3) 变量            i, j, k, undefined

//undefined 是全局变量，和 null 不同，它不是一个关键字
```
原始表达式和原始表达式之间可以复合成复合表达式。

![Javascript-expression-and-operator-01](https://raw.githubusercontent.com/pininq/MarkdownPhotos/master/Javascript/Javascript-Data-Type/Javascript-expression-and-operator-01.png)

### 初始化表达式

对象、数组初始化表达式实际上是一个新创建的对象或数组，这些初始化表达式有时称作 “对象直接量” 或“数组直接量”，然而和布尔直接量不同，它们不是原始表达式，因为它们包含的成员和元素都是子表达式。

**示例**
```javascript
var i = [1, 2]  等价于 var i = new Array(1, 2);

var j = [1, , , 4]  表示 [1, undefined, undefined , 4]

var k = {x : 1, y : 2}  等价于 var o = new Object(); o.x = 1; o.y = 2;
```


### 函数表达式

函数表达式非常类似于 `函数声明`，并且两者拥有几乎相同的语法。函数表达式与函数声明的最主要区别是 `函数名称`，在函数表达式中可省略它，从而创建匿名函数。一个函数表达式可以被用作一个 `IIFE`（Immediately Invoked Function Expression，即时调用的函数表达式），它一旦定义就运行。

**示例**

```javascript
// 函数表达式
var f = function(){};

// 即时调用的函数表达式
(function () {
    console.log('hello world');
})();
```

### 属性访问表达式

**示例**

```javascript
var o = {x : 1};

// 属性访问表达式
o.x
o['x']
```


### 调用表达式


```javascript
func();
```

### 对象创建表达式


```javascript
new Func(1, 2);

new Object;
```

## 运算符

![Javascript-expression-and-operator-02](https://raw.githubusercontent.com/pininq/MarkdownPhotos/master/Javascript/Javascript-Data-Type/Javascript-expression-and-operator-02.png)


### 运算符 ? :

**c ? a : b**

```javascript
var val = true ? 1 : 2; // val = 1
```
### 运算符 ,

**a, b**
```javascript
var val = (1, 2, 3); // val = 3
```

### 运算符 delete

**delete obj.x;**

```javascript
var obj = {
    x: 1
};
obj.x; // 1
delete obj.x;
obj.x;                  // undefined
```

```javascript
var obj = {};
Object.defineProperty(obj, 'x', {
    configurable: false,
    value: 1
});
delete obj.x; // false
obj.x;         // 1

```

### 运算符 in

```javascript
window.x = 1;
'x' in window; // true
```

### 运算符 instanceof, typeof

```javascript
{} instanceof Object                // true
typeof 100 === 'number' // true

```
### 运算符 new

```javascript
function Foo() {}
Foo.prototype.x = 1;
var obj = new Foo();
obj.x; // 1
obj.hasOwnProperty('x'); // false
obj.__proto__.hasOwnProperty('x'); // true
```

### 运算符 this

```javascript
this; // window (浏览器)
var obj = {
    func: function () {
        return this;
    }
};
obj.func(); // obj
```
### 运算符 void

```javascript
void 0  // undefined
void(0) // undefined
```


## 运算优先级

[JavaScript 所有运算符的优先级](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Operator_Precedence)

