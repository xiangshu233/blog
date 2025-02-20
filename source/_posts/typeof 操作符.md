---
date: 2021-10-12
title: typeScript typeof 操作符
categories: [TS]
tags: [typescript, typeof操作符]
cover: https://03c068f.webp.li/i/2025/02/08/x4sb0-lf.jpg
banner: https://03c068f.webp.li/i/2025/02/08/x4sb0-lf.jpg
references:
  - title: '面向 Type 编程 -- Typescript 类型和类型操作(一)'
    url: https://zhuanlan.zhihu.com/p/311150643
  - title: ts 官方文档
    url: https://www.tslang.cn/docs/handbook/interfaces.html
---


## js 表达式中的 typeof 用来返回一个变量的基本数据类型

如 string、number、function、object

```ts
typeof 1; // number
typeof true; // boolean
typeof 'hello world'; // string
typeof function () {}; // function
typeof {}; // 'object' 在 JavaScript 中所有数据类型严格意义上都是对象
typeof []; // 'object'
typeof null; // 'object'
typeof undefined; // 'undefined'
typeof new RegExp('1'); // 'object'
typeof Symbol(); // symbol
```

## 在 ts 中，typeof 可以获取变量的声明类型

1. `typeof` 在 `ts` 中用来返回一个 `变量` 的 `声明类型`，如果 `不存在`，则获取该 `类型` 的 `推论类型`

2. `typeof` 在上下文的作用决定了 `返回值` 是 `类型查询` / `还是表达式`。前者返回的是 `类型`（types）

后者返回 `变量的类型值`（values）

列如在 `console.log()`、`if判断`、变量声明 `let` / `const` 等操作中 `typeof` 返回 `变量` 的 `类型` 的 `字符串名称`

而在 `typeof` 的限制下，`typeof` 返回变量的类型

```ts
const n: number = 1;
type TN = typeof n; // 返回变量的类型 type TN = number
console.log('typeof n', typeof n); // 返回变量的类型的字符串名称 `number`
const a: TN = 100; // TN 返回为 number 类型，可直接用作类型
//
//
const an: string[] = ['2'];
type TA = typeof an; // TA = number[]
const b: TA = ['9'];
```

这里有一点需要说明，不能单纯的通过关键字，判断 `typeof` 在上下文中的作用，而是综合的来判断是属于表达式还是类型查询。比如下面的例子中，虽然出现了 `let` 关键字，但是很明显 `typeof` 出现在类型声明的位置上，因此这里是类型查询。

```ts
let s: string = 'hello';
let s2: typeof s = 'world'; // typeof s = string
let s3: typeof s = 123; // 错误，不能将类型“number”分配给类型“string”
```

`typeof s ` 返回 `s` 的声明类型 `string`

> typeof 操作符用于获取变量的类型。因此这个操作符的后面接的始终是一个变量，且需要运用到类型定义当中
>
> ```ts
> // type TD = typeof 's' // 错误 应为标识符。ts(1003)
> let asoul = 's';
> type AST = typeof asoul; // 正确 type AST = string
> ```

如果 typeof 接收的变量是对象，则 typeo 返回的类型不是 object，而是同样结构的类型字面量

```ts
let obj = {
  name: 'zhangsan',
  age: 28,
  male: true,
  child: {
    name: 'lisi',
    age: 15
  },
  run: () => 'run'
};

type TO = typeof obj;

// 等同于
// type TO = {
//   name: string;
//   age: number;
//   male: boolean;
//   child: {
//     name: string;
//     age: number;
//   };
//   run: () => string;
// }
```

## 如果变量没有声明类型，typeof 返回变量的推断类型

```ts
let hello = 'hello world'; // ts 会自动推断为 string 类型 (let hello: string)
type TS = typeof hello; // 返回变量的推断类型 type TS = string
hello = 'hey,guys'; // let 声明的 hello 可以被重新分配
```



## tips: 什么是字面量类型？

```ts
let box1: string; //  box1 变量是 string 类型的，可以赋值任何字符串
box1 = 'aaa';
box1 = 'bbb';
```
如果定义一个变量 `box2` 并指定它的字面量类型为 `'123'`
那么它只能赋值 `'123'` ，其他类型一定报错，`'456'` 都不行

```ts
let box2: '123'; // let box2: "123"
box2 = '123';
box2 = '456'; // 不能将类型“"456"”分配给类型“"123"”
```

换个角度，`const` 声明的 `变量` 是不可更改的，所以 `ts` 会自动把该变量推断成 `字面量类型`

```ts
const box3 = '456'; // 此时 box3 的类型为字面量 '456'，无法为 box3 重新分配变量。 const box3: "456"
// box3 = '123'  // 无法分配到 "box3" ，因为它是常数。
```


有时候，我们希望变量是常量，不允许重新分配，可使用 `const` 来定义变量，此时，`ts` 基于类型推断， 返回的类型是字面量类型（注意：字面量类型不等于字符串），如下：

```ts
const world = 'the world is big'; // 返回的是字面量   const world: "the world is big"
type NW = typeof world; // type NW = "the world is big"
// world = 'the wrld'  // 无法分配到 "world" ，因为它是常数。
```

`ts 3.4` 引入了一种新的字面量构造方式，`const` 断言。在 `const` 断言的作用下，即使是 `let` 声明也可以限制类型扩展，变量不能重新分配，`typeof` 返回变量`s5` 字面量类型 `'hi'`

```ts
let s5 = 'hi' as const;
type CS = typeof s5; // type CS = "hi"
// s5 = 'hello'  // 不能将类型“"hello"”分配给类型“"hi"”
```

因为，当我们使用 `const` 断言构造新的字面量表达式时，向编程语言发出以下信号：

表达式中的任何字面量类型都不应该被扩展

对象字面量的属性，将使用 `readonly` 修饰

数组字面量将变成 `readonly` 元组

```ts
let x = 'hello' as const;
type X = typeof x; // type X = "hello"

let y = [10, 20] as const;
type Y = typeof y; // type Y = readonly [10, 20]

let z = { text: 'hello' } as const;
type Z = typeof z; // let z: { readonly text: "hello"; }
```

如果变量声明了 `类型` ，推断不受 `const` 的影响，唯一不同的是：`typeof` 返回 `s6` 的声明类型 `string`，而不是字面量类型 `'hello'`，但是变量依然不能重新分配

```ts
const s6: string = 'hello';
type TS6 = typeof s6; // type TS6 = string
// s6 = 'world'  // 无法分配到 "s6" ，因为它是常数
```

`boolean` 类型是个特殊的存在，`typeof` 作用下将被收窄（现在还没搞懂这个收窄啥意思，有知道的评论可以教教我吗）

无论有无类型都将返回字面量 `true/false`

```ts
let b = true; // 推断 b:boolean
type TB = typeof b; // TB = true   由于作用被收窄不再返回类型
b = false;
type TB1 = typeof b; // TB1 = false

// string
let hello = 'hello world';
type TS = typeof hello; // type TS = string

// 无论有无类型都将返回字面量`true/false`
let b1: boolean = false;
type TB3 = typeof b1; // TB3 = false
b1 = true;
type TB4 = typeof b1; // TB4 = true
```
