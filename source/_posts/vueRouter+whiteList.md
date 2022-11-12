---
date:  2020-8-13
title: vue 路由配置（导航守卫 + 白名单）
categories: [Vue]
tags: [vue, router]
---

## Vue Router

> Vue Router 是 [Vue.js](http://cn.vuejs.org/) 官方的路由管理器。它和 Vue.js 的核心深度集成，让构建单页面应用变得易如反掌。包含的功能有：
>
> - 嵌套的路由/视图表
> - 模块化的、基于组件的路由配置
> - 路由参数、查询、通配符
> - 基于 Vue.js 过渡系统的视图过渡效果
> - 细粒度的导航控制
> - 带有自动激活的 CSS class 的链接
> - HTML5 历史模式或 hash 模式，在 IE9 中自动降级
> - 自定义的滚动条行为



## 导航守卫

> 译者注
>
> “导航”表示路由正在发生改变。
>
> 正如其名，`vue-router` 提供的导航守卫主要用来通过跳转或取消的方式守卫导航。有多种机会植入路由导航过程中：全局的, 单个路由独享的, 或者组件级的。
>
> 记住**参数或查询的改变并不会触发进入/离开的导航守卫**。你可以通过[观察 `$route` 对象](https://router.vuejs.org/zh/guide/essentials/dynamic-matching.html#响应路由参数的变化)来应对这些变化，或使用 `beforeRouteUpdate` 的组件内守卫。



## 全局前置守卫

> 你可以使用 `router.beforeEach` 注册一个全局前置守卫：
>
> ```js
> const router = new VueRouter({ ... })
>
> router.beforeEach((to, from, next) => {
> // ...
> })
> ```
>
> 当一个导航触发时，全局前置守卫按照创建顺序调用。守卫是异步解析执行，此时导航在所有守卫 resolve 完之前一直处于 **等待中**。
>
> 每个守卫方法接收三个参数：
>
> - **`to: Route`**: 即将要进入的目标 [路由对象](https://router.vuejs.org/zh/api/#路由对象)
> - **`from: Route`**: 当前导航正要离开的路由
> - **`next: Function`**: 一定要调用该方法来 **resolve** 这个钩子。执行效果依赖 `next` 方法的调用参数。
>
> [more...](https://router.vuejs.org/zh/)



## 项目需求

用户未登录的情况下默认跳转至登录界面，需要导航守卫来确保这一需求，但是，如一些 `忘记密码`、`注册`、`用户协议/隐私`  等页面操作并不需要用户进行登录操作的就需要进行白名单配置，跳出导航守卫直接渲染出来。



## 代码实现

路由表目录
- router
  - constRouters.js
  - index.js
  - routes.js

- `constRouters.js`文件中定义的是路由白名单，也就是那些不需要用户登陆之后再进行跳转的页面

- `index.js`文件中写的便是具体路由跳转的逻辑代码，也是最重要的一部分

- `routes.js`文件中定义的是默认路由，也就是上面说到的需要登陆才能操作的页面



### constRouters.js

```js
// 白名单
export default [
  {
    path: '*',
    name: 'NotFound',
    component: () => import('../views/error/NotFound')
  },
  {
    path: '/login',
    name: 'Login',
    component: () => import('../views/login/Login')
  },
  {
    path: '/privacy', // 用户隐私
    name: 'Privacy',
    component: () => import('../views/login/Privacy')
  },
  {
    path: '/userAgreement', // 用户协议
    name: 'userAgreement',
    component: () => import('../views/login/userAgreement')
  }
];
```

### index.js

```js
import Vue from 'vue';
import VueRouter from 'vue-router';
import routes from './routes';
import constRouters from './constRouters';
import auth from '@/common/auth';

Vue.use(VueRouter);

function exportWhiteListFromRouter(router) {
  let res = [];
  for(let item of router) {
    res.push(item.path);
  }
  return res;
}

const router = new VueRouter({
  mode: 'hash',
  base: process.env.BASE_URL,
  routes,

  // 滚动行为 https://router.vuejs.org/zh/guide/advanced/scroll-behavior.html
  scrollBehavior(to, from, savedPosition) {
    if (savedPosition) {
      return savedPosition;
    }
    return { x: 0, y: 0 };
  }
});

router.addRoutes(constRouters);
const whiteList = exportWhiteListFromRouter(constRouters);

// to: Route: 即将要进入的目标 路由对象
// from: Route: 当前导航正要离开的路由
// next: Function: 一定要调用该方法来 resolve 这个钩子。执行效果依赖 next 方法的调用参数。
router.beforeEach((to, from, next) => {
  next();
  if(whiteList.indexOf(to.path) != -1) {
    console.log('白名单');
    next();
    return;
  }

  if (!auth.getStorageObj('userInfo') && to.path !== '/login') {
    next({ path: '/login' });
  }else {
    window.pageReturned = false;
    next();
  }
});

export default router;

```

### routes.js

```js
export default [
  {
    path: '/',
    redirect: '/login'
  },
  {
    path: '/index',
    name: 'Index',
    component: () => import('../views/index/Index')
  },
  {
    path: '/historyChart/:partitionId',
    name: 'historyChart',
    component: () => import('../views/index/historyChart')
  },
  {
    path: '/historyData',
    name: 'historyData',
    component: () => import('../views/history/historyData')
  },
  {
    path: '/alarmData',
    name: 'alarmData',
    component: () => import('../views/alarm/alarmData')
  },
  {
    path: '/user',
    name: 'User',
    component: () => import('../views/user/User')
  },
  {
    path: '/userInfo',
    name: 'userInfo',
    component: () => import('../views/detail/userInfo')
  },
  {
    path: '/editPwd',
    name: 'editPwd',
    component: () => import('../views/detail/editPwd')
  },
  {
    path: '/feedBack',
    name: 'feedBack',
    component: () => import('../views/detail/feedBack')
  }
];
```



### 核心代码

在 `index.js`  中引入白名单路由表

```js
import constRouters from './constRouters';
```

将`白名单`添加至路由对象中

```js
router.addRoutes(constRouters);
```

获取`白名单`中的 `path` push 进 `res` 数组中

```js
function exportWhiteListFromRouter(router) {
  let res = [];
  for(let item of router) {
    res.push(item.path);
  }
  return res;
}
```

`白名单`作为参数调用`exportWhiteListFromRouter`方法获取白名单`path`

```js
const whiteList = exportWhiteListFromRouter(constRouters);
```

在全局前置守卫中如下代码，这段代码的意思是即将进入的路由在 `whiteList` 存不存在，如果存在即放行

>`indexOf()` 如果要检索的字符串值没有出现，则该方法返回  `-1`。[more...](https://www.w3school.com.cn/jsref/jsref_indexOf.asp)

```js
router.beforeEach((to, from, next) => {
  next();
  // 添加以下代码
  if(whiteList.indexOf(to.path) != -1) {
    console.log('白名单');
    next();
    return;
  }

  //..其他逻辑
});
```



## 总结

在这个项目背景下，我的理解中导航守卫的作用便是防止未登陆用户进入系统内，需先默认跳转至登陆页面进行登陆之后，才允许跳转至其它页面。但在很多情况下，有些功能并不需要用户进行登陆，因此为了解决这一问题我们采取了路由白名单，将这种特殊功能的页面路由单独拎出来，使其跳出导航守卫以达到我们的目的。

