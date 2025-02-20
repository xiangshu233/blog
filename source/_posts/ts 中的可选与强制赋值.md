---
date: 2021-08-24
updated: 2022-03-04
title: typeScript 中的 ! ?
categories: [TS]
tags: [typescript]
cover: https://03c068f.webp.li/i/2025/02/08/wfzdv-p6.jpg
references:
  - title: 'ts 官方文档'
    url: https://www.tslang.cn/docs/handbook/interfaces.html
  - title: ts 中的!和?用法
    url: https://blog.csdn.net/qq_39261142/article/details/108734647
---


## ！的用法

```ts
{
  // 用在赋值的内容时，使 null 和 undefined 类型可以赋值给其他类型并编译通过
  // 表示该变量值可空
  let y: number;
  let a: string;
  //  y = null		    //  无法通过编译
  //  y = undefined	    //  无法通过编译
  y = null!;
  y = undefined!;
  a = null!;

  console.log('y', y); // undefined
  console.log('a', a); // null
}
```

```ts
{
  interface IDemo {
    x?: number; // ? 表示 x 为可选的
  }

  let y: number;
  // 无法传递给类型为 number 的 y，因此需要用 !
  const demo = (param: IDemo) => {
    // 由于 x 为可选的，因此 param.x 的类型可能为  number | undefined
    y = param.x!; // 强制 赋值给 y
    return y;
  };
  // { x: 2 } 可传可不传，但是不能不传，可传空对象 {}，这里指的是接口参数表示可选
  console.log('demo----->', demo({ x: 2 })); // demo-----> 2
}
```
### 成员
```ts
// 因为接口 B 里面 name 被定义为可空的值，但是实际情况是不为空的，那么我们就可以
// 通过在 class 里面使用 ！，重新强调了 name 这个不为空值
class A implemented B {
  name!: string
}

interface B {
  name?: string
}
```

### 强制链式调用(断言)

```ts
// 这里 Error对象定义的stack是可选参数，如果这样写的话编译器会提示
// 出错 TS2532: Object is possibly 'undefined'.
new Error().stack.split('\n');

// 我们确信这个字段100%出现，那么就可以添加！，强调这个字段一定存在
new Error().stack!.split('\n');
```


## ? 用法

除了表示 `可选`参数外，常常用于防御性编程

```ts
{
  const a = { b: { c: 0 } } || {}; // 假设 a 是从后端拿到的一个对象类型数据
  const tableData = a.b.c; // 这样写不安全，无法确保 b 是否有值，如果为 空 则 b.c 会进行报错
  const testData = a?.b?.c; // 实际上就是相当于 const testData = a && a.b && a.b.c
  // 当使用 a 对象属性 a.b 时，如果无法确定 a 是否为空，则需要用 a?.b
  // 表示当 a 有值的时候才去访问 b 属性，没有值的时候就不去访问，如果不使用 ? 则会报错
}
```


```ts
{
  interface IDemo {
    x: number;
  }

  let y: number;

  // 由于函数参数可选，因此 parma 无法确定是否拥有
  // (parameter) parma: IDemo | undefined
  const demo2 = (parma?: IDemo): number => {
    // 所以无法正常使用 parma.x，使用 parma?.x 向编译器假设此时 parma 不为空且为 IDemo 类型，
    // 同时 parma?.x 无法保证非空，因此使用 parma?.x! 来保证了强制赋值并整体通过编译
    y = parma?.x!;
    console.log('parma?.x---->', parma?.x); //  parma?.x----> undefined | 7
    return y;
  };

  console.log('demo2----->无参', demo2()); //  demo2----->无参 undefined
  console.log('demo2----->有参', demo2({ x: 7 })); //  demo2----->有参 7
}
```

### 示例

```ts
{
  // 接口里的属性不全都是必须的。有些是只在某些条件下存在，或者根本不存在。
  interface SquareConfig {
    color?: string; // 表示可选
    width?: number; // 表示可选
  }
  // : { color: string; area: number } 表示返回值参数类型
  //  config? 表示参数也是可选
  const createSquare = (
    config?: SquareConfig
  ): { color: string; area: number } => {
    const newSquare = { color: 'red', area: 100 };

    if (config?.color) {
      newSquare.color = config.color;
    }
    if (config?.width) {
      newSquare.area = config.width * config.width;
    }
    return newSquare;
  };
  // 由于 createSquare 的参数对象中属性是可选的，所以可传部分属性，或者是不传，具体看接口对象属性
  console.log(
    'createSquare---->传部分参数属性',
    createSquare({ color: 'black' })
  ); // {color: "black", area: 100}
  console.log('createSquare---->传空对象参数', createSquare({})); // {color: "red", area: 100}
  console.log('createSquare---->无参', createSquare()); // {color: "red", area: 100}
}
```

## 结语

> 永远不要相信苦难是值得的，苦难就是苦难，苦难不会带来成功，苦难不值得追求，磨炼意志是因为苦难无法躲开
