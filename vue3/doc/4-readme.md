# 【第四讲】Vue3.x中的事件方法详解、事件监听、方法传值、事件对象、多事件处理程序、事件修饰符、按键修饰符

 发布于 3 年前 作者 [phonegap100](http://bbs.itying.com/user/phonegap100) 3808 次浏览 最后一次编辑是 3 年前 来自 Vue3教程

### 一、Vue3.x监听事件

vue中监听事件可以使用`v-on:click`或者 `@click` ,`@click` 为`v-on:click`的简写，例如：

```
data() {
    return {
      counter: 1
    }
  }
```

**template模板**：

```
div id="basic-event">
  <button @click="counter += 1">Add 1</button>
  <p>The button above has been clicked {{ counter }} times.</p>
</div>
```

### 二、Vue3.x定义方法 执行方法 获取数据 改变数据

**业务逻辑：**

```
export default {
    data() {
        return {
            num: 1,
            msg: "你好vue",
        };
    },
    methods: {
        setMsg() {
            this.msg = "我是方法里改变后的msg";
        },
        getMsg() {
            alert(this.msg);
        },
    },
};
```

**template模板**：

```
<template>
<div>
    <h2>{{ msg }}</h2>

    <button @click="setMsg()">设置msg</button>
    <br />
    <br>
    <button @click="getMsg()">获取msg</button>

    <br>
    <br>
    <h2>Num:{{num}}</h2>

    <button @click="num += 1">Add 1</button>
</div>
</template>
```

### 三、Vue3.x方法传值、方法的相互调用

**业务逻辑：**

```
export default {
    data() {
        return {
            msg: "你好vue",
        };
    },
    methods: {
        setMsg(msg) {
            this.msg = msg;
        },
        run() {
            this.getMsg()
        },
        getMsg() {
            alert(this.msg);
        }
    },
};
```

**template模板**：

```
<template>
<div>
    {{msg}}
    <br>
    <br>

    <button @click="setMsg('这只执行方法传值改变msg')">执行方法传值</button>

    <br>
    <br>
    <button @click="run()">执行run方法</button>
</div>
</template>
```

### 四、事件对象

有时我们还需要在内联语句处理程序中访问原始DOM事件。您可以使用特殊`$event`变量将其传递给方法

#### 一、单个参数

**业务逻辑：**

```
export default {
    data() {
        return {
            msg: "你好vue",
        };
    },
    methods: {
       eventFn(e){
          console.log(e);

          // e.srcElement  dom节点

          e.srcElement.style.background='red';
          console.log(e.srcElement.dataset.aid);  /*获取自定义属性的值*/
		}
    },
};
```

**template模板**：

```
<button data-aid='123' @click="eventFn($event)">事件对象</button>
```

#### 二、多个参数

```
<button @click="warn('Form cannot be submitted yet.', $event)">
  Submit
</button>
methods: {
  warn(message, event) {
    // now we have access to the native event
    if (event) {
      event.preventDefault()
    }
    alert(message)
  }
}
```

### 五、Vue3.x 多事件处理程序

您可以在事件处理程序中使用逗号分隔多个事件处理程序，如下所示：

**业务逻辑：**

```js
methods: {
  one(event) {
    // first handler logic...
  },
  two(event) {
    // second handler logic...
  }
}
```

**template模板**：

```
<button @click="one($event), two($event)">
  Submit
</button>
```

### 六、事件修饰符

vue中阻止冒泡 阻止默认行为，可以通过事件对象`event.preventDefault()`或`event.stopPropagation()`实现，还可以通过事件修饰符实现。

**vue中给我们提供了很多的修饰符：**

```
.stop
.prevent
.capture
.self
.once
.passiv
```

**stopPropagation:**

```
<a @click.stop="doThis"></a>
```

**preventDefault:**

```
<a @click.prevent="doThat"></a>
```

**stopPropagation And preventDefault**

```
<a @click.stop.prevent="doThat"></a>
```

### 七、按键修饰符

监听键盘事件时，我们通常需要检查特定的键。Vue允许在监听关键事件时`v-on`或`@`在监听关键事件时添加按键修饰符：

```
<input @keyup.enter="submit" />
```

Vue为最常用的键提供别名：

- `.enter`
- `.tab`
- `.delete` （同时捕获“删除”和“退格”键）
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`