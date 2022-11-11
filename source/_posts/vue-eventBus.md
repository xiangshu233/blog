---
date: 2020-7-7
title: vue 非父子间传值：事件总线（$eventBus）
categories: [Vue]
tags: [vue, 事件总线, 父子通信, 组件通信]
cover: https://tva4.sinaimg.cn/mw690/006wuklaly1gk2zi1o105j31hc0u0dl9.jpg
---

## 事件总线
> `EventBus` 又称为事件总线。在Vue中可以使用 `EventBus` 来作为沟通桥梁的概念，就像是所有组件共用相同的事件中心，可以向该中心注册发送事件或接收事件，所以组件都可以上下平行地通知其他组件，但也就是太方便所以若使用不慎，就会造成难以维护的“灾难”，因此才需要更完善的 `Vuex` 作为状态管理中心，将通知的概念上升到共享状态层次。


```js
// 原理上就是建立一个公共的js文件，专门用来传递消息
// bus.js
import Vue from 'vue'
export default new Vue;

// 在需要的 .vue 传递消息的页面引入
import bus from './bus.js'

// 传递消息
bus.$emit('msg', val)

// 接收消息
bus.$on('msg', val => {
    console.log(val)
})
```

## Demo
src 下新建一个 bus.js
```js
// bus.js
import Vue from 'vue'
export default new Vue
```

创建页面 A
```js
<!-- A.vue-->

<template>
  <p>A.vue：<button @click="sendMsg">发送</button></p>
</template>

<script>
import bus from '../util/bus'
export default {
  methods: {
    sendMsg() {
      bus.$emit("aMsg","来自A页面的消息")
    }
  },
}
</script>

<style>

</style>
```

创建页面 B

```js
<!-- B.vue-->
<template>
  <div>B.vue：{{ msg }}</div>
</template>

<script>
import bus from '../util/bus'
export default {
  data() {
    return {
      msg: ''
    }
  },

  mounted () {
    // $on 事件返回一个回调函数可以用箭头函数来写
    bus.$on("aMsg", (msg)=>{
      // 接收A页面的消息
      console.log(msg);
      this.msg = msg;
    });
  },
}
</script>

<style>

</style>
```

App.vue 引入上面两个页面
```js
<template>
  <div id="app">
    <router-view/>
    <apage></apage>
    <bpage></bpage>

  </div>
</template>

<script>
import Bpage from './views/B'
import Apage from './views/A'
export default {

  components: {
    Bpage,
    Apage,
  },
}
</script>
<style>

</style>

```
## 测试
A组件点击发送！
![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@8f483cccf32f1597c09c6a1d83fc8e99527ddc38/2020/10/14/3e0de036d34cc326b3bbb83c95c98ad3.png)
B组件收到A组件的消息
![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@95f238243365fc86ef6e971c4fa663989d1a5678/2020/10/14/8b5526d49b7217ebd0086d9e6667bc3c.png)

## 单次接收事件或移除所有事件监听
如果你只想监听一次该事件。可以使用 `bus.$once(event, callback)` 事件触发一次后将移除事件。
```js
  mounted () {
    // bus.$on("aMsg", (msg) => {
    //   // 接收A页面的消息
    //   console.log(msg);
    //   this.msg = msg;
    // });

    bus.$once("aMsg", (msg) => {
      // 接收A页面的消息
      console.log(msg);
      this.msg = msg;
    });
  },
```
[bus.$on(event, callback)]{.label}

![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@e5e44b74d7ceae57ac0c1e9f0f9840d30ef9b83c/2020/10/14/dd4fb157e7352588875a1d9d3af8e986.png)

[bus.$once(event, callback)]{.label}

![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@8b61748bc8369cdf1cc6f6016526efeb019ac882/2020/10/14/b74705f13d8ed12004d602874c13fe18.png)

如果你想移除自定义事件监听器，你可以使用 `bus.$off(event, callback)` 来实现。该方法如果没有提供参数，则移除所有的事件监听器；如果只提供事件，则移除该事件所有的监听器；如果同时提供了事件与回调，则只移除这个回调的监听器。

