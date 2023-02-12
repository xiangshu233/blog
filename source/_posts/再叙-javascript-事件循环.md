---
title: 再叙 JavaScript 事件循环
tags: [事件循环]
categories: [JS]
date: 2023-01-03
updated: 2023-2-12
cover: https://link.jscdn.cn/1drv/aHR0cHM6Ly8xZHJ2Lm1zL3UvcyFBaFhWN0U3bHBTaWtsd1U4c3dGX29MV1VmU2pqP2U9SG9jd3J3.jpg
banner: https://link.jscdn.cn/1drv/aHR0cHM6Ly8xZHJ2Lm1zL3UvcyFBaFhWN0U3bHBTaWtsd1U4c3dGX29MV1VmU2pqP2U9SG9jd3J3.jpg
---
## 一、为什么 JavaScript 是单线程

我们都知道 JavaScript 是一门单线程语言，也就是说，同一个时间内只能做一件事。至于它为什么不能是多线程，这和它的用途有关。作为浏览器脚本语言，JavaScript 的主要用途是与用户互动，操作 DOM。这决定了它只能是单线程，否则会带来很复杂的同步问题。比如，假定 JavaScript 同时有两个线程，一个线程在某个 DOM 节点上添加内容，另一个线程删除了这个 DOM 节点，此时浏览器则会无法处理而报错。

所以为了避免复杂性，从一诞生，JavaScript 就是单线程，这是这门语言的核心特征，将来也不会改变。

## 二、任务队列

单线程就意味着，所有任务需要排队，前一个任务结束，才会执行后一个任务。如果前一个任务耗时很长，后一个任务就不得不一直等着。

如果排队是因为计算量大，CPU 忙不过来，倒也算了，但是很多时候 CPU 是闲着的，因为 IO 设备（输入输出设备）很慢（比如 Ajax 操作从网络读取数据），不得不等着结果出来，再往下执行。

JavaScript 语言的设计者意识到，这时主线程完全可以不管 IO 设备，挂起处于等待中的任务，先运行排在后面的任务。等到 IO 设备返回了结果，再回过头，把挂起的任务继续执行下去。

于是，所有任务可以分成两种，一种是同步任务（synchronous），另一种是异步任务（asynchronous）。

同步任务指的是，在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务；异步任务指的是，不进入主线程、而进入"任务队列"（task queue）的任务，只有"任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。

具体来说，异步执行的运行机制如下。（同步执行也是如此，因为它可以被视为没有异步任务的异步执行。）

> （1）所有同步任务都在主线程上执行，形成一个[执行栈](https://www.ruanyifeng.com/blog/2013/11/stack.html)（execution context stack）。
>
> （2）主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。
>
> （3）一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。
>
> （4）主线程不断重复上面的第三步。

## 三、事件和回调函数

"任务队列"是一个事件的队列（也可以理解成消息的队列），IO 设备完成一项任务，就在"任务队列"中添加一个事件，表示相关的异步任务可以进入"执行栈"了。主线程读取"任务队列"，就是读取里面有哪些事件。

"任务队列"中的事件，除了 IO 设备的事件以外，还包括一些用户产生的事件（比如鼠标点击、页面滚动等等）。只要指定过回调函数，这些事件发生时就会进入"任务队列"，等待主线程读取。

所谓"回调函数"（callback），就是那些会被主线程挂起来的代码。异步任务必须指定回调函数，当主线程开始执行异步任务，就是执行对应的回调函数。

"任务队列"是一个先进先出的数据结构，排在前面的事件，优先被主线程读取。主线程的读取过程基本上是自动的，只要执行栈一清空，"任务队列"上第一位的事件就自动进入主线程。

## 四、Event Loop、宏任务和微任务

事件循环分为三个部分：主线程、宏任务队列、微任务队列，异步任务都会被丢到宏/微任务中

异步任务分为宏任务和微任务

宏任务：`script、setTimeout、setInterval、setImmeditate、I/O`

微任务：`process.nextTick(nodejs)、promise.then(cb)、object.observe`


{% note color:yellow 警告 关于 `promise` 有一个很容易出错的点，只有 `promise.then()`中的回调函数才会被放入到微任务当中去，而 `promise` 这个构造函数的参数是作为同步代码执行的 %}

```js
new promise(resolve => {
    console.log('promise1') // 同步
    resolve() // 同步
    console.log('promise2') // 同步
}).then(function () {
    console.log('promise3') // 进入微任务队列
})
```

同样的在 `async` 函数中 `await` 右边的代码也是同步代码

```js
async function async1() {
    await async2() // 同步代码
    console.log('async1 end') // async1 end 则会被放入微任务队列中
}
async function async2() {
    console.log('async2 end') // async2 end 立即执行打印
}
async1()
```
{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/1/bVcZ9MV %}


简单来说，执行一段代码时，整段代 码会作为宏任务进入主线程执行，接下来会有 3 种情况：

- 同步代码，直接执行
- 碰到 `setTimeout`，分发到宏任务队列
- 碰到 `promise.then(cb)`，分发到微任务队列

下面看这个示例：

```js
console.log(1); // 直接执行

setTimeout(function() {
    console.log(2); // 进入宏任务队列
})

new Promise(function(resolve) {
    console.log(3); //直接执行
    resolve()
}).then(function() {
    console.log(4); // 进入微任务队列
})

console.log(5); // 直接执行

// 打印结果： 1、3、5、4、2
```

在一次事件循环中，会只执行一个宏任务和微任务队列中的所有微任务
都执行完后则进入下一轮事件循环，再从宏任务开始执行（setTimeout）。

所以在整段代码中，`setTimeout` 是在 `then` 之后执行的，因为它俩不在同一次事件循环中。

这里很多人会混淆一个问题，我也是纠结了好久才整明白

问题：JavaScript 事件循环到底是先执行宏任务还是先执行微任务？

问题的关键是在于你指的是一次事件循环中还是一整段代码中。

> 在一次事件循环中，宏任务先执行
> 整段代码中，异步的微任务先执行



其实整个 `<script></script>` 代码块它是一个宏任务，我们可以理解成事件循环就是起始于这个宏任务，那这段代码在执行过程中就会向微任务队列和宏任务队列推入各种任务
同步任务会直接进入主线程依次执行
异步任务会再分为宏任务和微任务
宏任务进入到 `Event Table`中，并在里面注册回调函数，每当指定的事件完成时，`Event Table` 会将这个函数移到 `Event Queue` 中
微任务也会进入到另一个 `Event Table` 中，并在里面注册回调函数，每当指定的事件完成时，`Event Table` 会将这个函数移到 `Event Queue` 中
当主线程内的任务执行完毕，主线程为空时，会检查微任务的 `Event Queue`，如果有任务，就全部执行，如果没有就执行下一个宏任务
上述过程会不断重复，这就是 `Event Loop`，比较完整的事件循环。

## 五、为什么要有微任务
那么为什么会有微任务呢？这种设计是为了给紧急任务一个`插队`的机会，否则新入队的任务`永远被放在队尾`。具体表现在执行过程：
当前宏任务中的 JavaScript 快执行完成时，也就在 JavaScript 引擎准备退出全局执行上下文并清空调用栈的时候，JavaScript 引擎会检查全局执行上下文中的微任务队列，然后按照顺序执行队列中的微任务。

如果在执行微任务的过程中，产生了`新的微任务`，同样会将该微任务添加到微任务队列中，V8 引擎一直循环执行微任务队列中的任务，直到`队列为空才算执行结束`。也就是说在执行微任务过程中**产生的新的微任务并不会推迟到下个宏任务中执行**，而是在`当前的宏任务中继续执行`。



## 六、测试题

下面这个示例能很好的解答什么是事件循环，还挺绕，但是多看几遍其实也就那回事

```ts
console.log('script start'); // 同步

async function async1() {
  await async2(); // 同步
  console.log('async1 end'); // 微任务
}
async function async2() {
  console.log('async2 end'); // 同步
}
async1();

setTimeout(() => { // 定时器2 宏任务 先放入宏任务队列中
  console.log('setTimeout1');
  new Promise<void>((res) => res())
    .then(() => {
      console.log('promise in timer1'); // 第二轮 微任务
    })
    .then(() => {
      console.log('promise in timer2');// 第二轮 微任务
    });
}, 0);
setTimeout(() => { // 定时器3 宏任务 先放入宏任务队列中
  console.log('setTimeout2'); // 第三轮 宏任务
}, 0);

new Promise<void>((resolve) => {
  console.log('promise1'); // 同步
  resolve(); // 同步
  console.log('promise2'); // 同步
})
  .then(() => {
    console.log('promise3'); // 微任务
  })
  .then(() => {
    console.log('promise4'); // 微任务
  });
console.log('script end'); // 同步
```



第一轮事件循环：

首先是同步代码 `console.log('script start');` 立即打印的 `script start`

接着执行 `async1()` 函数，在 `async1()` 中又会先执行 `async2()`，在 `async` 函数中 `await` 右边的代码是同步代码，最终执行 `async2()` ，打印 `async2 end`

执行完 `async2()` 之后，`await` 后面的代码会被放入到微任务队列中，就是这段 `console.log('async1 end');`

下面遇到了两个 `setTimeout` 先不管，标记为宏任务，继续往下执行

接下来遇到了 `Promise`，`Promise` 构造函数中的代码是同步任务，只有 `then` 回调函数才是异步微任务，所以会立即打印 `promise1`  和 `promise2`

而这两个 `then` 中的回调函数会被放入到微任务队列当中，继续往下执行

紧接着遇到最后同步代码 `console`，会打印 `script end`

上面同步任务执行完了接着执行微任务，按照先进先出的原则依次执行：

`async1 end`、`promise3` 、`promise4`

本轮事件循环结束，整理下打印顺序为：

1. script start
2. async2 end
3. promise1
4. promise2
5. script end
6. async1 end
7. promise3
8. promise4

第二轮事件循环：

先执行第一个定时器，注意每次事件循环只会执行一次宏任务和所有微任务，`setTimeout` 是宏任务，内部碰到 `console.log('setTimeout1');` 接着打印 `setTimeout1`

好巧不巧又遇到了 `Promise`，接着把这两个 `then` 回调函数放入微任务中去，接着往下执行又遇到了第二个定时器，前面说了，一次事件循环只执行一个宏任务，接着把第二个定时器放到宏任务队列中，此时调用栈是空的（第一个setTimeout 执行完了），内部检查的时候发现微任务队列中有微任务，接着执行清空微任务队列，依旧是先进先出的原则打印 `promise in timer1` 和 `promise in timer2`

本轮事件循环结束，打印顺序为：

1. setTimeout1
2. promise in timer1
3. promise in timer2

第三轮事件循环：

最后只剩下一个定时器了，直接执行 `console.log('setTimeout2');` 打印 `setTimeout2`

1. setTimeout2



最终打印结果为：

1. script start
2. async2 end
3. promise1
4. promise2
5. script end
6. async1 end
7. promise3
8. promise4
9. setTimeout1
10. promise in timer1
11. promise in timer2
12. setTimeout2

## 代码截图

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/1/eventloop.png %}

