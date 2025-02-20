---
date: 2023-11-23
title: TypeScript 中的 never 类型另一个妙用
categories: [笔记]
tags: [TypeScript]
cover: https://03c068f.webp.li/i/2025/02/08/wg1ic-wg.jpg
references:
  - title: 'TypeScript中的never应用场景【渡一教育】'
    url: https://www.bilibili.com/video/BV1kw411N7GN/?spm_id_from=333.337.search-card.all.click&vd_source=324bd11f94c36a5b45ef0ae878feea05
---

## Never

在 TypeScript 中，`never` 本质就是一个**空集**。事实上，在另一个流行的 JavaScript 类型系统 [Flow](https://link.juejin.cn/?target=https%3A%2F%2Fflow.org%2F) 中，作用完全一样的类型直接被命名为 [empty](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Ffacebook%2Fflow%2Fcommit%2Fc603505583993aa953904005f91c350f4b65d6bd)。

由于集合中没有值，`never` 类型字面意义地永远不会（双关）有任何值，包括 `any` 类型的也不在 `never` 这个空集中。这就是为什么 `never` 有时也被称为 [uninhabited](https://link.juejin.cn/?target=https%3A%2F%2Fcs.stackexchange.com%2Fquestions%2F134215%2Fwhat-is-an-uninhabited-type) 类型或 [Bottom](https://link.juejin.cn/?target=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FBottom_type) 类型。

Bottom 类型是 [TypeScript 手册](https://link.juejin.cn/?target=https%3A%2F%2Fwww.typescriptlang.org%2Fdocs%2Fhandbook%2Ftypescript-in-5-minutes-func.html%23other-important-typescript-types) 对它的定义。当我们把 `never` 放一个完整[类型层次](https://link.juejin.cn/?target=https%3A%2F%2Fwww.zhenghao.io%2Fposts%2Ftype-hierarchy-tree%23the-bottom-of-the-tree)的树形结构图之后，看起来才比较有意义。

![image.png](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/12/202402102232677.webp)

就像在数学中我们使用**零**来表示没有的数量一样，我们需要一个类型来表示类型系统中的**不可能**。

## 如何使用 never 类型

### 案例：限制函数参数类型

由于我们永远无法给一个 `never` 类型赋值，因此我们可以使用它来对函数参数施加限制。

比如这个 `log` 函数的参数，他可以赋值任何类型，但是唯独不能赋值日期类型

```ts
// x 可以是任何类型，但不能是日期
function log(x) {
  console.log(x);
}
```

第一时间想到的是，我们可以写一个判断，如果 x 是类型日期就抛出一个错误

```ts
function log(x) {
  if (x instanceof Date) {
    throw new Error('x 不能是日期类型');
  }
  console.log(x);
}
```

但是你这样子做，也没毛病，就是用一点也不 TS，你把这个错误的发生事件延迟到了运行时，也就意味着你在编写代码的时候，在运行之前你是收不到任何的错误提示的

![image-20231210172351792](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/12/202402102233456.png)

可以看到上图中 TS 是不会报错的，只有当你运行了代码的时候你才发现这个错误发生了

![image-20231210172648389](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/12/202402102233792.png)

那么我们能不能运用 TS 的能力把这个错误提前到编译时呢？答案是当然可以，这就是 TS 的主要功能之一

现在我们把这个判断去掉，给 log 加上一个泛型 T，有了这个泛型参数 T 之后，我们可以对这个参数 x 进行进一步的约束，我们可以使用一个三目运算，看一下这个类型 T 是不是日期，如果说他是日期类型的话，那么这个 x 的类型就给他标注为 never 类型，就是绝不可能的类型，否则的话就是参数 T

![image-20231210173603467](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/12/202402102233438.png)

这样我们会发现当你给他传一个 Date 的时候，他的类型约束这里就满足 never 了，这个 x 就变成一个 never 类型，而 never 类型是不能接受一个 Date 类型的

这样写比较丑可以把这个类型给提出去

而且这个类型是通用的，任何需要去除 Date 类型都是可以使用 `BanDate<T>` 的

```ts
type BanDate<T> = T extends Date ? never : T;

// x 可以是任何类型，但不能是日期
function log<T>(x: BanDate<T>) {
  console.log(x);
}

log(new Date()); // 类型“Date”的参数不能赋给类型“never”的参数。ts(2345)
```

把错误提到编译时是 TS 的主要功能，得益于 TS 的类型检查，我们就可以尽早的发现这个问题，不能传递日期

![image-20231210174453213](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/12/202402102233953.png)

我们甚至可以更近一步，把这个类型做的再通用一点，可以去除掉任何类型

![image-20231210174710393](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/12/202402102233918.png)

这样我们就做出来一个更佳通用的类型
