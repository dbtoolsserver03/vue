# 【第三讲】Vue3.x中的事件方法入门、模板语法模板中类和样式绑定

 发布于 3 年前 作者 [phonegap100](http://bbs.itying.com/user/phonegap100) 2861 次浏览 最后一次编辑是 3 年前 来自 Vue3教程

### 一、Vue3.x中的事件方法入门

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

### 二、Vue3 v-bind绑定Class

当`v-bind`与`class`和一起使用时，Vue提供了特殊的增强功能`style`。除了字符串外，表达式还可以求值为对象或数组。

#### **1.1、v-bind:class绑定字符串**

**业务逻辑**：

```
export default {
    name: "App",
    data() {
        return {
            isActive: "active",
        };
    },
};
```

**template模板**：

```
  <div :class="isActive"></div>
```

**Css**：

```
.active {
    width: 100px;
    height: 100px;
    background: red;
}
```

#### **1.2、v-bind:class绑定对象**

**业务逻辑**：

```
export default {
    name: "App",
    data() {
        return {
             isActive: true,
   			 hasError: false
        };
    },
};
```

**template模板**：

```
<div :class="{ 'active': isActive, 'error': hasError }">
    v-bind:class演示
</div>
```

**Css**：

```
.active {
    width: 100px;
    height: 100px;
    background: red;
}

.error {
    color: blue;
}
```

#### **1.3、v-bind:class 结合 class的使用**

**业务逻辑**：

```
export default {
    name: "App",
    data() {
        return {
             isActive: true,
   			 hasError: false
        };
    },
};
```

**template模板**：

class会与:class共同存在

```
<div class="static" :class="{ 'active': isActive, 'error': hasError }">
    v-bind:class演示
</div>
```

**Css**：

```
.static {
    font-size: 20px;
}

.active {
    width: 100px;
    height: 100px;
    background: red;
}

.error {
    color: blue;
}
```

**它将呈现：**

```html
<div class="static active error"></div>
```

#### **1.4、数组语法**

**业务逻辑：**

```
data() {
  return {
    activeClass: 'active',
    errorClass: 'text-danger'
  }
}
```

**模板：**

```
<div :class="[activeClass, errorClass]"></div>
```

#### **1.5、数组语法 结合三目运算**

**业务逻辑：**

```
export default {
    name: "App",
    data() {
        return {
            isActive: true,
            activeClass: 'active',
            errorClass: 'error',
        };
    },
};
```

**模板：**

```
<div :class="[isActive ? activeClass : errorClass]">active</div>
```

**Css：**

```
.active {
    width: 100px;
    height: 100px;
    background: red;
}

.error {
    color: blue;
}
```

### 三、v-bind:style 绑定内联样式

#### **2.1、第一种绑定方式**

**业务逻辑：**

```
data() {
  return {
    activeColor: 'red',
    fontSize: 30
  }
}
```

**模板:**

```
<div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```

#### **2.2、第二种绑定方式**

**业务逻辑：**

```
data() {
  return {
    styleObject: {
      color: 'red',
      fontSize: '13px'
    }
  }
}
```

**模板:**

```
<div :style="styleObject"></div>
```

#### **2.3、第三种绑定方式 数组方式**

**业务逻辑：**

```
data() {
  return {
			 baseStyles: {
                color: 'orange',
                fontSize: '13px'
            },
            overridingStyles: {
                width: "100px",
                height: "100px",
                background: "blue"
            }
  }
}
```

**模板:**

```
<div :style="[baseStyles, overridingStyles]"></div>
```

#### 2.4、自动前缀

当您使用需要一个CSS属性[供应商前缀](https://developer.mozilla.org/en-US/docs/Glossary/Vendor_Prefix)的`:style`，例如`transform`，Vue公司会自动检测并添加适当的前缀到应用的样式。

#### 2.5、多个值

您可以为样式属性提供多个（前缀）值的数组，例如：

```html
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

这只会呈现浏览器支持的数组中的最后一个值。在此示例中，它将`display: flex`为支持非前缀版本的flexbox的浏览器呈现。

### 四、案例：循环数据 第一个数据高亮显示

**业务逻辑：**

```
export default {
    name: "App",
    data() {
        return {
          list: ['马总', '刘总', '李总'],
        };
    },
};
```

**模板：**

```
<ul>
    <li v-for="(item,index) in list" :key="index" :class="{'red':index==0,'blue':index==1}">{{item}}</li>
</ul>
```

**Css：**

```
.red {
    color: red;
}

.blue {
    color: blue;
}
```