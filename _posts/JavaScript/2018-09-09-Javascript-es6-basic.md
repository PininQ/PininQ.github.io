---
layout: post
title: ECMAScript6 基础语法
categories: JavaScript
description: ECMAScript6 基础语法
keywords: JavaScript, ECMAScript6
---

## 一、背景

jQuery 的语法偏向 ES3

现如今很多开源框架是使用 ES6 进行开发的，比如，vue、react 等框架的语法偏向 ES6

ES6 的好处，ES6 弥补了很多 ES3、ES5 功能上的不足，比如常量、作用域、对象代理、类、继承等等。

这些在 ES3 或者 ES5 中想实现比较困难，而 ES6 中直接对这些语法进行了封装。有了这些功能，就可以显著提高我们的开发效率了。


## 二、ES3、ES5、ES6 语法对比

下面对比一下 ES3、ES5、ES6 中一些语法的区别，分别对比一下它们的常量、作用域、箭头函数、默认参数、对象代理。


### 常量

#### 1. ES3 中没有常量的概念

#### 2. ES5 中常量的写法

```javascript
Object.defineProperty(window, "name", {
    value: 'QinBin',
    writable: false // 属性标签默认为 false
})

console.log(window.name) // 'QinBin'
```

#### 3. ES6 中常量的写法

```javascript
const NAME = 'QinBin'
console.log(NAME) // 'QinBin'

NAME = 'QB'
// Uncaught Error: Module build failed: SyntaxError: H:/webCode/es6-webpack/src/qinbin.js: "NAME" is read-only
```

### 作用域
#### 1. ES5 中的作用域

```javascript
const callbacks = []
// 变量 i 被提升，变为全局变量
for (var i = 0; i <= 2; i++) {
    callbacks[i] = function () {
        // 函数体内的 i 是对变量的引用，而不是对变量的值的引用，此时 i 的值为 3
        return i * 2
    }
}

console.table([
    callbacks[0](),
    callbacks[1](),
    callbacks[2](),
])
```
ES5 中有函数作用域，本例子中的 callbacks 内部 function 形成一个闭包，但闭包内并没有声明内部的 i 变量，因此 i 变量从 for 循环内声明的全局变量中取得，从全局变量中取得时的 i 是最终计算量。

结果如下：

![ES6-01](https://raw.githubusercontent.com/qinbin52qiul/MarkdownPhotos/master/Javascript/ES6/ES6-01.png)

#### 2. ES6 中的作用域

```javascript
const callbacks = []
for (let i = 0; i <= 2; i++) {
    callbacks[i] = function () {
        return i * 2
    }
}

console.table([
    callbacks[0](),
    callbacks[1](),
    callbacks[2](),
])
```
使用 `let` 声明的变量，它会有一个块作用域的概念，这个时候的闭包是取决于当前的作用域，
当前的块作用域是 `for` 循环之后的花括号 `{}`，它会把当前块作用域的值保存下来，
供后面的闭包使用。每循环一次就形成一个新的作用域。闭包使用的变量是闭包作用域里的变量。

结果如下：

![ES6-02](https://raw.githubusercontent.com/qinbin52qiul/MarkdownPhotos/master/Javascript/ES6/ES6-02.png)

#### 3. ES5 中的块作用域

在 ES5 中要保证一块代码所要执行的作用域问题是通过立即执行函数实现的。

```javascript
(function () {
    // 该作用域在函数体内
    const foo = function () {
        return 1
    }
    console.log("foo() === 1", foo() === 1) // foo() === 1 true
    ;
    ((function () {
        // 该作用域在另一个函数体内
        const foo = function () {
            return 2
        }
        console.log("foo() === 2", foo() === 2) // foo() === 2 true
    })())
})()
```

如果在一个作用域中定义了两个一样的常量，会报错并提示该常量是只读的（read-only）。
而上面的两个常量 const 一样没有报错是因为这两个 const 常量被两个立即执行函数给隔离开了。
它们处在不同的作用域中，所以并不会受影响。

#### 4. ES6 中的块作用域

ES6 中有块作用域，只需要一对花括号 `{}`，就可以解决块作用域的问题。

```javascript
// 块作用域
{
    function foo() {
        return 1
    }
    console.log("foo() === 1", foo() === 1) // foo() === 1 true
    // 新的块作用域
    {
        function foo() {
            return 2
        }
        console.log("foo() === 2", foo() === 2) // foo() === 2 true
    }
    console.log("foo() === 1", foo() === 1) // foo() === 1 true
}
```

### 箭头函数

箭头函数是 ES6 中的一个新的语法，

在 ES3 和 ES5 中定义一个函数，

如下代码

```javascript
function a() {

}
```

在 ES6 中，可以使用箭头函数来定义一个函数，

好处：
- 在写法上省去了 function；
- this 指向定义时的上下文（ES5 中，this 指向调用者）。

如下代码

```javascript
() => {

}
```
#### 1. ES3/ES5 中关于数组的遍历

```javascript
var evens = [1, 2, 3, 4, 5]
var odds = evens.map(function (v) {
    return v * v
})
console.log(evens) // [1, 2, 3, 4, 5]
console.log(odds) // [1, 4, 9, 16, 25]
```

#### 2. ES6 中关于数组的遍历

```javascript
let evens = [1, 2, 3, 4, 5]
let odds = evens.map(v => v * v)
console.log(evens) // [1, 2, 3, 4, 5]
console.log(odds) // [1, 4, 9, 16, 25]
```

#### 3. ES3/ES5 中的 this

this 的指向是该函数被调用的对象。也就是说，函数被执行的时候，这个 this 指向的是调用
该函数的对象。

```javascript
// 声明 factory 函数
var factory = function () {
    this.a = 'a'
    this.b = 'b'
    this.c = {
        a: 'a+',
        // 普通函数声明
        b: function () {
            return this.a
        }
    }
}

console.log(new factory().c.b()) // a+
```
上面代码中，` 函数 b` 是 `c` 调用的，`c` 本身是一个对象，所以 `this` 指向的是 `c`，而 `c` 中的 ` 属性 a` 的值是 `a+`，所以 `this.a` 是 `a+`

#### 4. ES6 中的 this

```javascript
var factory = function () {
    this.a = 'a'
    this.b = 'b'
    this.c = {
        a: 'a+',
        // 箭头函数声明
        b: () => {
            // 这个 this 的指向是定义时的指向
            return this.a
        }
    }
}

console.log(new factory().c.b()) // a
```
箭头函数中的 `this` 指向是定义时 `this` 的指向，`b` 在定义箭头函数时，`this` 指向的就是 factory 函数体中的 `this`，
`new factory().c.b()` 中调用了 b 对象，但是 b 对象中指向的是构造函数的实例，
这个实例就是 `new factory()`，而 `new factory()` 实例的属性 a 的值是 `a`，所以返回的 `this.a` 值为 `a`

#### 小结

箭头函数解决了 this 指向不明确，避免 this 在运行的时候 this 指向不确定的问题。


### 默认参数

#### 1. ES3/ES5 中默认参数写法


```javascript
// ES3/ES5 默认参数的写法
function f(x, y, z) {
    if (y === undefined) {
        y = 7;
    }
    if (z === undefined) {
        z = 42
    }
    return x + y + z
}

console.log(f(1)) // 50
console.log(f(1, 3)) // 46
```

可以看出，写法比较麻烦，如果参数很多的话这个默认参数的写法会相当繁琐。

#### 2. ES6 中默认参数写法

```javascript
// ES6 默认参数的写法
function f(x, y=7, z=42) {
    return x + y + z
}

console.log(f(1)) // 50
console.log(f(1, 3)) // 46
```
可见，简短了很多

#### 3. ES6 中默认参数写法

```javascript
// ES6 默认参数的写法 + 必要参数的检查
function checkParameter() {
    throw new Error('can\'t be empty')
}

function f(x = checkParameter(), y = 7, z = 42) {
    return x + y + z
}

console.log(f(1)) // 50
console.log(f(1, 3)) // 46
try {
    f()  // 参数 x 没有赋值
} catch (e) {
    console.log(e) // Error: can't be empty
} finally {

}
```
当 x 没有赋值，就会执行默认参数 checkParameter()，checkParameter() 是一个函数，
这个函数一致性，就抛出了一个异常，从而达到了检查参数为空的检查的效果。

#### 4. ES3/ES5 可变参数

```javascript
function f() {
    // 转成一个数组
    var a = Array.prototype.slice.call(arguments)
    var sum = 0
    a.forEach(function (item) {
        sum += item * 1
    })
    return sum
}

console.log(f(1, 2, 3)) // 6
console.log(f(1, 2, 3, 45)) // 51
```
arguments 是一个伪数组，指向的是当前函数的参数列表，
通过 arguments 来获取当前函数的参数列表，然后进行
循环的运算，实现可变参数的求和运算。

#### 5. ES6 实现可变参数

```javascript
function f(...a) {
    var sum = 0
    a.forEach(item => {
        sum += item * 1
    })
    return sum
}
console.log(f(1, 2, 3)) // 6
console.log(f(1, 2, 3, 45)) // 51
```
`...` 是一个扩展运算符，`...a` 表示可变参数的列表

#### 6. ES5 合并两个数组

```javascript
var params = ['hello', true, 7]
var other = [1, 2].concat(params)
console.log(other) // [1, 2, "hello", true, 7]
```

#### 7. ES6 利用扩展运算符合并数组


```javascript
var params = ['hello', true, 7]
var other = [1, 2, ...params]
console.log(other) // [1, 2, "hello", true, 7]
```

### 对象代理

#### 1. ES3/ES5 数据保护

```javascript
// 声明 Person 函数对象
var Person = function () {
    var data = {
        name: 'qinbin',
        sex: 'male',
        age: 15
    }
    this.get = function (key) {
        return data[key]
    }
    this.set = function (key, value) {
        if (key !== 'sex') {
            data[key] = value
        } else {
            throw new Error('can\'t be modified sex.')
        }
    }
}

// 声明一个实例
var person = new Person()
// 读取
console.table({
    name: person.get('name'),
    sex: person.get('sex'),
    age: person.get('age')
})
// 修改 name
person.set('name', 'QB')
console.table({
    name: person.get('name'),
    sex: person.get('sex'),
    age: person.get('age')
})
try {
    // 修改 sex
    person.set('sex', 'female')
    console.table({
        name: person.get('name'),
        sex: person.get('sex'),
        age: person.get('age')
    })
} catch (e) {
    console.log(e) // Error: can't be modified sex.
} finally {
    console.table({
        name: person.get('name'),
        sex: person.get('sex'),
        age: person.get('age')
    })
}
```

![ES6-03](https://raw.githubusercontent.com/qinbin52qiul/MarkdownPhotos/master/Javascript/ES6/ES6-03.png)

#### 2. ES5 数据保护

```javascript
// 声明一个对象
var Person = {
    name: 'qinbin',
    age: 15
}
// 给对象 Person 定义一个新的属性 sex 并设置不可写
Object.defineProperty(Person, 'sex', {
    writable: false, // 属性是否可写，默认 false
    value: 'male'
})

// 读取
console.table({
    name: Person.name,
    sex: Person.sex,
    age: Person.age
})
// 修改
Person.name = 'QB'
console.table({
    name: Person.name,
    sex: Person.sex,
    age: Person.age
})
try {
    Person.sex = 'female'
    console.table({
        name: Person.name,
        sex: Person.sex,
        age: Person.age
    })
} catch (e) {
    console.log(e) // Error: can't be modified sex.
} finally {
    console.table({
        name: Person.name,
        sex: Person.sex,
        age: Person.age
    })
}
```

![ES6-04](https://raw.githubusercontent.com/qinbin52qiul/MarkdownPhotos/master/Javascript/ES6/ES6-04.png)


#### 3. ES6 对象代理

Proxy 用于修改某些操作的默认行为，等同于在语言层面做出修改，所以属于一种 “元编程”（meta programming），即对编程语言进行编程。

Proxy 可以理解成，在目标对象之前架设一层 “拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。Proxy 这个词的原意是代理，用在这里表示由它来“代理” 某些操作，可以译为“代理器”。

```javascript
// ES6
let Person = {
    name: 'es6',
    sex: 'male',
    age: 15
};

// 代理 Person，通过代理操作 Person 中的数据
let person = new Proxy(Person, {
    // get 为读操作，target 为代理的数据，key 为读取的属性
    get(target, key) {
        return target[key]
    },
    set(target, key, value) {
        if (key !== 'sex') {
            target[key] = value;
        }
    }
});

console.table({
    name: person.name,
    sex: person.sex,
    age: person.age
});
try {
    person.sex = 'female';
} catch (e) {
    console.log(e);
} finally {

}
```

![ES6-05](https://raw.githubusercontent.com/qinbin52qiul/MarkdownPhotos/master/Javascript/ES6/ES6-05.png)

## 三、ES6 入门书籍

[ECMAScript 6 入门 -- 阮一峰](http://es6.ruanyifeng.com/#README)