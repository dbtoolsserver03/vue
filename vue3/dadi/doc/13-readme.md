# 【第十三讲】Vue3.x自定义组件上面使用v-mode双休数据绑定 以及 slots以及 Prop 的Attribute 继承 、禁用 Attribute 继承

 发布于 3 年前 作者 [phonegap100](http://bbs.itying.com/user/phonegap100) 2583 次浏览 最后一次编辑是 3 年前 来自 Vue3教程

### 一、自定义组件使用`v-model`实现双休数据绑定

前面的课程我们给大家讲过v-model，v-model主要用于表单的双休数据绑定。现在给大家讲解一下v-model实现自定义组件的双休数据绑定。

#### 1.1、单个v-mode数据绑定

默认情况下，组件上的 `v-model` 使用 `modelValue` 作为 prop 和 `update:modelValue` 作为事件。我们可以通过向 `v-model` 传递参数来修改这些名称：

```html
<my-component v-model:foo="bar"></my-component>
```

在本例中，子组件将需要一个 `foo` prop 并发出 `update:foo` 要同步的事件：

```js
const app = Vue.createApp({})

app.component('my-component', {
  props: {
    foo: String
  },
  template: `
    <input 
      type="text"
      :value="foo"
      @input="$emit('update:foo', $event.target.value)">
  `
})
```

#### 1.2、多个 v-model 绑定

通过利用以特定 prop 和事件为目标的能力，正如我们之前在 [`v-model` 参数](https://v3.cn.vuejs.org/guide/component-custom-events.html#v-model-参数)中所学的那样，我们现在可以在单个组件实例上创建多个 v-model 绑定。

每个 v-model 将同步到不同的 prop，而不需要在组件中添加额外的选项。

```html
<user-name
  v-model:first-name="firstName"
  v-model:last-name="lastName"
></user-name>
const app = Vue.createApp({})

app.component('user-name', {
  props: {
    firstName: String,
    lastName: String
  },
  template: `
    <input 
      type="text"
      :value="firstName"
      @input="$emit('update:firstName', $event.target.value)">

    <input
      type="text"
      :value="lastName"
      @input="$emit('update:lastName', $event.target.value)">
  `
})
```

### 二、自定义组件slots

Vue 实现了一套内容分发的 API，这套 API 的设计灵感源自 [Web Components 规范草案](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md)，将 `<slot>` 元素作为承载分发内容的出口。

#### 1、自定义一个按钮组件

```
<template>
<button class="btn-primary">
    <slot></slot>
</button>
</template>

<script>
export default {

}
</script>

<style lang="scss">
.btn-primary {
    padding: 5px 10px;
    background: orange;
    color: #fff;
    border: none;
}
</style>
```

#### 2、调用这个组件

```
<v-button class="btn-primary">登录</v-button>
```

slot还允许在自定义组件里面传入任意的html标签,或者其他组件

```
<v-button class="btn-primary">
	<i>Icon</i> 登录
</v-button>
```

slot中还可以绑定父组件的数据

```
<v-button class="btn-primary">
	<i>Icon</i> 登录
	{{title}}
</v-button>
```

### 三、slots默认值

```html
<button type="submit">
  <slot>Submit</slot>
</button>
<submit-button></submit-button>
```

“Submit”将会被渲染为：

```
<button type="submit">
  Submit
</button>
```

### 五、Vue3.x非 Prop 的Attribute 继承

一个非 prop 的 attribute 是指传向一个组件，但是该组件并没有相应 [props](https://v3.cn.vuejs.org/guide/component-props) 或 [emits](https://v3.cn.vuejs.org/guide/component-custom-events.html#defining-custom-events) 定义的 attribute。常见的示例包括 `class`、`style` 和 `id` 属性。

#### 2.1、当组件返回单个根节点时，非 prop attribute 将自动添加到根节点的 attribute 中。

**如：**

子组件DatePicker.vue

```
<template>
    <div class="date-picker">
        <input type="date" />
    </div>
</template>

<script>
export default {

}
</script>
```

父组件：

```
<template>
    <date-picker data-status="activated"></date-picker>
</template>

<script>
import DatePicker from "./DatePicker"
export default {
    data() {
        return {
            title: "你好vue"
        }
    },
    components: {
        DatePicker
    }
}
</script>
```

**渲染完成的效果：**

```
<div class="date-picker" data-status="activated">
  <input type="datetime" />
</div>
```

#### 2.2、同样的规则适用于事件监听器：

**父组件:**

```
<date-picker @change="submitChange"></date-picker>
```

**子组件：**

```
mounted() {
  console.log(this.$attrs) // { onChange: () => {}  }
}
```

#### 2.3、完整示例：

**子组件DatePicker.vue**

```
<template>
     <select>
        <option value="1">Yesterday</option>

        <option value="2">Today</option>

        <option value="3">Tomorrow</option>
    </select>
</template>
```

**父组件**

```
 <date-picker @change="showChange"></date-picker>
methods: {
    showChange(event) {
      console.log(event.target.value) // 获取子组件选择的值
    }
 }
```

### 六、自定义 Attribute 继承

如果你**不**希望组件的根元素继承 attribute，你可以在组件的选项中设置 `inheritAttrs: false`。例如：

禁用 attribute 继承的常见情况是需要将 attribute 应用于根节点之外的其他元素。

通过将 `inheritAttrs` 选项设置为 `false`，你可以访问组件的 `$attrs` property，该 property 包括组件 `props` 和 `emits` property 中未包含的所有属性 (例如，`class`、`style`、`v-on` 监听器等)。

**子组件：**

```
<template>
<div class="date-picker">
    <input type="date" v-bind="$attrs" />
</div>
</template>

<script>
export default {
    inheritAttrs: false,
    data() {
        return {

        }
    }
}
</script>
```

**父组件：**

```
<template>
    <date-picker data-status="activated"></date-picker>
</template>

<script>
import DatePicker from "./DatePicker"
export default {
    data() {
        return {
            title: "你好vue"
        }
    },
    components: {
        DatePicker
    }
}
</script>
```

**渲染完成的效果：**

```
<div class="date-picker">
  <input type="datetime" data-status="activated" />
</div>
```

### 七、多个根节点上的 Attribute 继承

与单个根节点组件不同，具有多个根节点的组件不具有自动 attribute 回退行为。如果未显式绑定 `$attrs`，将发出运行时警告。

```
<custom-layout id="custom-layout" @click="changeValue"></custom-layout>
// 这将发出警告
app.component('custom-layout', {
  template: `
    <header>...</header>
    <main>...</main>
    <footer>...</footer>
  `
})

// 没有警告，$attrs被传递到<main>元素
app.component('custom-layout', {
  template: `
    <header>...</header>
    <main v-bind="$attrs">...</main>
    <footer>...</footer>
  `
})
```