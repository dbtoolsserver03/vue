# 【第十六讲】Vue3.x中的Mixin实现组件功能的复用 、全局配置Mixin

 发布于 3 年前 作者 [phonegap100](http://bbs.itying.com/user/phonegap100) 2008 次浏览 来自 Vue3教程

### 一、Vue3.x中的mixin介绍使用

混入 (mixin) 提供了一种非常灵活的方式，来分发 Vue 组件中的可复用功能。一个混入对象可以包含任意组件选项。当组件使用混入对象时，所有混入对象的选项将被“混合”进入该组件本身的选项。

**1、新建mixin/base.js**

```
const baseMixin = {
    data() {
       return{
            apiDomain: "http://www.itying.com"
       }
       
    },
    methods: {
        success() {
            console.log('succss')
        },
        error() {
            console.error('error')
        }
    }
}

export default baseMixin;
```

**2、使用mixin**

```
<template>
<div>
    首页模板--{{apiDomain}}
</div>
</template>

<script>
import BaseMixin from '../mixin/base'
export default {
    mixins: [BaseMixin],
    data() {
        return {

        }
    }
}
</script>
```

### 二、关于Mixin的选项合并

当组件和混入对象含有同名选项时，这些选项将以恰当的方式进行“合并”。

**1、比如，数据对象在内部会进行递归合并，并在发生冲突时以组件数据优先。**

```
const myMixin = {
  data() {
    return {
      message: 'hello',
      foo: 'abc'
    }
  }
}

const app = Vue.createApp({
  mixins: [myMixin],
  data() {
    return {
      message: 'goodbye',
      bar: 'def'
    }
  },
  created() {
    console.log(this.$data) // => { message: "goodbye", foo: "abc", bar: "def" }
  }
})
```

**2、同名钩子函数将合并为一个数组，因此都将被调用。另外，混入对象的钩子将在组件自身钩子之前调用。**

```
const myMixin = {
  created() {
    console.log('mixin hook called')
  }
}

const app = Vue.createApp({
  mixins: [myMixin],
  created() {
    console.log('component hook called')
  }
})

// => "混入对象的钩子被调用"
// => "组件钩子被调用"
```

**3、值为对象的选项，例如 `methods`、`components`，将被合并为同一个对象。两个对象键名冲突时，取组件对象的键值对。**

```
const myMixin = {
  methods: {
    foo() {
      console.log('foo')
    },
    conflicting() {
      console.log('from mixin')
    }
  }
}

const app = Vue.createApp({
  mixins: [myMixin],
  methods: {
    bar() {
      console.log('bar')
    },
    conflicting() {
      console.log('from self')
    }
  }
})

const vm = app.mount('#mixins-basic')

vm.foo() // => "foo"
vm.bar() // => "bar"
vm.conflicting() // => "from self"
```

### 三、全局配置Mixin

你还可以为 Vue 应用程序全局应用 mixin

```
const app = Vue.createApp({
  myOption: 'hello!'
})

// 为自定义的选项 'myOption' 注入一个处理器。
app.mixin({
  created() {
    const myOption = this.$options.myOption
    if (myOption) {
      console.log(myOption)
    }
  }
})

app.mount('#mixins-global') // => "hello!"
import { createApp } from 'vue'
import App from './App.vue'
import BaseMixin from './mixin/base'

//原来的代码
// createApp(App).mount('#app')

//修改后的代码
const app=createApp(App);

app.mixin(BaseMixin)

app.mount('#app');
```