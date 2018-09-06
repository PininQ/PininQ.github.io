---
layout: post
title: JavaScript 之严格模式
categories: JavaScript
description: JavaScript 严格模式
keywords: JavaScript, Statement
---
## 严格模式

严格模式是一种特殊的执行模式，它修复了部分语言上的不足，提供更强的错误检查，并增强安全性。

`ECMAScript 5` 的严格模式是采用具有限制性 JavaScript 变体的一种方式，从而使代码显示地 脱离 “马虎模式 / 稀松模式 / 懒散模式 “（sloppy）模式。
严格模式不仅仅是一个子集：它的产生是为了形成与正常代码不同的语义。

严格模式对正常的 JavaScript 语义做了一些更改。

- 严格模式通过抛出错误来消除了一些原有静默错误。

- 严格模式修复了一些导致 JavaScript 引擎难以执行优化的缺陷：有时候，相同的代码，严格模式可以比非严格模式下运行得更快。

- 严格模式禁用了在 ECMAScript 的未来版本中可能会定义的一些语法。

**! function(){}()**

解释器在解释一个语句时，如果以 `function` 开头，则是函数声明。

如果在前面加 `!` 可以让解释器理解为函数表达式，从而实现立即调用。

```javascript
! function () {
    var a = 2
    console.log(a);
}();
// 2
```
如果 function 前缺少 `!`，

```javascript
function () {
    var a = 2
    console.log(a);
}();
// 报错：Uncaught SyntaxError: Unexpected token (
```
就会理解为函数声明，而函数声明是不能立即调用的，函数声明会被前置（可以在函数声明之前对函数进行调用），被提前解析了，相当于只留下一对括号，没有函数名，所以会报错。

### 1. 严格模式的开启方式

```javascript
// 方式一：函数级别严格模式语法
function func() {
    'use strict';
}
// ----------------- 分割线 -------------------
// 方式二：在所有语句之前添加 "use strict"，为整个脚本都开启严格模式的语法
 'use strict';
function func() {

}
```
### 2. 严格模式下不允许用 with

```javascript
// 1、非严格模式
!function() {
	 with({x : 1}) {
           console.log(x);
      }
}();
// 结果为 1
// ----------------- 分割线 -------------------
// 2、严格模式
!function() {
	'use strict';
	 with({x : 1}) {
            console.log(x);
      }
}();
// 结果报错
// Uncaught SyntaxError: Strict mode code may not include a with statement
```
### 3. 严格模式下不允许未声明的变量被赋值

```javascript
// 1、非严格模式
! function () {
    // 相当于声明了一个全局变量
    a = 1;
    console.log(window.x);
}();
// 结果为 1
// ----------------- 分割线 -------------------
// 2、严格模式
! function () {
    'use strict';
    // 给没有声明过的变量 a 赋值，在严格模式下会报错。
    a = 1;
    console.log(window.x);
}();
// 结果报错
// Uncaught ReferenceError: a is not defined
```

### 4. arguments 变为参数的静态副本

```javascript
// 1、一般模式
! function (a) {
    // a 和 arguments[0] 有相互绑定的关系，所以修改 arguments[0]，对应的形参 a 也会被修改
    arguments[0] = 100;
    console.log(a);
}(1);
// 结果为 100
// 特殊：如果不传参数，a 实际上为 undefined，执行 arguments[0] = 100 后，a 仍然为 undefined，不会受 arguments 所影响

// ----------------- 分割线 -------------------

// 2、严格模式
! function (a) {
    'use strict';
    // arguments 变为参数的静态副本，所以 a 和 arguments[0] 没有相互的绑定关系，不会相互影响
    arguments[0] = 100;
    console.log(a);
}(1);
// 结果为 1

// ----------------- 分割线 -------------------

// 3、严格模式，参数是对象
! function (a) {
    'use strict';
    // 如果传入的参数是对象，修改对象的属性，a 和 arguments[0] 还是会相互影响。
    arguments[0].x = 100;
    console.log(a.x);
}({
    x: 1
});
// 结果为 100
```
### 5. delete 参数、函数名报错


```javascript
// 1、一般模式
!function(a) {
    // 视图去删除参数或者函数名
	console.log(delete a);
}(1);
// 结果为 false

// ----------------- 分割线 -------------------

// 2、严格模式
! function (a) {
    'use strict';
    delete a;
}(1);
// 结果报错
// Uncaught SyntaxError: Delete of an unqualified identifier in strict mode.
```

### 6. delete 不可配置的属性报错

```javascript
// 1、一般模式
! function (a) {
    var obj = {};
    // 定义一个带有属性描述器的属性
    Object.defineProperty(obj,
        'a', {
            // 设置 configurable 标签为 false
            configurable: false
        });
    // 返回 false，并且删除失败
    console.log(delete obj.a);
}(1);
// 结果为 false

// ----------------- 分割线 -------------------

// 2、严格模式
! function (a) {
    'use strict';
    var obj = {};
    Object.defineProperty(obj,
        'a', {
            configurable: false
        });
    delete obj.a;
}(1);
// 结果报错
// Uncaught TypeError: Cannot delete property 'a' of #<Object>
```

### 7. 对象字面量重复属性名报错

```javascript
// 1、一般模式
! function () {
    // 定义了重复的属性名，属性值以最后一个为准
    var obj = {
        x: 1,
        x: 2
    };
    console.log(obj.x);
}();
// 结果为 2

// ----------------- 分割线 -------------------

// 2、严格模式
! function () {
    'use strict';
    var obj = {
        x: 1,
        x: 2
    };
}();
// 没报错，结果为 2
```
### 8. 禁止八进制字面量
```javascript
// 1、一般模式
! function () {
    console.log(0123);
}();
// 结果为 83

// ----------------- 分割线 -------------------

// 2、严格模式
! function () {
    'use strict';
    console.log(0123);
}();
// 结果报错
// Uncaught SyntaxError: Octal literals are not allowed in strict mode.
```
### 9. eval, arguments 变为关键字，不能作为变量、函数名

```javascript
// 1、一般模式
! function () {
    function eval() {}
    console.log(eval);
}();
// 结果为 function eval(){}

// ----------------- 分割线 -------------------

// 2、严格模式
! function () {
    'use strict';

    function eval() {}
}();
// 结果报错
// Uncaught SyntaxError: Unexpected eval or arguments in strict mode
```

### 10. 严格模式下 eval 变成独立作用域

```javascript
// 1、一般模式
! function () {
    // eval 中定义一个变量
    eval('var evalVal = 2;');
    // 在 eval 所在的函数内部仍然可以拿到 eval 中定义的变量 evalVal
    console.log(typeof evalVal);
}();
// 结果为 number

// ----------------- 分割线 -------------------

// 2、严格模式
! function () {
    'use strict';
    eval('var evalVal = 2;');
    // 在严格模式下, eval() 中的代码会在一个独立的作用域中执行，所以拿不到 evalVal
    console.log(typeof evalVal);
}();
// 结果为 undefined
```

## 总结

严格模式下的区别

1. 不允许用 with

1. 所有变量必须声明, 赋值给为声明的变量报错，而不是隐式创建全局变量。

1. eval 中的代码不能创建 eval 所在作用域下的变量、函数。而是为 eval 单独创建一个作用域，并在 eval 返回时丢弃。

1. 函数中得特殊对象 arguments 是静态副本，而不像非严格模式那样，修改 arguments 或修改参数变量会相互影响。

1. 删除 configurable=false 的属性时报错，而不是忽略

1. 禁止八进制字面量，如 010 (八进制的 8)

1. eval, arguments 变为关键字，不可作为变量名、函数名等

1. 一般函数调用时 (不是对象的方法调用，也不使用 apply/call/bind 等修改 this)this 指向 null，而不是全局对象。

1. 若使用 apply/call，当传入 null 或 undefined 时，this 将指向 null 或 undefined，而不是全局对象。

1. 试图修改不可写属性 (writable=false)，在不可扩展的对象上添加属性时报 TypeError，而不是忽略。

1. arguments.caller, arguments.callee 被禁用
