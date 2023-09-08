# 【第十五讲】Vue3.x中全局绑定属性、使用Axios和fetchJsonp请求真实api接口数据、函数防抖实现百度搜索

 发布于 3 年前 作者 [phonegap100](http://bbs.itying.com/user/phonegap100) 2776 次浏览 来自 Vue3教程

### 一、Vue3.x中使用Axios请求远程真实Api接口数据

https://github.com/axios/axios

**1、安装**

```
npm install axios --save  
或者
yarn add axios
或者
cnpm install axios --save 
```

**2、引入使用**

```
import Axios from "axios";

axios.get('/user?ID=12345')
  .then(function (response) {
    // handle success
    console.log(response);
  })
  .catch(function (error) {
    // handle error
    console.log(error);
  })
  .then(function () {
    // always executed
  });
```

**完整代码：**

```
getData(){                
  var api='http://www.phonegap100.com/appapi.php?a=getPortalList&catid=20&page=1';
  Axios.get(api).then((response)=>{
  	this.list=response.data.result;
  }).catch((error)=>{
    console.log(error);
  })
}
```

### 二、Vue3.x全局绑定Axios

**vue3.x中全局绑定属性参考**：https://v3.vuejs.org/api/application-config.html#warnhandler

```
import { createApp } from 'vue'
import App from './App.vue'
import axios from 'axios';

var app=createApp(App)
app.config.globalProperties.axios=axios
app.mount('#app')
```

### 三、Vue3.x中使用fetch-jsonp请求jsonp接口

axios不支持jsonp请求，如果我们要用jsonp来请求数据可以使用fetch-jsonp这个模块。

https://github.com/camsong/fetch-jsonp

```
import fetchJsonp from 'fetch-jsonp';
getData() {
            fetchJsonp('https://sp0.baidu.com/5a1Fazu8AA54nxGko9WTAnF6hhy/su?wd=php', {
                    jsonpCallback: 'cb',
                })
                .then(function (response) {
                    return response.json()
                }).then(function (json) {
                    console.log(json)
                }).catch(function (ex) {
                    console.log('parsing failed', ex)
                })
}
```

### 四、使用函数防抖实现百度搜索

```
<template>
<div class="home">
    <button @click="getData()">获取数据</button>

    <input type="text" v-model="keyword" @keyup="getData" />
    <br>
    <ul>
        <li v-for="(item,index) in list" :key="index">{{item}}</li>
    </ul>
</div>
</template>

<script>
import fetchJsonp from 'fetch-jsonp';
export default {
    data() {
        return {
            keyword: "",
            list: [],
            timer: ""
        }
    },
    methods: {
        getData() {
            if (this.keyword != "") {
                clearTimeout(this.timer);
                this.timer = setTimeout(() => {
                    var api = "https://sp0.baidu.com/5a1Fazu8AA54nxGko9WTAnF6hhy/su?wd=" + this.keyword
                    fetchJsonp(api, {
                            jsonpCallback: 'cb',
                        })
                        .then(function (response) {
                            return response.json()
                        }).then((json) => {
                            this.list = json.s
                        })

                }, 200)

            }
        }
    },

}
</script>

<style lang="scss">
.home {
    padding: 20px;
}
</style>
```