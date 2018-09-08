---
layout: post
title: JavaScript 之对象「下」
categories: JavaScript
description: JavaScript 对象
keywords: JavaScript, Object
---

## 一、get/set 方法

在上一篇文章中介绍了属性的读写方式有 `.` 和 `[]` 操作符，

而 getter/setter 方法也读写属性的一种方式。

- get 语法将对象属性绑定到查询该属性时将被调用的函数。

- set 语法将对象属性绑定到要调用的函数。

### 1、属性 getter/setter 方法

get/set 方法和其他属性区别很大， 以 get/set 关键字开头，紧接着对应的属性的名字，后面是个函数体，get/set 方法与一般属性之间是用逗号隔开的。


在对象初始化时定义 getter/setter 方法，如下代码：

```javascript
var man = {
    name : 'QinBin',
    sex : 'male',
    get age() {
        return new Date().getFullYear() - 1999;
    },
    set age(val) {
        console.log('年龄不能设为' + val);
    }
}

console.log(man.name) // QinBin
console.log(man.age); // 19
man.age = 100; // 年龄不能设为 100
console.log(man.age); // 19
```

复杂点的用法：

`$age` 是一个特殊的 `key`，表示这个 key 不会暴露到外面。

`isNaN()` 函数用于检查其参数是否是非数字值。

`isNaN(x)`，如果 x 是特殊的非数字值 NaN（或者能被转换为这样的值），返回的值就是 true。如果 x 是其他值, 则返回 false。

```javascript
var man = {
    name: 'QinBin',
    $age: null,
    get age() {
        if (this.$age == undefined) {
            // $age 为 null 或者 undefined
            return new Date().getFullYear() - 1999;
        } else {
            return this.$age;
        }
    },
    set age(val) {
        // 如果 val 是字符串类型，将字符串隐式转换成数值
        val = +val;
        if (!isNaN(val) && val > 0 && val < 150) {
            this.$age = +val;
        } else {
            throw new Error('Incorrect val =' + val);
        }
    }
}

// 测试
console.log(man.age); // 19
man.age = 100;
console.log(man.age); // 100;
man.age = 150; // Error: Incorrect val = 150
man.age = 'abc'; // Error: Incorrect val = NaN
```

### 2、get/set 与原型链
get/set 方法与原型链结合的情况

`Object.defineProperty()` 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性， 并返回这个对象。

```javascript
// 1、创建一个空的 foo 函数，该函数有一个 foo.prototype 属性
function foo() {}

// 2、通过 Object.defineProperty() 方法给 foo 函数添加 z 属性和 get 方法
Object.defineProperty(foo.prototype, 'z', {
    get: function () {
        return 1;
    }
});

// 3、使用 new 创建一个新对象 obj，该对象的原型会指向构造器的 prototype 属性，也就是 foo.prototype 属性
var obj = new foo();

// 4、向上查找，找到一个 z 的 get 方法，返回 1
obj.z; // 1

// 5、没有给 obj 对象添加新属性 z，因为原型连上有 z 属性的 get/set 方法
obj.z = 10; // 10
obj.z; // 1
```
当 obj 对象上没有 z 属性，并且在原型链上查找的时候发现有 get/set 方法的时候，那么当我们尝试去赋值的时候，会尝试走 get 和 set 方法，而不会再去给 obj 添加新属性。而如果原型链上查找没有 get/set 方法，则会给 obj 对象添加新属性 z。

那么，如何在 obj 对象上添加 z 属性呢？

可以通过 `Object.defineProperty()` 方法给 obj 对象添加 z 属性。

```javascript
// 继续上面的 js 代码
// 6、给 obj 对象添加 z 属性，并设置 z 属性的值为 100，configurable 标签为 true（delete 操作需要）
Object.defineProperty(obj, 'z', {
    value: 100,
    configurable: true
});

// 7、调用 obj.z 返回 100，证明 obj 对象有 z 属性
obj.z; // 100;

// 8、删除 obj 对象的 z 属性
delete obj.z; // true

// 9、在原型链向上查找，返回原型链上的 z 属性
obj.z; // 1
```
对应的结构如下：

![Javascript-object-10](https://raw.githubusercontent.com/qinbin52qiul/MarkdownPhotos/master/Javascript/Javascript-Data-Type/Javascript-object-10.png)

访问对象原型链上 `writable` 为 `false` 的属性时也是不可写的，此时也需要用 `Object.defineProperty()` 方法来给对象添加属性 `writable`、`configurable` 并且设置为 `true`。

```javascript
// 1、定义一个空对象 o
var o = {};

// 2、给 o 对象定义属性 x，其他标签默认 false, 如：writable=false, configurable=false
Object.defineProperty(o, 'x', {
    value: 100
});

// 3、通过 Object.create 创建对象 obj，以 o 对象作为原型
var obj = Object.create(o);

// 4、访问 obj.x 返回 100，给其赋值之后还是 100，没有改变。
// 原因是原型链上的 x 属性是不可写的，所以不能修改 x 属性，也不能给 obj 对象添加新的属性 x
obj.x; // 100
obj.x = 200;
obj.x; // 还是 100

// 5、通过 Object.defineProperty() 方法给 obj 对象添加自己的 x 属性，并且设置为可写可删除，值为 100
Object.defineProperty(obj, 'x', {
    writable: true,
    configurable: true,
    value: 100
});

// 6、访问和修改 obj 对象自己的 x 属性
obj.x; // 100
obj.x = 500;
obj.x; // 500
```

## 二、属性标签

### 关于属性标签

属性标签的权限设置，有 4 个描述其属性行为的特性（默认都为 false）：

- **Writable**：表示对象的属性是否可写，为 `true` 才能对 `value` 重新赋值。

- **Enumerable**：表示对象的属性是否可以在 `for...in` 循环和 `Object.keys() ` 中被枚举。

- **Configurable**：表示对象的属性是否可以被删除，以及除 `writable` 特性外的其他特性是否可以被修改。

- **Value**：属性的值。可以是任何有效的 JavaScript 值（数值，对象，函数等）。默认 `undefined`。


### 查看对象属性标签

`Object.getOwnPropertyDescriptor()` 方法返回指定对象上一个自有属性对应的属性描述符。（自有属性指的是直接赋予该对象的属性，不需要从原型链上进行查找的属性）

> 如果是访问器属性，这个对象的属性有 configurable,enumerable,get 和 set；如果是数据属性，这个对象的属性有 configurable，enumerable，writable 和 value。

#### 语法

```javascript
Object.getOwnPropertyDescriptor(obj, prop)
```
`obj` 需要查找的目标对象

`prop` 目标对象内属性名称（String 类型）

#### 示例

**示例 2-1**

```javascript
// 第一个参数要判断的对象，第二个参数要查看属性名，返回一个对象
Object.getOwnPropertyDescriptor({pro: true}, 'pro');
// {value: true, writable: true, enumerable: true, configurable: true}

// 查看一个不存在属性，返回 undefined
Object.getOwnPropertyDescriptor({pro: true}, 'a');
// undefined
```
**示例 2-2**
```javascript
// 创建一个空对象
var person = {};
// 给 person 对象创建属性一个属性 name，值为'QinBin'
Object.defineProperty(person, 'name', {
    configurable: false,
    writable: false,
    enumerable: true,
    value: "QinBin"
});

// 测试
person.name; // QinBin
person.name = 1;
person.name; // QinBin，无法修改 person.name
delete person.name; // false

// 再给 person 对象创建新属性 type，值为'Object'
Object.defineProperty(person, 'type', {
    configurable: true,
    writable: true,
    enumerable: false,
    value: "Object"
});

// Object.keys(person) 获取对象上的所有的 key
// 查看 person 对象的 key
Object.keys(person); // ["name"]，type 属性是不可枚举，所以没有显示 type 属性。
```
**示例 2-3**

```javascript
// 通过 Object.defineProperties 一次创建多个属性
Object.defineProperties(person, {
    name: {
        value: 'QinBin',
        enumerable: true
    },
    language: {
        value: 'JavaScript',
        enumerable: true
    },
    salary: {
        value: 50000,
        enumerable: true,
        writable: true
    }
});

// 测试：查看属性标签的情况
Object.getOwnPropertyDescriptor(person, 'name');
// {value: "QinBin", writable: false, enumerable: true, configurable: false}
Object.getOwnPropertyDescriptor(person, 'salary');
// {value: 50000, writable: true, enumerable: true, configurable: false}
Object.getOwnPropertyDescriptor(person, 'language');
// {value: "JavaScript", writable: false, enumerable: true, configurable: false}
```
**示例 2-4**

```javascript
// 创建一个空对象
var person = {};
Object.defineProperties(person, {
    title: {
        value: 'QinBin',
        enumerable: true
    },
    language: {
        value: 'JavaScript',
        enumerable: true
    },
    salary: {
        value: 50000,
        enumerable: true,
        writable: true
    },
    luck: {  // 测试好运
        get: function () {
            return Math.random()> 0.5 ? 'good' : 'bad';
        }
    },
    promote: { // salary 提升
        set: function (level) {
            this.salary *= 1 + level * 0.1;
        }
    }
});

// 测试
Object.getOwnPropertyDescriptor(person, 'salary');
// {value: 50000, writable: true, enumerable: true, configurable: false}
Object.getOwnPropertyDescriptor(person, 'language');
// {value: "JavaScript", writable: false, enumerable: true, configurable: false}
person.salary; // 50000
person.promote = 2;
person.salary; // 60000
person.luck; // "good"
```
### configurable 和 writable

属性的标签可以通过 `Object.defineProperty()` 方法重复设置

![Javascript-object-11](https://raw.githubusercontent.com/qinbin52qiul/MarkdownPhotos/master/Javascript/Javascript-Data-Type/Javascript-object-11.png)

由上表可知，

如果 `writable` 值为 `false`，则不能修改属性的值。但是如果此 ` 时 configurable` 为 `true`，仍然可以通过 `Object.defineProperty()` 方法设置 `value` 来修改属性的值。或者 ` 把 writable` 改为 `true`，再修改属性值。所以 `configurable` 为 `true` 时想通过 `writable` 阻止属性的修改没有意义。

`configurable` 为 `false` 后：

1. 不允许 `delete` 属性；

2. 不允许修改 `getter/setter` 方法；

3. 不允许修改属性（标签特殊：允许将 `writable` 从 `true` 修改为 `false`）。

也就是说一旦把属性定义为不可配置的，就不能再把它变回可配置了。此时再调用 `Object.defineProperty()` 方法修改除 `writable` 之外的特性，都会报错。如下图

![Javascript-object-12](https://raw.githubusercontent.com/qinbin52qiul/MarkdownPhotos/master/Javascript/Javascript-Data-Type/Javascript-object-12.png)

## 三、对象标签

### [[proto]]

`Object.prototype.__proto__`  指向当对象被实例化的时候，用作原型的对象。


`Object.prototype` 的 `__proto__ ` 属性是一个访问器属性（一个 `getter` 函数和一个 `setter` 函数）, 暴露了通过它访问的对象的内部 `[[Prototype]]`  (一个对象或 `null`)。

![Javascript-object-13](https://raw.githubusercontent.com/qinbin52qiul/MarkdownPhotos/master/Javascript/Javascript-Data-Type/Javascript-object-13.png)

### [[class]]

`class` 标签没有一个直接的方式去查看它或者是修改它。

可以间接的通过 `Object.prototype.toString` 的方式去获取 `class`。

```javascript
Object.prototype.toString.call([1, 2]); //[object Array]
```

```javascript
// 方便后面调用 Object.prototype.toString 时代码简短一些
var toString = Object.prototype.toString;
// 参数 o 为传进来的对象的 this
function getType(o) {
    return toString.call(o).slice(8, -1);
};

// 测试
toString.call(null); // "[object Null]"
getType(null); // "Null"
getType(undefined); // "Undefined"
getType(1); // "Number"
// Object.prototype.toString 会把参数变为对象再处理
getType(new Number(1)); // "Number"
// 对象类型
typeof new Number(1); // "object"
getType(true); // "Boolean"
getType(new Boolean(true)); // "Boolean"
```

### [[extensible]]

`extensible` 对象标签表示对象是否可扩展，也就是说，对象是否可以继续添加属性。

通过 `Object.isExtensible` 方法判断对象是否可扩展，返回 `true` 证明可扩展

```javascript
var obj = {x: 1, y: 2};
Object.isExtensible(obj); // true
```
可以通过 `Object.preventExtensions` 来阻止对象继续添加新的属性 对象上的属性标签没变化，
如下

```javascript
// 继续上面代码
Object.preventExtensions(obj);
Object.isExtensible(obj); // false
// 不让扩展后，继续添加新属性 z 并访问，结果为 undefined
obj.z = 1;
obj.z; // undefined
// 查看对象属性 x 的属性标签
Object.getOwnPropertyDescriptor(obj, 'x');
// {value: 1, writable: true, enumerable: true, configurable: true}
// 表明属性仍然可以删除掉 修改和枚举
```
阻止对象的扩展的同时，禁止修改属性标签，禁止删除属性。

通过 `Object.seal()` 方法，可以把对象上的所有属性的 `configurable` 设置为 `false`。

在 `Object.preventExtensions()` 的基础上增加一条限制，如下

```javascript
// 继续上面代码
Object.seal(obj);
Object.getOwnPropertyDescriptor(obj, 'x');
// Object {value: 1, writable: true, enumerable: true, configurable: false}
```
通过 `Object.isSealed()` 方法来判断一个对象是否被隐藏。

```javascript
// 继续上面代码
Object.isSealed(obj); // true
```
通过 `Object.freeze()` 可以将对象变为不可添加新属性，不可配置属性标签，不删除属性，不可对属性进行写操作。

```javascript
// 继续上面代码
Object.freeze(obj);
Object.getOwnPropertyDescriptor(obj, 'x');
// Object {value: 1, writable: false, enumerable: true, configurable: false}
```
通过 `Object.isFrozen()` 判断对象是否被冻结

```javascript
// 继续上面代码
Object.isFrozen(obj); // true
```
> PS：Object.preventExtensions、Object.seal、Object.freeze 只对一个对象有效，不会影响对象的原型链。

## 四、对象序列化

前端 javascript 处理一些数据或者处理的结果发给后端，可能后端需要接收字符串的数据格式，这个时候就需要对数据进行序列化了。

对象的序列化可以使用 `JSON.stringify()` 方法，该方法返回一个表示给定值的 JSON 字符串。

`JSON.stringify()` 方法是将一个 JavaScript 值 (对象或者数组) 转换为一个 JSON 字符串，如果指定了 replacer 是一个函数，则可以替换值，或者如果指定了 replacer 是一个数组，可选的仅包括指定的属性。

### 语法

```javascript
JSON.stringify(value[, replacer [, space]])
```
`value`　将要序列化成 一个 JSON 字符串的值。

`replacer`  （可选）
如果该参数是一个函数，则在序列化过程中，被序列化的值的每个属性都会经过该函数的转换和处理；如果该参数是一个数组，则只有包含在这个数组中的属性名才会被序列化到最终的 JSON 字符串中；如果该参数为 null 或者未提供，则对象所有的属性都会被序列化；

`space` （可选）
指定缩进用的空白字符串，用于美化输出（pretty-print）；如果参数是个数字，它代表有多少的空格；上限为 10。该值若小于 1，则意味着没有空格；如果该参数为字符串 (字符串的前十个字母)，该字符串将被作为空格；如果该参数没有提供（或者为 null）将没有空格。

### 示例

**示例 4-1**

```javascript
var obj = {
    x: 1,
    y: true,
    z: [1, 2, 3],
    nullVal: null
};
JSON.stringify(obj); // "{"x":1,"y":true,"z":[1,2,3],"nullVal":null}"
```
**示例 4-2**

序列化的一些注意点：

- 如果属性的值是 `undefined`，不会出现在序列化的结果当中。
- 如果属性的值是 `NaN` 或者是 `Infinity` 的话，那么会转化为 `null`。
- 如果属性的值是日期类型的话会转化为 UTC 的格式。

比如如下代码：

```javascript
obj = {
    val: undefined,
    a: NaN,
    b: Infinity,
    c: new Date()
};
JSON.stringify(obj); // "{"a":null,"b":null,"c":"2018-09-07T13:04:35.142Z"}"
```
**示例 4-3**

通过 `JSON.pars()` 方法将 JSON 数据转换成 JavaScript 对象。

```javascript
obj = JSON.parse('{"x": 1}');
obj.x; // 1
```
**示例 4-4**

自定义序列化

```javascript
var obj = {
    x: 1,
    y: 2,
    // o 属性的值是个对象
    o: {
        o1: 1,
        o2: 2,
        // 自定义序列化结果，toJSON 方法
        toJSON: function () {
            // this 指向 obj 对象的 o 属性
            return this.o1 + this.o2;
        }
    }
};
JSON.stringify(obj); // "{"x":1,"y":2,"o":3}"
```

## 五、其它对象方法

### 1、toString()

调用对象的 `toString()` 方法默认返回 `[object Object]`

```javascript
var obj = {x: 1, y: 2};
obj.toString(); // "[object Object]"
```
自定义 `toString` 方法

```javascript
// 字符串拼接和一元运算符'+'这两种情况都会调用该自定义 toString 方法
obj.toString = function () {
    return this.x + this.y
};
"Result" + obj; // "Result 3"

+obj; // 3
```

### 2、valueOf()

自定义如下 `valueOf` 方法

尝试把对象转换为基本类型和一元运算符'+'时会自动去调该自定义 `valueOf` 方法。

```javascript
obj.valueOf = function () {
    return this.x + this.y + 100;
};
+obj; // 103

"Result" + obj; // "Result 3"
```

> PS：
>
>当 toString 和 valueOf 都存在的时候，不管是一元的加号 (+) 还是二元的作为字符串拼接的的加号(+)，在做具体操作的时候都会尝试把对象转化为基本类型。
>
> 在进行如上操作时，会先去找 valueOf，如果 valueOf 返回的是基本类型的话，就以 valueOf 的值作为结果，反之如果 valueOf 不存在或者返回的是对象这样一个不合法的值，就会去找 toString。如果 toString 和 valueOf 都没有，或者都返回了对象，那么就会报错。

## 六、JS 对象总结

- 对象的结构
- 创建对象
- 属性操作
- getter setter
- 属性标签
- 对象标签
- 序列化
- 对象方法


## 七、参考文档

[JSON.stringify()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)

