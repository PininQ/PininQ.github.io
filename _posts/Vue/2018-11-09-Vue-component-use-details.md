---
layout: post
title: Vue 组件使用中的一些细节点
categories: Vue
description: Vue 组件使用中的一些细节点
keywords: Vue, 组件
---

记录一些组件使用中的一些细节点，这些细节点平时我们可能没有太注意，但是用起来对我们的帮助还是比较大的。


后面的示例中都使用如下 Vue 代码模板；

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>组件使用中的细节点</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.5.16/vue.js"></script>
</head>

<body>

  <div id="root">

  </div>

  <script>
    var vm = new Vue({
      el: '#root'
    })
  </script>

</body>
</html>
```

## 一、is 标签的使用

在项目开发中，可以使用 `is` 标签解决渲染中可能出现的一些小 BUG。

以 table 为例，正常代码：

```html
<div id="root">
  <table>
    <tbody>
      <tr><td>1</td></tr>
      <tr><td>2</td></tr>
      <tr><td>3</td></tr>
    </tbody>
  </table>
</div>

<script>
  var vm = new Vue({
    el: '#root'
  })
</script>
```

有时候，我们希望 table 中的每一行的数据都是一个组件，子组件可以如下定义

定义和使用子组件的代码如下：

```html
<div id="root">
  <table>
    <tbody>
      <row></row>
      <row></row>
      <row></row>
    </tbody>
  </table>
</div>

<script>
  Vue.component('row', {
    template: '<tr><td>this is a row</td></tr>'
  })

  var vm = new Vue({
    el: '#root'
  })
</script>
```

结果如下，

![Vue-component-use-details-01](https://raw.githubusercontent.com/qinbin52qiul/MarkdownPhotos/master/Vue/Vue-component-use-details/Vue-component-use-details-01.png)

看样子，结果出错了。一切并未如我们所预料的那样，子组件的内容渲染在 table 标签中。

三个 tr 应该在 tbody 中，现在 tr 却跑到了 table 外面。

在 HTML5 的规范里面，要求 table 里面有 tbody，tbody 里面必须得放 tr，而现在，我们使用子组件，
原本放 tr 的位置放了子组件 row，所以浏览器在解析 row 的时候，就会出现这种问题。

解决的办法是使用 Vue 给我们提供的 `is` 属性。

使用 `is` 属性的代码如下：

```html
<div id="root">
  <table>
    <tbody>
      <tr is='row'></tr>
      <tr is='row'></tr>
      <tr is='row'></tr>
    </tbody>
  </table>
</div>

<script>
  Vue.component('row', {
    template: '<tr><td>this is a row</td></tr>'
  })

  var vm = new Vue({
    el: '#root'
  })
</script>
```

使用 `is` 属性后的结果如下：

![Vue-component-use-details-02](https://raw.githubusercontent.com/qinbin52qiul/MarkdownPhotos/master/Vue/Vue-component-use-details/Vue-component-use-details-02.png)

在 tbody 中，要使用一个组件，但是不能直接写组件的名字，可以使用 `is` 属性，虽然写的是 tr，
但是实际上它是一个 row 的组件。

这样写的好处：
- tr 中使用的是子组件
- 符合 HTML5 的编码规范

除了 table 中的 tbody 不建议直接使用子组件，还有 ul、ol、select。这就是使用 is 标签解决模板上出现的一些 BUG 的问题。

## 二、ref 使用

官方解释：

`ref` 被用来给元素或子组件注册引用信息。引用信息将会注册在父组件的 `$refs` 对象上。
- 如果在普通的 DOM 元素上使用，引用指向的就是 DOM 元素；
- 如果用在子组件上，引用就指向组件实例。

Vue 官网不建议我们在代码里操作 DOM，但是有一些很是复杂的效果不操作 DOM，光靠 Vue 的数据绑定，有时候是处理不了。所以，在某些必要的情况是必须操作 DOM 的。

那么，在 Vue 中是如何操作 DOM 的呢？

在 Vue 中，我们需要通过 ref 这种引用的形式来获取 DOM 并进行 DOM 的操作。

div 标签中使用 ref 的代码如下：

```html
<div id="root">
  <div
    ref='hello'
    @click="handleClick"
  >
    hello world
  </div>
</div>

<script>
  var vm = new Vue({
    el: '#root',
    methods: {
      handleClick: function() {
        // 在所有的引用中找到引用名字为 hello 的引用
        // 而 hello 指向的是 div 所对应的 DOM
        console.log(this.$refs.hello)
      }
    }
  })
</script>
```

执行结果如下：

![Vue-component-use-details-03](https://raw.githubusercontent.com/qinbin52qiul/MarkdownPhotos/master/Vue/Vue-component-use-details/Vue-component-use-details-03.png)

可以看到，在 div 这样的元素标签上使用 ref，通过 `this.$refs.ref` 获取到这个引用的时候，获取到的是一个 DOM 元素

如果是在一个组件上使用 ref，通过 `this.$refs.ref` 获取到的是一个组件的引用。

那么，在组件中使用 ref 代码如下：

```html
<div id="root">
  <!-- 父组件监听 change 事件，一旦触发 change 事件，则执行 handleChange 方法 -->
  <counter ref="one" @change="handleChange"></counter>
  <counter ref="two" @change="handleChange"></counter>
  <div>{{ total }}</div>
</div>

<script>
  // 创建一个计数器的子组件 counter，每点击一次，number 加1
  Vue.component('counter', {
    data: function() {
      return {
        number: 0
      }
    },
    template: '<div @click="handleClick">{{number}}</div>',
    methods: {
      handleClick: function() {
        this.number++
        // 子组件向外触发一个事件，通知 number 已经被改变
        this.$emit('change', this.number)
      }
    }
  })

  var vm = new Vue({
    el: '#root',
    data: {
      total: 0
    },
    methods: {
      // 子组件 number 改变，对两个计数器中的点击次数进行求和
      handleChange: function() {
        console.log(this.$refs.one)
        console.log(this.$refs.two)
        this.total = this.$refs.one.number + this.$refs.two.number
      }
    }
  })
</script>
```

`this.$refs.one` 指向的是 `ref="one"` 的子组件

`this.$refs.two` 指向的是 `ref="two"` 的子组件

结果如下：

![Vue-component-use-details-04](https://raw.githubusercontent.com/qinbin52qiul/MarkdownPhotos/master/Vue/Vue-component-use-details/Vue-component-use-details-04.gif)

小结：
- 当在 `div标签` 上使用 ref 的时候，通过 `this.$refs.ref` 获取到的是标签对应的 DOM
- 当在 `组件` 上使用 ref 的时候 ，通过 `this.$refs.ref` 获取到的是子组件的引用
