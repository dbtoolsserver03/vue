# 【第27-30讲】Vuex教程-Vuex 中的 State Mutation Getters mapGetters Actions Modules

 发布于 3 年前 作者 [phonegap100](http://bbs.itying.com/user/phonegap100) 3113 次浏览 最后一次编辑是 3 年前 来自 Vue3教程

## 一、Vuex

Vuex 是一个专为 Vue.js 应用程序开发的**状态管理模式**。

官网：https://next.vuex.vuejs.org/

##### **主要功能：**

1、vuex可以实现vue不同组件之间的状态共享  （解决了不同组件之间的数据共享）

2、可以实现组件里面数据的持久化。

##### **Vuex的几个核心概念：**

State

Getters

Mutations

Actions

Modules

### 二、Vuex的基本使用

#### 1、安装依赖

##### NPM

```
npm install vuex@next --save
```

##### yarn

```
yarn add vuex@next --save
```

#### 2、src目录下面新建一个vuex的文件夹，vuex 文件夹里面新建一个store.js

```
import { createStore } from 'vuex'
const store = createStore({
    state () {
      return {
        count: 1
      }
    },
    mutations: {
        increment (state) {     
          state.count++
        }
      }
  })  

export default store;
```

#### 3、main.ts中挂载Vuex

```
import { createApp } from 'vue'
import App from './App.vue'
import route from './routes'
import store from './vuex/store'


let app=createApp(App);
//挂载路由
app.use(route)
//挂载vuex
app.use(store)
app.mount('#app')
```

#### 4、获取 修改state里面的数据

```
<template>
    <div>
      增加新闻--{{count}}
      <br>
      <button @click="incCount">改变Vuex里面的count</button>
    </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
export default defineComponent({
    data(){
        return{}
    },methods:{        
        incCount(){
            this.$store.commit('increment')
        }
    },
    computed:{
        count():number{            
            return this.$store.state.count
        }
    }
})
</script>
```

### 三、Vuex中的State

State在Vuex中主要用于存储数据，State是存储在 Vuex 中的数据和 Vue 实例中的 `data` 遵循相同的规则。

```
import { createStore } from 'vuex'
const store = createStore({
    state () {
      return {
        count: 1,
        list:['马总','雷总','王总']
      }
    },
    mutations: {
        increment (state) {     
          state.count++
        }
      }
  })  

export default store;
```

#### 3.1、第一种获取State的方法（不推荐）

用到的组件里面引入store，然后计算属性里面获取

```
computed: {
    count () {
      return store.state.count
    }
  }
```

#### 3.2、第二种获取State的方法

由于全局配置了Vuex `app.use(store)`，所以直接可以通过下面方法获取store里面的值。

```
computed: {
    count () {
      return this.$store.state.count
    }
}
```

#### 3.3、第三种获取State的方法-通过`mapState`助手

**方法 1：**

```
<template>
  <div>修改新闻--{{ count }}</div>

  <br />
  <ul>
    <li v-for="(item, index) in list" :key="index">{{ item }}</li>
  </ul>
  <br />
</template>

<script>
import { defineComponent } from "vue";
import { mapState } from "vuex";
export default defineComponent({
  data() {
    return {};
  },
  methods: {},
  computed: {
    ...mapState({
      count: (state) => state.count,
      list: (state) => state.list,    
    }),
  },
});
</script>
```

**方法 2：**

```
<template>
  <div>修改新闻--{{ count }}</div>

  <br>  
  <ul>
    <li v-for="(item,index) in list" :key="index">{{item}}</li>
  </ul>
  <br>

</template>

<script>
import { defineComponent } from "vue";
import { mapState } from "vuex";
export default defineComponent({
  data() {
    return {};
  },
  methods: {},
  computed: {
    ...mapState([     
      "count",
      "list"
    ]),
  },
});
</script>
```

### 四：Vuex中的Getters

Getter有点类似我们前面给大家讲的计算属性。

Vuex 允许我们在 store 中定义“getter”（可以认为是 store 的计算属性）。就像计算属性一样，getter 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算。

#### 4.1 、定义Getter

```js
const store = createStore({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
  }
})
```

#### 4.2、访问Getter的第一种方法

Getter 会暴露为 `store.getters` 对象，你可以以属性的形式访问这些值：

```
store.getters.doneTodos
```

#### **4.3、访问Getter的第二种方法**

```
computed: {
  doneTodosCount () {
    return this.$store.getters.doneTodosCount
  }
}
```

#### 4.4、访问Getter的第四种方法 通过`mapGetters` 辅助函数

```
import { mapGetters } from 'vuex'

export default {
  // ...
  computed: {
  // 使用对象展开运算符将 getter 混入 computed 对象中
    ...mapGetters([
      'doneTodosCount',
      'anotherGetter',
      // ...
    ])
  }
}
```

如果你想将一个 getter 属性另取一个名字，使用对象形式：

```js
...mapGetters({
  // 把 `this.doneCount` 映射为 `this.$store.getters.doneTodosCount`
  doneCount: 'doneTodosCount'
})
```

### 五、Vuex中的Mutations

更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。Vuex 中的 mutation 非常类似于事件：每个 mutation 都有一个字符串的 **事件类型 (type)** 和 一个 **回调函数 (handler)**。这个回调函数就是我们实际进行状态更改的地方，并且它会接受 state 作为第一个参数。

#### 4.1、定义Mutations 触发Mutations里面的方法

```
const store = createStore({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      // mutate state
      state.count++
    }
  }
})
```

触发mutations里面的方法：

```
store.commit('increment')
```

#### 4.2、执行方法传入参数：

```
mutations: {
  increment (state, n) {
    state.count += n
  }
}
store.commit('increment', 10)
```

#### 4.3 对象方式提交数据

```
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}
store.commit({
  type: 'increment',
  amount: 10
})
```

#### 4.4 在组件中提交 Mutation

```
import { mapMutations } from 'vuex'

export default {
  // ...
  methods: {
    ...mapMutations([
      'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`

      // `mapMutations` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
    ]),
    ...mapMutations({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
    })
  }
}
```

### 六、Vuex中的Actions

Action 类似于 mutation，不同在于：

- Action 提交的是 mutation，而不是直接变更状态。
- Action 可以包含任意异步操作。

#### 6.1、定义Action

```
const store = createStore({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    }
  }
})
```

另一种写法

```
actions: {
  increment ({ commit }) {
    commit('increment')
  }
}
```

#### 6.2、分发 Action(触发Action中的方法)

```
store.dispatch('increment')
```

乍一眼看上去感觉多此一举，我们直接分发 mutation 岂不更方便？实际上并非如此，还记得 **mutation 必须同步执行**这个限制么？Action 就不受约束！我们可以在 action 内部执行**异步**操作：

```
actions: {
  incrementAsync ({ commit }) {
    setTimeout(() => {
      commit('increment')
    }, 1000)
  }
}
```

Actions 支持同样的载荷方式和对象方式进行分发：

```
// 载荷方式
store.dispatch('incrementAsync', {
  amount: 10
})

// 对象方式
store.dispatch({
  type: 'incrementAsync',
  amount: 10
})
```

来看一个更加实际的购物车示例，涉及到**调用异步 API** 和**分发多重 mutation**：

```
actions: {
  checkout ({ commit, state }, products) {
    // 把当前购物车的物品备份起来
    const savedCartItems = [...state.cart.added]
    // 发出结账请求，然后乐观地清空购物车
    commit(types.CHECKOUT_REQUEST)
    // 购物 API 接受一个成功回调和一个失败回调
    shop.buyProducts(
      products,
      // 成功操作
      () => commit(types.CHECKOUT_SUCCESS),
      // 失败操作
      () => commit(types.CHECKOUT_FAILURE, savedCartItems)
    )
  }
}
```

注意我们正在进行一系列的异步操作，并且通过提交 mutation 来记录 action 产生的副作用（即状态变更）

#### 6.3 在组件中分发 Action

你在组件中使用 `this.$store.dispatch('xxx')` 分发 action，或者使用 `mapActions` 辅助函数将组件的 methods 映射为 `store.dispatch` 调用（需要先在根节点注入 `store`）：

```
import { mapActions } from 'vuex'

export default {
  // ...
  methods: {
    ...mapActions([
      'increment', // map `this.increment()` to `this.$store.dispatch('increment')`

      // `mapActions` also supports payloads:
      'incrementBy' // map `this.incrementBy(amount)` to 		`this.$store.dispatch('incrementBy', amount)`
    ]),
    ...mapActions({
      add: 'increment' // map `this.add()` to `this.$store.dispatch('increment')`
    })
  }
}
```

#### 6.4 组合 Action

Action 通常是异步的，那么如何知道 action 什么时候结束呢？更重要的是，我们如何才能组合多个 action，以处理更加复杂的异步流程？

首先，你需要明白 `store.dispatch` 可以处理被触发的 action 的处理函数返回的 Promise，并且 `store.dispatch` 仍旧返回 Promise：

```
actions: {
  actionA ({ commit }) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        commit('someMutation')
        resolve()
      }, 1000)
    })
  }
}
```

现在你可以：

```
store.dispatch('actionA').then(() => {
  // ...
})
```

在另外一个 action 中也可以:

```
actions: {
  // ...
  actionB ({ dispatch, commit }) {
    return dispatch('actionA').then(() => {
      commit('someOtherMutation')
    })
  }
}
```

最后，如果我们利用 [async / await](https://tc39.github.io/ecmascript-asyncawait/)，我们可以如下组合 action：

```
// assuming `getData()` and `getOtherData()` return Promises

actions: {
  async actionA ({ commit }) {
    commit('gotData', await getData())
  },
  async actionB ({ dispatch, commit }) {
    await dispatch('actionA') // wait for `actionA` to finish
    commit('gotOtherData', await getOtherData())
  }
}
```

### 七、Modules

由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。

为了解决以上问题，Vuex 允许我们将 store 分割成**模块（module）**。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块——从上至下进行同样方式的分割：

```
const moduleA = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... }
}

const store = createStore({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> `moduleA`'s state
store.state.b // -> `moduleB`'s state
```

#### 模块的局部状态

对于模块内部的 mutation 和 getter，接收的第一个参数是**模块的局部状态对象**。

```
const moduleA = {
  state: () => ({
    count: 0
  }),
  mutations: {
    increment (state) {
      // `state` is the local module state
      state.count++
    }
  },
  getters: {
    doubleCount (state) {
      return state.count * 2
    }
  }
}
```

### 八 、Vuex项目结构

Vuex 并不限制你的代码结构。但是，它规定了一些需要遵守的规则：

1. 应用层级的状态应该集中到单个 store 对象中。
2. 提交 **mutation** 是更改状态的唯一方法，并且这个过程是同步的。
3. 异步逻辑都应该封装到 **action** 里面。

只要你遵守以上规则，如何组织代码随你便。如果你的 store 文件太大，只需将 action、mutation 和 getter 分割到单独的文件。

对于大型应用，我们会希望把 Vuex 相关代码分割到模块中。下面是项目结构示例：

```
├── index.html
├── main.js
├── api
│   └── ... # 抽取出API请求
├── components
│   ├── App.vue
│   └── ...
└── store
    ├── index.js          # 我们组装模块并导出 store 的地方
    ├── actions.js        # 根级别的 action
    ├── mutations.js      # 根级别的 mutation
    └── modules
        ├── cart.js       # 购物车模块
        └── products.js   # 产品模块
```

### 九、Vuex结合组合式合成API

组合式api中没有this.$store，可以使用useStore来替代

```
import { useStore } from 'vuex'

export default {
  setup () {
    const store = useStore()
  }
}
```

#### 9.1、组合式api中访问state 和 getters

```
const store = new createStore({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
  }
})
import { computed } from 'vue'
import { useStore } from 'vuex'

export default {
  setup () {
    const store = useStore()

    return {
      // access a state in computed function
      count: computed(() => store.state.count),

      // access a getter in computed function
      double: computed(() => store.getters.double)
    }
  }
}
```

#### 9.2、组合式api中访问 Mutations and Actions

```
import { useStore } from 'vuex'

export default {
  setup () {
    const store = useStore()

    return {
      // access a mutation
      increment: () => store.commit('increment'),

      // access an action
      asyncIncrement: () => store.dispatch('asyncIncrement')
    }
  }
}
```

### 十、Vue+Typescript的项目里面集成Vuex

首先需要在vue项目中集成typescript

```
vue add typescript
```

**提示：**如果配置完ts后调用this.$store有警告信息，请重启vscode，或者安装vue3的插件后重启vscode充实

#### 一、修改store.js 为store.ts

#### 二、配置store.ts中的代码

Vuex与TypeScript一起使用时，必须声明自己的模块扩充。

```
import { ComponentCustomProperties } from 'vue'
import { createStore,Store  } from 'vuex'

//配置让Vuex支持ts
declare module '@vue/runtime-core' {
  //declare your own store states
  interface State {
    count: number,
    list:string[]
  }

  // provide typings for `this.$store`
  interface ComponentCustomProperties {
    $store: Store<State>
  }
}

const store = createStore({
    state () {
      return {
        count: 1,
        list:['马总','雷总','王总']
      }
    },
    mutations: {
        increment (state:any):void {     
          state.count++
        }
      }
  })
export default store;
```

#### 三、main.ts中挂载

```
import { createApp } from 'vue'
import App from './App.vue'
import route from './routes'
import store from './vuex/store'


let app=createApp(App);
//挂载路由
app.use(route)
//挂载vuex
app.use(store)
app.mount('#app')
```

#### 四、组件中使用挂载

```
<template>
  <div>修改新闻--{{ count }}</div>

  <br />
  <ul>
    <li v-for="(item, index) in list" :key="index">{{ item }}</li>
  </ul>
  <br />
</template>

<script lang="ts">
import { defineComponent } from "vue";
import { mapState } from "vuex";
export default defineComponent({
  data() {
    return {};
  },
  methods: {},
  computed: {
    mylist():string[]{
      return  this.$store.state.list
    },
    ...mapState({
      count: (state:any) => state.count,
      list: (state:any) => state.list,    
    })   
  },
});
</script>
```