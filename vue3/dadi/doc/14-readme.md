# 【第十四讲】Vue3.x中组件的生命周期函数（lifecycle）、 this.$nextTick、动态组件 keep-alive、Vue实现Tab切换

 发布于 3 年前 作者 [phonegap100](http://bbs.itying.com/user/phonegap100) 3440 次浏览 最后一次编辑是 3 年前 来自 Vue3教程

### 一、Vue3.x中组件的生命周期函数

![lifecycle.png](http://bbs.itying.com/public/upload/02e3b550-2d3b-11eb-8ac2-41a88e51bce8.png)

#### **beforeCreate：**

在实例初始化之后，数据观测 (data observer) 和 event/watcher 事件配置之前被调用。

#### **created：**

在实例创建完成后被立即调用。在这一步，实例已完成以下的配置：数据观测 (data observer)，property 和方法的运算，watch/event 事件回调。然而，挂载阶段还没开始，`$el` property 目前尚不可用。

#### **beforeMount：**

在挂载开始之前被调用：相关的 `render` 函数首次被调用。

#### **mounted：**

实例被挂载后调用，这时 `Vue.createApp({}).mount()` 被新创建的 `vm.$el` 替换了。如果根实例挂载到了一个文档内的元素上，当 mounted 被调用时 `vm.$el` 也在文档内。

注意 `mounted` 不会保证所有的子组件也都一起被挂载。如果你希望等到整个视图都渲染完毕，可以在 `mounted` 内部使用 [vm。$nextTick](https://v3.cn.vuejs.org/api/instance-methods.html#nexttick)：

```js
mounted() {
  this.$nextTick(function () {
    // 仅在渲染整个视图之后运行的代码
  })
}
```

#### **beforeUpdate：**

数据更新时调用，发生在虚拟 DOM 打补丁之前。这里适合在更新之前访问现有的 DOM，比如手动移除已添加的事件监听器。

#### **updated：**

由于数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子。

当这个钩子被调用时，组件 DOM 已经更新，所以你现在可以执行依赖于 DOM 的操作。然而在大多数情况下，你应该避免在此期间更改状态。如果要相应状态改变，通常最好使用[计算属性](https://v3.cn.vuejs.org/api/options-data.html#computed)或[侦听器](https://v3.cn.vuejs.org/api/options-data.html#watch)取而代之。

注意，`updated` **不会**保证所有的子组件也都一起被重绘。如果你希望等到整个视图都重绘完毕，可以在 `updated` 里使用 [vm.$nextTick](https://v3.cn.vuejs.org/api/instance-methods.html#nexttick)：

```js
updated() {
  this.$nextTick(function () {
    // 仅在渲染整个视图之后运行的代码
  })
}
```

#### **activated：**

被 keep-alive 缓存的组件激活时调用。

#### **deactivated：**

被 keep-alive 缓存的组件停用时调用。

#### **beforeUnmount：**

在卸载组件实例之前调用。在这个阶段，实例仍然是完全正常的。

#### **unmounted：**

卸载组件实例后调用。调用此钩子时，组件实例的所有指令都被解除绑定，所有事件侦听器都被移除，所有子组件实例被卸载。

### 二、动态组件 keep-alive

当在这些组件之间切换的时候，你有时会想保持这些组件的状态，以避免反复重渲染导致的性能问题，这个时候就用用keep-alive

在不同路由切换的时候想保持组件的状态也可以使用keep-alive

```
    <keep-alive>
        <life-cycle v-if="isShow"></life-cycle>
    </keep-alive>
```

#### keep-alive结合Tab切换Demo:

**业务逻辑：**

```
export default {
    data() {
        return {
            currentTab: 0,
            posts: [{
                    id: 1,
                    title: 'Nodejs 教程',
                    content: '<p>大地老师Nodejs系列教程有Nodejs基础、Nodejs Express、Nodejs Koa、Egg.js、Nest.js等</p>'
                },
                {
                    id: 2,
                    title: 'Golang教程',
                    content: '<p>教程包括：Golang零基础入门进阶教程（50讲）、Beego基础、Mysql入门实战、Gorm入门、Gorm实战、Gorm关联查询、Golang+Beego+Grom仿小米商城项目（真实企业级项目实战）</p>'
                },
                {
                    id: 3,
                    title: 'Ionic5.x教程',
                    content: '<p>Ionic4.x以后在 Angular、Vue、React基础上面做了一些封装，让我们可以用自己喜欢的框架，更快速和容易的开发移动的项目</p>'
                }
            ]

        }
    },
    computed: {
        content() {
            return this.posts[this.currentTab].content;
        }
    }   
}
```

**模板：**

```
<div class="tab_demo">
    <ul class="tab">
        <li v-for="(item,index) in posts" :class="{'active': currentTab === index }" :key="index" @click="currentTab=index">{{item.title}}</li>
    </ul>
    <div class="content">
        <span v-html="content"></span>
    </div>
</div>
```

**Css:**

```
ul {
    list-style: none;
}

.tab_demo {
    width: 600px;
    margin: 0 auto;
    border: 1px solid #eee;

    .tab {
        width: 100%;
        height: 44px;
        line-height: 44px;
        background: #eee;

        li {
            cursor: pointer;
            margin: 0 5px;
            display: inline-block;

            &.active {
                color: red;
            }
        }
    }

    .content {
        padding: 20px;
    }

}
```

### 三、this.$nextTick

Vue中可以把获取Dom节点的代码放在mounted里面，但是如果要在渲染完成数据后获取DOM节点就需要用到`this.$nextTick`

```
 mounted() {
      this.$nextTick(function () {
        // 仅在渲染整个视图之后运行的代码
      })
 },
 mounted() {
       
        var oDiv1 = document.querySelector("#msg");
        console.log("1-" + oDiv1.innerHTML);
        this.msg = "$nextTick演示";
        var oDiv2 = document.querySelector("#msg");
        console.log("2-" + oDiv2.innerHTML);
        this.$nextTick(() => {
            // 仅在渲染整个视图之后运行的代码
            var oDiv3 = document.querySelector("#msg");
            console.log("3-" + oDiv3.innerHTML);
        })
    },
```