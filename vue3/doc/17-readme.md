# 【第十七讲】Vue3.x Teleport、使用Teleport自定义一个模态对话框的组件

 发布于 3 年前 作者 [phonegap100](http://bbs.itying.com/user/phonegap100) 1671 次浏览 来自 Vue3教程

### 一、Vue3.x Teleport、

Vue3.x中的组件模板属于该组件，有时候我们想把模板的内容移动到当前组件之外的DOM 中，这个时候就可以使用 Teleport。

表示teleport内包含的内容显示到body中

```
<teleport to="body">
内容
</teleport>
<teleport to="#app">
内容
</teleport>
```

### 二、使用Teleport实现一个模态对话框的组件

**Modal.vue**

```
<template>
<teleport to="body">
    <div class="model-bg" v-show="visible">
        <div class="modal-content">
            <button class="close" @click="$emit('close-model')">X</button>
            <div class="model-title">{{title}}</div>
            <div class="model-body">
                <slot>
                    第一个对话框
                </slot>
            </div>
        </div>
    </div>

</teleport>
</template>

<script>
export default {
    props: ["title", "visible"]
}
</script>

<style lang="scss">
.model-bg {
    background: #000;
    opacity: 0.7;
    width: 100%;
    height: 100%;
    position: absolute;
    top: 0px;
}

.modal-content {
    width: 600px;
    min-height: 300px;
    border: 1px solid #eee;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background: #fff;

    .model-title {
        background: #eee;
        color: #000;
        height: 32px;
        line-height: 32px;
        text-align: center;
    }

    .model-body {
        padding: 40px;
    }

    .close {
        position: absolute;
        right: 10px;
        top: 5px;
        padding: 5px;
        border: none;
        cursor: pointer;
    }

}
</style>
```

Home.vue使用model

```
<template>
<div class="home">
    <button @click="isVisible=true">弹出一个模态对话框</button>
    <modal :visible="isVisible" title="用户登录" @close-model="isVisible=false"></modal>
</div>
</template>

<script>
import Modal from "./Modal"
export default {
    data() {
        return {
            isVisible: false
        }
    },
    components: {
        Modal
    }

}
</script>

<style lang="scss">
.home {
    position: relative;
}
</style>
```