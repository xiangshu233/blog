---
date: 2021-4-1
title: eventLoop、promise 笔记
categories: [JS]
tags: [es6, promise, eventloop]
cover: https://tva2.sinaimg.cn/large/006wuklaly8h5aptc16swj31hc0u0n4t.jpg
references:
  - title: 带你彻底弄懂 Event Loop
    url: https://segmentfault.com/a/1190000016278115?utm_source=tag-newest
---


## 什么是宏任务与微任务

JS是单线程，但是一些高耗时的操作就带来了进程阻塞的问题，为了解决这个问题，JS有两种任务的执行模式：**同步模式（Synchronous）和异步模式（Asynchronous）**。

在异步模式下，创建异步任务主要分为 `宏任务（Macrotask）` 和 `微任务(Microtask)` 两种。ES6规范中，宏任务被称为Task，微任务被称为Jobs。宏任务是由宿主（浏览器、Node）发起，微任务是由JS自身发起。

| 宏任务（Macrotask）      | 微任务（Microtask）             |
| ------------------------ | ------------------------------- |
| setTimeout               | requestAnimationFrame（有争议） |
| setInterval              | MutationObserver（浏览器环境）  |
| MessageChannel           | Promise.[ then/catch/finally ]  |
| I/O，事件队列            | process.nextTick（Node环境）    |
| setImmediate（Node环境） | queueMicrotask                  |
| script（整体代码块）     |                                 |

如何理解 `script（整体代码块）` 是个宏任务：
如果同时存在两个 `script` 代码块，会首先在执行第一个script代码块中的 `同步代码`，**如果这个过程中有创建了微任务并进入了微任务队列**，第一个script代码块中的同步代码 `执行完成以后` 会首先去清空微任务队列，再去开启第二个script代码块的执行。

## 什么是 Event Loop

示意图

![eventLoop.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2baaf009636748c491898aafeceddb32~tplv-k3u1fbpfcp-watermark.image)



解释：

1、宏任务队列是否为空

- 是——>执行微任务队列

- 否——>执行最早的宏任务——>执行微任务队列

2、微任务队列是否为空

- 是——>下一步

- 否——>执行最早的微任务队列——>继续检查微任务队列是否为空

因为**首次执行宏队列**中会有 script（整体代码块）任务，宏队列执行完毕，在异步任务中，会先执行完所有微任务。新创建的微任务会**立即进入微任务队列排队执行**，**不需要**等待下一次事件轮回。这就是浏览器的事件循环

重点：

1、宏队列（Macrotask）一次只从队列中取一个任务执行，执行完成后就去执行微任务队列中的任务。

2、微任务队列中所有的任务都会被依次取出执行，直到微任务队列（Microtask Queue）为空。



根据示例代码来说明会更加清楚

```js
console.log(1);

setTimeout(() => {
  console.log(2);
  Promise.resolve().then(() => {
    console.log(3)
  });
});

new Promise((resolve, reject) => {
  console.log(4)
  resolve(5)
}).then((data) => {
  console.log(data);
})

setTimeout(() => {
  console.log(6);
})

console.log(7);
```

```js
// 正确答案
1
4
7
5
2
3
6
```

下面来分析下整个流程

- 执行全局script（整体代码块）

```js
console.log(1); // 属于同步代码，首先执行同步代码
```

`Stack Queue: [console]`

`Macrotask Queue: []`

`Microtask Queue: []`

> 打印结果：1



```js
setTimeout(() => {
  // 这个回调函数叫做callback1，setTimeout属于(宏任务)macrotask，所以放到macrotask queue中
  console.log(2);
  Promise.resolve().then(() => {
    console.log(3)
  });
});
```

`Stack Queue: [setTimeout]`

`Macrotask Queue: [callback1]`

`Microtask Queue: []`

此段不执行挂起，放到宏任务队列中等待下次（Event Loop）事件循环执行，即微任务全部执行完毕后，开启下轮事件循环

```js
new Promise((resolve, reject) => {
  // promise没调用then之前属于同步任务这里打印4
  console.log(4)
  resolve(5) //  resolve()方法返回一个由给定的 value 决议的Promise对象。
}).then((data) => {
  // 这个回调函数叫做callback2，promise属于微任务（microtask），所以放到microtask queue中
  console.log(data);
})
```

`Stack Queue: [promise]`

`Macrotask Queue: [callback1]`

`Microtask Queue: [callback2]`

> 打印结果：1、4

>  `resolve()`方法返回一个由给定的 value 决议的Promise对象，`promise`的状态为`fullfilled`，相当于只是定义了一个有状态的`Promise`，但是并没有调用它；
>
> `promise`调用`then`的前提是`promise`的状态为`fullfilled`；
>
> 只有`promise`调用`then`的时候，`then`里面的函数才会被推入`微任务`中；

```JS
setTimeout(() => {
  // 这个回调函数叫做callback3，promise属于微任务（microtask），所以放到macrotask queue中
  console.log(6);
})
```

`Stack Queue: [promise]`

`Macrotask Queue: [callback1, callback3]`

`Microtask Queue: [callback2]`

此段不执行`setTimeout`为异步宏任务挂起

```js
console.log(7);	// 同步代码立即执行
```
`Stack Queue: [console]`

`Macrotask Queue: [callback1, callback3]`

`Microtask Queue: [callback2]`

> 打印结果：1、4、7

- 到这里全局script代码执行完毕，进入下一个步骤，依次从微任务队列（microtask queue）中取出任务执行，直到微任务队列（microtask queue）为空，则本次事件循环结束。





```js
.then((data) => {
  // 这个回调函数叫做callback2，promise属于微任务（microtask），所以放到microtask queue中
  console.log(data);	// 这里data是promise的决议值5
})
```

`Stack Queue: [callback2]`

`Macrotask Queue: [callback1, callback3]`

`Microtask Queue: []`

由于microtask queue只有一个任务，微任务队列清空，本次事件循环结束

> 打印结果：1、4、7、5

```js
setTimeout(() => {
  // 这个回调函数叫做callback1，setTimeout属于(宏任务)macrotask，所以放到macrotask queue中
  console.log(2);
  Promise.resolve().then(() => {
    console.log(3)
  });
});
```

到了这里即代表第二轮事件循环开始，开始执行宏任务队列`callback1`，

`Stack Queue: [callback1]`

`Macrotask Queue: [callback3]`

`Microtask Queue: []`

> 打印结果：1、4、7、5、2

但是，执行callback1的时候又遇到另一个promise，promise异步执行完后，`then`里面的回调函数又被推入微任务队列（microtask queue）中，等待被执行。

> 只有`promise`调用`then`的时候，`then`里面的函数才会被推入`微任务`中

```js
Promise.resolve().then(() => {
    // 这个回调函数叫做callback4，promise属于microtask，所以放到microtask queue中
    console.log(3)
});
```

`Stack Queue: [promise]`

`Macrotask Queue: [callback3]`

`Microtask Queue: [callback4]`

> 打印结果：1、4、7、5、2

此时只是推入微任务中了并没有执行，callback1执行完毕后（清空后），然后再去微任务队列（microtask queue）中依次取出执行

> 传递到`then()`中的函数被置入一个`微任务队列`中，而不是`立即执行`，这意味着它是在`本轮`JavaScript**事件队列（宏任务）**的所有运行时结束了，**事件队列（宏任务）**被清空之后，才开始执行。

```js
console.log(3)
```

`Stack Queue: [callback4]`

`Macrotask Queue: [callback3]`

`Microtask Queue: []`

> 打印结果：1、4、7、5、2、3

- 微任务队列再次全部执行完，此时第二轮事件循环结束，第三轮事件循环开始，再去宏任务队列中去第一个任务执行

```js
setTimeout(() => {
  // 这个回调函数叫做callback3，promise属于微任务（microtask），所以放到macrotask queue中
  console.log(6);
})
```

开始执行宏任务队列

`Stack Queue: [callback3]`

`Macrotask Queue: []`

`Microtask Queue: []`

> 打印结果：1、4、7、5、2、3、6

- 以上，全部执行完后，Stack Queue为空，Macrotask Queue为空，Microtask Queue为空

`Stack Queue: []`

`Macrotask Queue: []`

`Microtask Queue: []`

> 最终打印结果：1、4、7、5、2、3、6

整个流程梳理结束，非常详细，须多看几遍

## Promise.resolve()

`Promise.resolve()` 用于将现有对象转换为 `Promise` 对象

```js
Promise.resolve(4);
// 等价于
new Promise (resolve => resolve(4));
```

如果参数是一个原始值，或者参数不具有 `then` 方法对象，则 `Promise.resolve()` 返回一个新的 `Promise` 对象，状态为 `resolved`（已完成）

```js
const p = Promise.resolve('hello world');

p.then((s) => {
    console.log(s)
});
// helloworld
```

不带任何参数

`Promise.resolve()` 方法允许调用时 `不带参数`，直接返回一个 `resolved` 状态的 `Promise` 对象，所以如果希望得到一个 `Promise` 对象，直接调用`Promise.resolve()` 方法即可。

需要注意的是，立即 `resolve` 的 `Promise` 对象，**是在本轮事件循环（event loop）结束时，而不是下轮事件循环的开始时**。

原因：传递到 `then()` 中的函数被置入一个 `微任务队列` 中，而不是 `立即执行` ，这意味着它是在 `本轮` JavaScript**事件队列（宏任务）**的所有运行时结束了，**事件队列（宏任务）**被清空之后，才开始执行。

```js
setTimeout(() => {
    console,log(3)
}, 0);

Promise.resolve().then(() => {
    console.log(2)
});

console.log(1);
// 1 2 3
```

上面的代码中， `setTimeout(fn, 0)` 在下轮 `“事件循环”开始时` 执行，`Promise.resolve()` 在本轮 `“事件循环”结束时` 执行，`console.log(1)`则是立即执行，因此最先输出。

`then()` 函数里 `不返回值` 或者 `返回的值` 不是 `Promise`，那么 `then` 返回的 `Promise` 将会成为 `接收` 状态（resolve）

## resolve() 本质作用

- `resolve()` 是用来返回一个新的 `Promise` 对象，`promise` 的状态为 `fullfilled`，相当于只是定义了一个有状态的 `Promise`，但是并没有调用它；
- `promise` 调用 `then` 的前提是 `promise` 的状态为 `fullfilled`
- **只有 `promise` 调用 `then` 的时候，`then` 里面的函数才会被推入 `微任务` 中；**

```js
new Promise((resolve, reject) => {
  // ！！注意，这里是同步代码
  console.log(4)
  resolve(5)
}).then((data) => {
  // 这个回调函数叫做callback，promise属于microtask（微任务），所以放到microtask queue中
  console.log(data); // 5
})
```
未完待补充，后续关于 promise 笔记均在此补充

以上

## 结语
> 劝君莫惜金缕衣，劝君须惜少年时。
> 花开堪折直须折，莫待无花空折枝。




