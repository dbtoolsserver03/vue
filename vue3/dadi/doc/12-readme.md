# 【第十二讲】Vue3.x组件自定义事件 以及mitt 实现非父子组件传值

 发布于 3 年前 作者 [phonegap100](http://bbs.itying.com/user/phonegap100) 4819 次浏览 最后一次编辑是 3 年前 来自 Vue3教程

### 一、父子组件介绍

### 二、Vue3.x组件自定义事件实现子组件给父组件传值

**注意：** Vue官方推荐你始终使用 **kebab-case 的事件名**。

**子组件DatePicker.vue**

```
<template>
<button @click="run">通过广播方式实现子组件给父组件传值</button>
</template>

<script>
export default {
    // 建议定义所有发出的事件，以便更好地记录组件应该如何工作。
    emits: ["run-parent"],
    data() {
        return {}
    },
    methods: {
        run() {
            this.$emit("run-parent", "这是子组件穿过来的值")
        }
    }
}
</script>
```

**父组件Home.vue**

```
<template>
<div>
    <date-picker @run-parent="getChild">
    </date-picker>
</div>
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
    },
    methods: {
        getChild(data) {
            alert(data)
        }
    }
}
</script>

<style lang="scss">

</style>
```

### 三、Vue3.x组件自定义事件验证

**子组件：**

```
<template>
<input type="text" v-model="username" />
<br>
<br>
<input type="text" v-model="password" />
<br>
<br>

<button @click="run">通过广播方式实现子组件给父组件传值</button>
</template>

<script>
export default {
    // 建议定义所有发出的事件，以便更好地记录组件应该如何工作。
    emits: {
        submit: ({
            username,
            password
        }) => {
            if (username && password) {
                return true
            } else {
                console.warn('传入的参数不能为空!')
                return false
            }
        }
    },
    data() {
        return {
            username: "张三",
            password: ""
        }
    },
    methods: {
        run() {
            this.$emit("submit", {
                username: this.username,
                password: this.password
            })
        }
    }
}
</script>
```

**父亲组件：**

```
<template>
<div>
    <login @submit="getChild">
    </login>
</div>
</template>

<script>
import Login from "./Login"
export default {
    data() {
        return {
            title: "你好vue"
        }
    },
    components: {
        Login
    },
    methods: {
        getChild(data) {
            alert(data)
        }
    }
}
</script>

<style lang="scss">

</style>
```

### 四、vue3.x第三方插件mitt 实现非父子组件传值

https://github.com/developit/mitt

Vue3.x以后从实例中移除了 `$on`，`$off` 和 `$once` 方法，`$emit` 仍然是现有 API 的一部分，只能实现子组件触发父组件的方法。

**使用mitt之前先安装mitt模块**

```
 npm install --save mitt
```

#### 3.1、新建model/event.js

```
import mitt from 'mitt'
const VueEvent = mitt();

export default VueEvent;
```

#### 3.2、Header组件

```
<template>
<div>
    <button @click="doLogin">非父子组件传值</button>
</div>
</template>

<script>
import VueEvent from '../model/event'

export default {
    data() {
        return {}
    },
    methods: {
        doLogin() {
            VueEvent.emit("login");
        }
    }
}
</script>
```

#### 3.3、Login组件

```
<template>
我是用户登录组件
</template>

<script>
import VueEvent from '../model/event'
export default {  
    mounted() {
        VueEvent.on("login", () => {
            alert("doLogin")
        })
    }
}
</script>
```