---
title: ts 中 type 与 interface 的区别
date: 2022-08-08 17:39
categories: [TS]
tags: [typescript, type, interface]
cover: https://03c068f.webp.li/i/2025/02/08/wg1ic-s9.jpg
banner: https://03c068f.webp.li/i/2025/02/08/wg1ic-s9.jpg
---

## 类型别名 type

类型别名用来给一个类型起个新名字，使用 type 创建类型别名，类型别名不仅可以用来表示基本类型，还可以用来表示对象类型、联合类型、元组和交集

```ts
// 基本类型
type userName = string;
// 联合类型
type userId = string | number;
// 数组类型
type arr = number[];
// 对象类型
type Person = {
    id: userId; // 可以使用定义类型
    name: userName;
    age: number;
    gender: string;
    isWebDev: boolean;
};
// 范型
type Tree<T> = { value: T };

const user: Person = {
    id: "901",
    name: "椿",
    age: 22,
    gender: "女",
    isWebDev: false,
};
const numbers: arr = [1, 8, 9];
```



## 接口 interface

接口是命名数据结构（例如对象）的另一种方式；与type 不同，interface仅限于描述对象类型，接口的声明语法也不同于类型别名的声明语法。

```ts
interface Person {
    id: userId;
    name: userName;
    age: number;
    gender: string;
    isWebDev: boolean;
}
```


![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2022/07/20220808174407.png)


## 语法不同

```ts
interface Point {
  x: number;
  y: number;
}
interface SetPoint {
  (x: number, y: number): void
}
type Point = {
  x: number
  y: number
}
type SetPoint = (x: number, y: number) => void
```

## 扩展 (继承)不同

扩展 (继承) 语法：interface 使用 extends，type 使用 &

```tS
// extends方式不同
interface PartialPointX { x: number }
interface Point extends PartialPointX { y: number }

type PartialPointX = { x: number }
// & 交叉类型
type Point = PartialPointX & { y: number }
```

两者也可以互相继承

```ts
// interface 继承 type
type Person{
    name:string
}
interface Student extends Person { stuNo: number }

// type 继承 interface
interface Person{
    name:string
}
type Student = Person & { stuNo: number }
```



## 同名合并

interface 支持，type 不支持（如果系统中存在两个使用 interface 定义的接口且同名的话，系统则会自动合并它们），如果存在两个同名 type 则会报错

```ts
// interface可以定义多次，并将被视为单个接口
// These two declarations become:
// interface Point { x: number; y: number }
interface Point { x: number }
interface Point { y: number }
```

## 描述类型

对象、函数两者都适用，但是 type 可以用于基础类型描述、联合类型、元组（简单类型用 type 来定义）

```ts
type test = number //基本类型
let num: test = 10
type userOjb = { name:string } // 对象
type getName = () => string  // 函数
type data = [number, string] // 元组
type numOrFun = userOjb | getName  // 联合类型

interface Common {
  name: string
}
interface Person<T> extends Common {
  age: T
  sex: string
}
type People<T> = {
  age: T
  sex: string
} & Common
// 联合类型
type P1 = Person<number> | People<number>
// 元组
type P2 = [Person<number>, People<number>]
```

## 映射类型

type 支持计算属性，生成映射类型，interface 不支持

场景： 有时候一个类型需要基于另外一个类型，但是你又不想拷贝一份，这个时候你可以考虑使用映射类型

```ts
// keys 联合类型
type Keys = 'firstName' | 'surName'

// DudeType 会遍历 keys 所有属性，然后设置为 string 类型
type DudeType = {
    [key in Keys]: string
}

const test: DudeType = {
    firstName:'Paerl'
    surName:'Grz'
}

// 报错
//interface DudeType2 {
//  [key in keys]: string
//}


// 带泛型的映射类型
// 在这个例子中，OptionsFlags 会遍历 Type 所有的属性，然后设置为布尔类型
type OptionsFlags<Type> = {
  [Property in keyof Type]: boolean
};

type FeatureFlags = {
  darkMode: () => void
  newUserProfile: () => void
};

type FeatureOptions = OptionsFlags<FeatureFlags>
// type FeatureOptions = {
//    darkMode: boolean
//    newUserProfile: boolean
// }


```

## interface 可以被 class 实现

interface 和 type 都可以实现一个函数的类型，但是  interface  可以被 `类class` 实现（implements），type 不行

```ts
interface SetPerson {
  (age: number, sex: string): void
}

type SetPeople = (age: number, sex: string) => void


let setPerson: SetPerson = function (age, sex) {}
let setPeople: SetPeople = function (age, sex) {}


class Config implements SetPerson {
  setPerson(age: number, sex: string) {
    // do nothing
  }
}
```

## type 可以配合 typeof

type 可以结合 `typeof` 使用，interface 不行

```ts
class Config {
  setPerson(age: number, sex: string) {
    // do nothing
  }
}
// 获取 Config class 的类型
type T = typeof Config

const C: T = class {
  setPerson(age: number, sex: string) {
    // do nothing
  }
}
```

## 总结

虽然 官方 中说几乎接口的所有特性都可以通过类型别名来实现，但建议优先选择接口，接口满足不了再使用类型别名，在 typescript 官网 Preferring Interfaces Over Intersections 有说明，具体内容如下：

大多数时候，对象类型的 简单 `类型别名` 的作用与 `接口` 非常相似

```ts
interface Foo { prop: string }

type Bar = { prop: string };
```

但是，一旦你需要组合两个或多个类型来实现其他类型时，你就可以选择使用接口扩展这些类型，或者使用类型别名将它们交叉在一个中(交叉类型)，这就是差异开始的时候。

接口创建一个单一的平面对象类型来检测属性冲突，这通常很重要！ 而交叉类型只是递归的进行属性合并，在某种情况下可能产生 never 类型

接口也始终显示得更好，而交叉类型做为其他交叉类型的一部分时，直观上表现不出来，还是会认为是不同基本类型的组合。

接口之间的类型关系会被缓存，而交叉类型会被看成组合起来的一个整体。

最后一个值得注意的区别是，在检查到目标类型之前会先检查每一个组分。

出于这个原因，建议使用 接口/扩展`interface/extends` 扩展类型而不是创建 交叉类型`&`。

```ts
- type Foo = Bar & Baz & {
-     someProp: string;
- }


+ interface Foo extends Bar, Baz {
+     someProp: string;
+ }
```

简单的说，接口更加符合 JavaScript 对象的工作方式，简单的说明下，当出现属性冲突时：

```ts
// 接口扩展
interface Sister {
    sex: number;
}

interface SisterAn extends Sister {
    sex: string;
}

// interface SisterAn
// 接口“SisterAn”错误扩展接口“Sister”。
// 属性“sex”的类型不兼容。
// 不能将类型“string”分配给类型“number”。ts(2430)
// “SisterAn”已声明，但从未使用过。ts(6196)


// 交叉类型
type Sister1 = {
    sex: number;
}

type Sister2 = {
    sex: string;
}

type SisterAn = Sister1 & Sister2;
// 不报错，此时的 SisterAn 是一个'number & string'类型，也就是 never
```

以上内容均来自 Google
