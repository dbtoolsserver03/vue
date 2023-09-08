# 【第六讲】Vue3.x中使用JavaScript表达式 、条件判断、 计算属性和watch侦听

 发布于 3 年前 作者 [phonegap100](http://bbs.itying.com/user/phonegap100) 2378 次浏览 来自 Vue3教程

### 一、Vue3.x模板中使用JavaScript表达式

**业务逻辑:**

```
data() {
    return {
      number: 1
    }
}
```

**template模板**：

```
{{ number + 1 }} 

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('')}}
```

### 二、 v-if v-else v-else-if v-show

#### 2.1 v-if

**业务逻辑:**

```
data() {
    return {
      flag: 1
    }
}
```

**template模板**：

```
<p v-if="flag">v-if flag=true</p>
```

#### 2.2 v-if v-else

```
<div v-if="Math.random() > 0.5">
 大于0.5
</div>
<div v-else>
小于0.5
</div>
```

`v-else` 元素必须紧跟在带 `v-if` 或者 `v-else-if` 的元素的后面，否则它将不会被识别。

#### 2.3、v-else-if

```
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```

与相似`v-else`，`v-else-if`元素必须紧随`v-if`或`v-else-if`元素

#### 2.4、在 `<template>` 元素上使用 v-if 条件渲染分组

因为 `v-if` 是一个指令，所以必须将它添加到一个元素上。但是如果想切换多个元素呢？此时可以把一个 `<template>` 元素当做不可见的包裹元素，并在上面使用 `v-if`。最终的渲染结果将不包含 `<template>` 元素。

```
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

#### 2.5、v-show

另一个用于根据条件展示元素的选项是 `v-show` 指令

```
<h1 v-show="ok">Hello!</h1>
```

#### 2.6、v-if 和 v-show的区别

v-if是dom操作，v-show只是css的显示隐藏，一般来说，`v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 `v-show` 较好。

### 三、计算属性

#### 3.1 计算属性入门

1、在模板中表达式非常便利，但是它们实际上只用于简单的操作。

2、模板是为了描述视图的结构。在模板中放入太多的逻辑会让模板过重且难以维护。这就是为什么 Vue.js 将绑定表达式限制为一个表达式。如果需要多于一个表达式的逻辑，应当使用计算属性。

**业务逻辑：**

```
export default {
    data() {
        return {
            a: 12,
            flag: true,
            message: "三国演义"
        };
    },
    computed: {
        reverseMsg() {
            return this.message.split("").reverse().join("")
        }
    },
    methods: {
        setMsg() {
            this.message = "大家好"
        }
    }

};
```

**template模板**：

```
<template>
    {{reverseMsg}}

    <button @click="setMsg()">改变数据</button>
</template>
```

#### 3.2、计算属性实现数据的筛选

```
export default {
    data() {
        return {
            a: 12,
            flag: true,
            message: "三国演义",
              list:['apple','banana','orange','pear']
        };
    },
    computed: {
        reverseMsg() {
            return this.message.split("").reverse().join("")
        },
        searchList() {

            var tempArr = [];
            this.listData.forEach((value) => {
                if (value.indexOf(this.keyword) != -1 && this.keyword != "") {
                    tempArr.push(value)
                }
            })
            console.log(tempArr)
            return tempArr;
        }
        
    },
    methods: {
        setMsg() {
            this.message = "大家好"
        }
    }

};
<template>
    <input type="text" v-model="keyword" placeholder="请输入关键词" />
    <ul>
        <li v-for="(item,index) in searchList" :key="index">{{item}}</li>
    </ul>
</template>
```

### 四、watch监听数据变化

Vue.js 提供了一个方法 watch ，它用于观察 Vue 实例上的数据变动。当一些数据需要根据其它数据变化时，watch 很诱人 —— 特别是如果你来自 AngularJS 。不过，通常更好的办法是使用计算属性而不是一个命令式的 watch 回调。

**业务逻辑：**

```
export default {
    data() {
        return {           
            firstName: "",
            lastName: "",
            fullName: ""
        };
    },   
    watch: {
        firstName: function (val) {
            this.fullName = val + this.lastName;
        },
        lastName: function (val) {
            this.fullName = this.firstName + val;
        }
    }
};
```

**template模板**：

```
<input type="text" v-model="firstName">
<input type="text" v-model="lastName">

{{ fullName }}
```