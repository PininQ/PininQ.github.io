---
layout: post
title: 插槽、动态组件和 v-once 指令
categories: Vue
description: 插槽、动态组件和 v-once 指令的简单使用
keywords: Vue, 插槽、动态组件和 v-once 指令
---


## 一、作用域插槽使用场景

- 当子组件需要做循环的时候
- 某一部分 DOM 结构需要由外部传进来的时候

使用作用域插槽，子组件可以向父组件传的插槽里传递数据，父组件
传递过来的插槽如果想要接收这个数据，必须在外层使用 template，同时 需要 ` slot-scope` 对应的属性名字来接收
子组件传递过来的额数据


```html
// 子组件向组件传递 item，父组件在作用域插槽里面接收 item，然后使用
<body>
  <div id="root">
    <body-content>
      <template slot-scope="props">
        <h1>{{ props.item }} -- hello</h1>
      </template>
    </body-content>
  </div>

  <script>
    Vue.component('body-content', {
      data: function() {
        return {
          list: [1, 2, 3, 4, 5]
        }
      },
      template: `<div>
                    <ul>
                      <slot
                        v-for="item of list"
                        :item="item"
                      >
                      </slot>
                    </ul>
                  </div>
                  `
    })

    var vm = new Vue({
      el: '#root'
    })
  </script>
</body>
```
## 二、动态组件和 v-once 指令

### 1. 通过 v-show 实现组件切换

```html
<body>
  <div id="root">
    <child-one v-show="type === 'child-one'"></child-one>
    <child-two v-show="type === 'child-two'"></child-two>
    <button @click="handleBtnClick">change</button>
  </div>

  <script>
    Vue.component('child-one', {
      template: `<div>child-one</div>`
    })

    Vue.component('child-two', {
      template: `<div>child-two</div>`
    })

    var vm = new Vue({
      el: '#root',
      data: {
        type: 'child-one'
      },
      methods: {
        handleBtnClick: function() {
          this.type = this.type === 'child-one' ? 'child-two' : 'child-one';
        }
      }
    })
  </script>
</body>
```

### 2. 通过 动态组件 实现组件切换


```html
<div id="root">
  <!-- 动态组件，会根据 is 中数据的变化，自动的加载不同的组件 -->
  <component :is="type"></component>
  <button @click="handleBtnClick">change</button>
</div>

<script>
  Vue.component('child-one', {
    template: `<div>child-one</div>`
  })

  Vue.component('child-two', {
    template: `<div>child-two</div>`
  })

  var vm = new Vue({
    el: '#root',
    data: {
      type: 'child-one'
    },
    methods: {
      handleBtnClick: function() {
        this.type = this.type === 'child-one' ? 'child-two' : 'child-one'
      }
    }
  })
</script>
```
### 3. v-once 指令的使用

我们在页面上切换组件的时候，Vue 的底层会判断当前的组件不用了，取而代之会用另外一个组件
，然后它会把第一个组件销毁掉，再创建第二个组件。

假设第二个组件又要隐藏，第一个组件又显示的话，这个时候又要把第二个组件销毁掉，再重新创建第一个组件。

也就是说，没进行一次组件的切换，Vue 底层都需要销毁一个组件再创建一个组件。
这种操作是耗费一定的性能的。

如果我们的组件每次都一样，我们可以使用 `v-once`
指令，这样，当第一个组件被渲染的时候，因为组件上有 `v-once` 指令，所以会直接放到内存中。当我们切换到第二个组件的时候，第二个组件也被放到内存里了。

再切换会第一个组件的时候，直接从内存中拿出以前的组件就可以了。


```html
<div id="root">
  <child-one v-show="type === 'child-one'"></child-one>
  <child-two v-show="type === 'child-two'"></child-two>
  <button @click="handleBtnClick">change</button>
</div>

<script>
  Vue.component('child-one', {
    template: `<div v-once>child-one</div>`
  })

  Vue.component('child-two', {
    template: `<div v-once>child-two</div>`
  })

  var vm = new Vue({
    el: '#root',
    data: {
      type: 'child-one'
    },
    methods: {
      handleBtnClick: function() {
        this.type = this.type === 'child-one' ? 'child-two' : 'child-one'
      }
    }
  })
</script>
```

**v-once 指令**

在第一次 DOM 时进行渲染，渲染完成后视为静态内容，跳出以后的渲染过程。


```html
<div v-once>第一次绑定的值：{{message}}</div>
<div><input type="text" v-model="message"></div>
```

