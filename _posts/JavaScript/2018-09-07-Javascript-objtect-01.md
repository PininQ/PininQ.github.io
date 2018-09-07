---
layout: post
title: JavaScript 之对象「上」
categories: JavaScript
description: JavaScript 对象
keywords: JavaScript, Object
---

## 一、对象概述

在 JavaScript 中，所有事物都是对象：字符串、数值、数组、函数...

JavaScript 提供多个内建对象，比如 String、Date、Array 等等。 对象只是带有属性和方法的特殊数据类型。

- 布尔型可以是一个对象。
- 数字型可以是一个对象。
- 字符串也可以是一个对象
- 日期是一个对象
- 数学和正则表达式也是对象
- 数组是一个对象
- 甚至函数也可以是对象

而对象中包含一系列的属性，这些属性是 **无序** 的。

每一个属性都有一个 **字符串** key 和对应的 value。

```javascript
var obj = {x : 1, y : 2};
obj.x; // 访问 obj 对应的属性 x，1
obj.y; // 访问 obj 对应的属性 y，2
```
> 对象属性的 key 必须是字符串。

关于对象属性的 key，可以看看如下例子：

```javascript
// 定义一个对象 obj
var obj = {};
obj[1] = 1;
obj['1'] = 2;
obj[2] = 333;
// 通过数字 1 和字符串 1 访问的结果是一样的
obj;  // 结果为 {1: 2, 2: 333}

// 使用空对象 {} 作为 key
obj[{}] = true;
// 使用带有 x 属性的对象作为 key，会被 js 通过调用 toString() 转换成字符串，所以最终是指向同一个属性 '1'。
obj[{x : 1}] = true;
obj; // 结果为 {1: 2, 2: 3, [object Object]: true}
```

### 对象的结构

#### 1、对象属性的结构
- 对象的属性可以动态添加和删除
- 对象的每个属性都有很多的属性标签

属性标签可以为每个属性提供访问权限的控制，比如是否可写 (writable)，是否可以被删除掉 (configurable)，是否可枚举 (enumerable)。

```javascript
var obj = {};
obj.y = 2;
obj.x = 1;
```
以上代码对应的属性的结构：

![Javascript-object-01](https://raw.githubusercontent.com/qinbin52qiul/MarkdownPhotos/master/Javascript/Javascript-Data-Type/Javascript-object-01.png)

#### 2、对象的原型

每个对象都有一个原型 `[[proto]]`

每一个函数都有一个对象属性 `prototype`
```javascript
function foo(){}
// 给 foo 函数对象属性 prototype
foo.prototype.z = 3;
// obj 对象的原型会指向构造器的原型
var obj =new foo();
```
上面这段代码对象 obj 的结构如下：

![Javascript-object-02](https://raw.githubusercontent.com/qinbin52qiul/MarkdownPhotos/master/Javascript/Javascript-Data-Type/Javascript-object-02.png)

#### 3、对象的标签

对象除了 `[[proto]]` 标签还有 `[[class]]` 标签和 `[[extensible]]` 标签。

`[[class]]` 标签标示对象是属于哪个种类的。

`[[extensible]]` 标签标示对象是否允许增加新的属性。

一个对象完整的结构如下：

![Javascript-object-09](https://raw.githubusercontent.com/qinbin52qiul/MarkdownPhotos/master/Javascript/Javascript-Data-Type/Javascript-object-09.png)

## 二、创建对象和原型链

### 1、对象创建 - 字面量
使用花括号括起来设置属性
```javascript
var obj1 = {
    x: 1,
    y: 2
};
```
对象字面量也可以做一些对象的嵌套。
```javascript
var obj2 = {
    x: 1,
    y: 2,
    o: {
        z: 3,
        n: 4
    }
};
```
### 2、对象创建 - new / 原型链

通过 function foo 定义一个函数对象，函数对象 foo 默认带一个 **prototype** 属性。**prototype** 属性是一个对象的属性。

```javascript
function foo(){}
foo.prototype.z = 3;
```

![Javascript-object-03](https://raw.githubusercontent.com/qinbin52qiul/MarkdownPhotos/master/Javascript/Javascript-Data-Type/Javascript-object-03.png)

使用 new 构造一个新的对象 obj，添加两个属性并赋值。

```javascript
var obj =new foo();
obj.y = 2;
obj.x = 1;
```
使用 new 来构造对象，它的原型会指向构造器的 **prototype** 属性，也就是 **foo.prototype**。

![Javascript-object-04](https://raw.githubusercontent.com/qinbin52qiul/MarkdownPhotos/master/Javascript/Javascript-Data-Type/Javascript-object-04.png)


```javascript
function foo(){}
foo.prototype.z = 3;

var obj =new foo();
obj.y = 2;
obj.x = 1;

obj.x; // 1
obj.y; // 2
obj.z; // 3
typeof obj.toString; // 'function', 原型链的末端有一个 Object.prototype
'z' in obj; // true, 在对象 obj 的原型链上找到'z'
obj.hasOwnProperty('z'); // false, 对象 obj 没有属性'z'
```

![Javascript-object-05](https://raw.githubusercontent.com/qinbin52qiul/MarkdownPhotos/master/Javascript/Javascript-Data-Type/Javascript-object-05.png)

作用：通过原型链继承，继承原型上的属性和方法。

当访问一个属性，这个对象上没有该属性，会通过原型链向上查找，一直找到 `null`，如果还没找到就返回 `undefined`。

> PS：obj.z 返回 undefined，不一定是没有 z 属性，也可能是 obj.z=undefined。也可能是向上查找原型链没找到返回 undefined。一个对象是否有某个属性，可以通过 in 或者 hasOwnProperty 方法来区分。如果这个时候想要拿到原型链上的 z 属性，则需要执行 `delete obj.z` 删除对象的 z 属性，在执行 obj.z 才能调用原型链上的 z 属性。

### 3、对象创建 - Object.create

`Object.create` 是一个系统内置的函数，这个函数会接受一个参数，一般是一个对象，它会返回新创建的对象，并且让这个对象的原型指向这个对象。

```javascript
var obj = Object.create({
    x: 1
});
obj.x // 1
typeof obj.toString // "function"
obj.hasOwnProperty('x'); // false
```
对应的结构为：

![Javascript-object-06](https://raw.githubusercontent.com/qinbin52qiul/MarkdownPhotos/master/Javascript/Javascript-Data-Type/Javascript-object-06.png)

使用 `字面量` 创建的对象的原型指向 `Object.prototype`，所以在 `obj` 对象上可以调用 `obj.toString()` 方法。

由于 `x` 属性是从原型链上继承来的，并不是 `obj` 对象本身的属性，所以 `obj.hasOwnProperty('x')` 返回 false。

然而，并不是所有的对象都有 `toString()` 方法，因为并不是所有的对象的原型链上都有 `Object.prototype`。

比如下面的代码中，在使用 `Object.create(null)` 创建一个对象的时候传入一个 `null`，它的原型链就直接指向 `null`，而 `null` 没有方法，因而创建的 obj 对象也就没有 `toString()` 方法了。

```javascript
var obj = Object.create(null);
obj.toString // undefined
```
对应的结构为：

![Javascript-object-07](https://raw.githubusercontent.com/qinbin52qiul/MarkdownPhotos/master/Javascript/Javascript-Data-Type/Javascript-object-07.png)

## 三、属性操作

### 1、属性读写

属性的读写可以通过 `.` 或者 `[]` 操作符。

```javascript
var obj = {
    x: 1,
    y: 2
};
obj.x; // 1
obj["y"]; // 2

obj["x"] = 3;
obj.y = 4;
```
通常情况下，使用 `.` 操作符更好一点，有些编辑器可以自动检查。

需要循环遍历某一个对象的属性的时候，可以使用 `[]`，在其中拼接属性名，比如：

```javascript
var obj = {
    x1: 1,
    x2: 2
};
var i = 1,
    n = 2;

for (; i <= n; i++) {
    console.log(obj['x' + i]);
}
// 输出: 1, 2
```
还可以使用 `for...in` 遍历对象的属性名。但是 `for...in` 有可能把原型链上的属性也一起遍历出来，并且遍历结果是无序的。

```javascript
var p;
for (p in obj) {
    console.log(obj[p]);
}
```
![Javascript-object-08](https://raw.githubusercontent.com/qinbin52qiul/MarkdownPhotos/master/Javascript/Javascript-Data-Type/Javascript-object-08.png)

### 2、属性读写 - 异常


```javascript
var obj = {
    x: 1
};
obj.y; // undefined
var yz = obj.y.z; // 读异常，Uncaught TypeError: Cannot read property 'z' of undefined
obj.y.z = 2; // 写异常，Uncaught TypeError: Cannot set property 'z' of undefined
```
obj.y 会查找原型链，一直找到末端是 `null` 还是没有的话就返回 `undefined`。

obj.y.z，obj.y 不存在是 undefined，再去访问或者写入 z，就会报错不能 read 或者 set 一个 undefined 属性 z。

在读取某一个属性的时候，可以先判断对象是否为空。

```javascript
var yz;
if (obj.y) {
    yz = obj.y.z;
}
```

也可以利用巧妙利用运算符的规则，实现链式读取：

```javascript
var yz = obj && obj.y && obj.y.z;
```

### 3、删除属性

（1）删除对象属性

```javascript
var person = {age : 28, title : 'fe'};
delete person.age; // true
delete person['title']; // true
person.age; // undefined
// 删除对象上已经不存在的属性也是返回 true。
// 所以删除一个对象并返回 true 并不能说明 delete 生效或者成功了，只代表该对象上不存在要 delete 的这个属性。
delete person.age; // true
```
（2）删除 Object 属性
```javascript
// Object.prototype 不允许删除
delete Object.prototype; // false,

// getOwnPropertyDescriptor 获取属性中的所有标签，第一个参数是要查看的对象，第二个参数是需要检测的属性
var descriptor = Object.getOwnPropertyDescriptor(Object, 'prototype');
// configurable 属性标签查看是否可配置，所以 delete Object.prototype; 时返回 false 并且不生效。
descriptor.configurable; // false
```
（3）删除 var 声明的全局变量和局部变量
```javascript
var globalVal = 1;
delete globalVal; // false

(function () {
    var localVal = 1;
    return delete localVal;
}()); // false
```
（4）删除全局函数和局部作用域的函数
```javascript
function fd() {}
delete fd; // false

(function () {
    function fd() {};
    return delete fd;
}()); // false
```
（5）删除隐式的创建全局变量
```javascript
ohNo = 1;
window.ohNo; // 1
delete ohNo; // true
```
**小结**

- 对象属性和隐式创建的全局变量可以被删除。

- Object 属性，var 声明的全局变量和局部变量，全局函数和局部函数都是不可被删除的。

### 4、属性检测

```javascript
// 定义一个对象，并添加属性
var dog = new Object;
dog.bones = 4;
dog.name = "kai";

// 检测对象是否有对应属性，in 操作符会向原型链向上查看是否有该属性，有则返回 true，相反返回 false
'bones' in dog; // true
'abc' in dog; // false
"toString" in dog; // true, 原型链上的属性

// 查看对象是否有对应属性
dog.hasOwnProperty('bones'); // true
dog.hasOwnProperty('toString'); // false

// 查看属性是否可枚举
dog.propertyIsEnumerable('bones'); // true
dog.propertyIsEnumerable('toString'); // false
```
自定义一个不可枚举的属性

```javascript
// Object.defineProperty 创建的对象默认所有标签都是 false
// 第一个参数属性对象，第二个属性名字，第三个对象
Object.defineProperty(dog, 'price', {
    enumerable: false,  // false，表示不可枚举
    value: 1000
});
dog.propertyIsEnumerable('price'); // false
dog.hasOwnProperty('price'); // true，是 dog 的属性
```

> PS：Object.defineProperty 创建的对象默认所有标签 (enumerable, writable, configurable) 都是 false。

判断属性
```javascript
if (dog && dog.bones) {
    dog.bones *= 2;
}

if (dog.bones != undefined) {
    // dog.bones 不等于 undefined 或 null 时执行 if 中的语句

}


if (dog.bones !== undefined) {
    // dog.bones 不等于 undefined 时才执行 if 中的语句

}
```

### 5、属性枚举


```javascript
var o = {
    x: 1,
    y: 2,
    z: 3
};
'toString' in o; // true
o.propertyIsEnumerable('toString'); // false
var key;
for (key in o) {
    console.log(key); // x, y, z
}
```

```javascript
// obj 对象的原型指向 o 对象
var obj = Object.create(o);
obj.a = 4;
var key;
for (key in obj) {
    // 原型链上的属性也一起打印
    console.log(key); // a, x, y, z
}

var obj = Object.create(o);
obj.a = 4;
var key;
for (key in obj) {
    if (obj.hasOwnProperty(key)) {
        // 只打印 obj 对象上的属性
        console.log(key); // a
    }
}
```
遍历数组可以用 forEach 方法，

也可以用 Object.keys() 来获取对象上的所有 key。

