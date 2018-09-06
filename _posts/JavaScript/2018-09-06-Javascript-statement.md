---
layout: post
title: JavaScript 之语句
categories: JavaScript
description: JavaScript 语句
keywords: JavaScript, Statement
---

JavaScript程序由语句组成，语句遵守特定的语法规则。

例如：if语句, while语句, with语句等等。

## JavaScript 常见语句

语句 |语句|语句
---|---|---
try catch		|switch			|debugger
break       |	if...else|label
continue	|	while	|with
return		|do...while	|empty
function	|	for		|block
var|for...in	|

### 块 block

块语句常用于组合0 ~ 多个语句。块语句用一对花括号定义。

#### 语法

```javascript
// 块声明
{
    语句1;
    语句2;
    ...
    语句n;
}
```
#### 示例

```javascript
{
    var str = "hi";
    console.log(str);
}
```

```javascript
if (true) {
    console.log('hi');
}
```

```javascript
{
    a: 1,
    b: 2
} // {a: 1, b: 2}

var o = {
    a: 1,
    b: 2
}; // o = {a: 1, b: 2}
```

JavaScript 中没有块级作用域。在块里定义变量跟在块外面定义的一样。以下两种方式是等价的
```javascript
{
    var x = 1;
    // .....
}
// -----------------分割线-------------------
var x = 1;
{
    // .....
}
```
以下两种方式是等价的，在for循环结束后，在for循环外面依然可以拿到i的值
```javascript
// 方式一
for (var i = 0; i < 10; i++) {
    var str = "hi";
    console.log(str);
}
// -----------------分割线-------------------
// 方式二
var i = 0
for (; i < 10; i++) {
    var str = "hi";
    console.log(str);
}
```
函数作用域：在函数里面定义的变量，在函数外拿不到。
```javascript
function foo() {
    var a = 1;
    console.log(a); // 1
}
foo();
console.log(typeof a); // undefined
```

### var 语句

var 是声明变量语句。

#### 语法


```javascript
var varname1 [= value1 [, varname2 [, varname3 ... [, varnameN]]]];
```
`varname1...N`  变量名。变量名可以定义为任何合法标识符。

`value1...N`  变量的初始化值。该值可以为任何合法表达式。

#### 示例
声明一个变量a并赋值为1。
```javascript
var a = 1;
```
一个var中定义多个变量，如下方式中，b 隐式的创建了全局变量，在函数外还能访问。

```javascript
function foo() {
    // 其中 b 隐式的创建了全局变量
    var a = b = 1;
}
foo();

console.log(typeof a); // 'undefined'，只能在 foo 函数内使用
console.log(typeof b); // 'number'，还能在 foo 函数外调用
```
如下方式，a和b两个变量都只能在foo函数内使用。
```javascript
function foo() {
    // 局部变量 a和b在foo函数外无法调用
    var a = 1, b = 1;
}
foo();

console.log(typeof a); // 'undefined'，foo函数外不能调用
console.log(typeof b); // 'undefined'，foo函数外不能调用
```
### try catch 语句


`try`语句包含了由一个或者多个语句组成的`try`块， 和至少一个`catch`子句或者一个`finally`子句的其中一个，或者两个兼有。

`try`的三种声明方式：

1. try...catch
2. try...finally

3. try...catch...finally

#### 语法

```javascript
try {
   try_statements
}
[catch (exception_var_1 if condition_1) { // non-standard
   catch_statements_1
}]
...
[catch (exception_var_2) {
   catch_statements_2
}]
[finally {
   finally_statements
}]
```
`try_statements`  需要被执行的语句。

`catch_statements_1, catch_statements_2`  如果在try块里有异常被抛出时执行的语句。

`exception_var_1, exception_var_2`   用于保存关联catch子句的异常对象的标识符。
condition_1
一个条件表达式。

`finally_statements`  在try语句块之后执行的语句块。无论是否有异常抛出或捕获这些语句都将执行。

#### 示例

```javascript
try {
   throw "test";
} catch (ex) {
    console.log(ex); // test
} finally {
    console.log('finally');
}
```

```javascript
try {
    try {
        throw new Error("oops");  // 抛出异常
    } finally {
        console.log("finally");   // 执行 finally
    }
} catch (e) {  // 外部 catch 捕获异常
    console.error("outer", e.message);  // 打印报错信息
}

// 上面try...catch语句执行结果
"finally"
"outer oops"
```

```javascript
try {
    try {
        throw new Error("oops");  // 抛出异常
    } catch (e) {   // 内部 catch 捕获异常
        console.error("inner", e.message);
    } finally {
        console.log("finally");  // 执行 finally
    }
} catch (e) {   // 异常已经被内部catch捕获，这里没有异常可以捕获
    console.error("outer", e.message);
}

// 上面try...catch语句执行结果
"inner oops"
"finally"
```

```javascript
try {
    try {
        throw new Error("oops");
    } catch (e) {
        console.error("inner", e.message);
        throw ex;
    } finally {
        console.log("finally");
    }
} catch (e) {
    console.error("outer", e.message);
}

// 上面try...catch语句执行结果
"inner oops"
"finally"
"outer oops"
```

### function 语句

一个被函数声明创建的函数是一个 函数对象「Function对象」。

默认情况下，函数是返回 `undefined` 的。想要返回一个其他的值，函数必须通过一个 return 语句指定返回值。

#### 语法

```javascript
function name([param,[, param,[..., param]]]) {
   [statements]
}
```
`name`  函数名

`param`  要传递给函数的参数的名称。不同引擎中的最大参数数量不同。

`statements`  包含函数体的语句。

#### 示例

JavaScript 中的函数声明被提升到了函数定义，所以可以在函数声明之前使用该函数。如下：

```javascript
// 使用function语句定义的函数对象叫做函数声明，函数声明会被预先处理。
fd(); // true

function fd() {
    // do sth.
    return true;
}
```
函数表达式function expressions 不会被提升，所以调用该函数会报 TypeError错误。

```javascript
// 函数表达式
fe(); // Uncaught TypeError: fe is not a function

var fe = function() {
    // do sth.
};
```
### for...in语句

`for...in`语句以任意顺序遍历一个对象的可枚举属性。

特点：

1. 遍历的顺序不确定，具体的顺序依赖引擎的实现；

2. `enumerable`为false时不会在`for...in`出现；

3. `for...in`对象属性会受原型链影响。

#### 语法

```javascript
for (variable in object) { 
    ...
}
```
`variable`  在每次迭代时，将不同的属性名分配给变量。

`object`  被迭代枚举其属性的对象。

#### 示例

```javascript
var p;
var obj = {
    x: 1,
    y: 2
}

for (p in obj) {
    console.log(obj[p])
}
```

### switch语句

switch语句，将表达式的值与case子句匹配，并执行匹配到的语句。

#### 语法

```javascript
switch (expression) {
  case value1:
    // 当 expression 的结果与 value1 匹配时，执行此处语句
    [break;]
  case value2:
    // 当 expression 的结果与 value2 匹配时，执行此处语句
    [break;]
  ...
  case valueN:
    // 当 expression 的结果与 valueN 匹配时，执行此处语句
    [break;]
  [default:
    // 如果 expression 与上面的 value 值都不匹配时，执行此处语句
    [break;]]
}
```
`expression`  一个用来与 case 子语句匹配的表达式。

`case valueN` `可选`  用于匹配 expression 的 case 子句。如果 expression 与给定的 valueN 相匹配，则执行该 case 子句中的语句直到该 switch 语句结束或遇到一个 break 。

`default` `可选`  一个 default 子句；如果给定，这条子句会在 expression 的值与任一 case 语句均不匹配时执行。

#### 示例

```javascript
var val = 2;

switch (val) {
    case 1:
        console.log(1);
        break;
    case 2:
        console.log(2);
        break;
    default:
        console.log(0);
        break;
}

// 结果：2
```

```javascript
var val = 2;

switch (val) {
    case 1:
        console.log(1);
    case 2:
        console.log(2);
    default:
        console.log(0);
}

// 结果：2, 0
```

```javascript
var val = 2;

switch(val) {
    case 1:
    case 2:
    case 3:
        console.log(123);
        break;
    case 4:
    case 5:
        console.log(45); 
        break;
    default:
        console.log(0);
}

// 结果：123
```

### 循环语句

`while`语句
```javascript
while(isTrue) {
    // do sth.
}
```
`do...while`语句
```javascript
do {
    // do sth.
} while (isTrue)
```
`for`语句
```javascript
var i;
for (i = 0; i < n; i++) {
    // do sth.
}
```
### with语句
`with语句` 扩展一个语句的作用域链。

`with`语句缺点：

- 让JS引擎优化更难
- 可读性差
- 可被变量定义代替
- 严格模式下被禁用

#### 示例
```javascript
with ({x : 1}) {
    console.log(x);
}
```

```javascript
with(document.forms[0]) {
    console.log(name.value);
}
```

```javascript
var form = document.forms[0];
console.log(form.name.value);
```
