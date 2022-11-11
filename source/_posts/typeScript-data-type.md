---
date: 2021-08-11
updated: 2022-07-19
title: typeScript 数据类型笔记
categories: [TS]
tags: [超集, typeScript]
cover: https://tva3.sinaimg.cn/large/006wuklaly8h5ap98jg85j315i0ndqaj.jpg
---


## 元组(tuple)

- 概念：就是一个规定了元素数量和每个元素类型的“数组”，而每个元素的类型，可以不相同
- 语法：

```typescript
// let 元组名: [类型1, 类型2, 类型3] = [值1, 值2, 值3];
let tup1: [string, number, boolean] = ['哈哈~~', 18, true];

// 只能输入两个，超出即报错
const tupleType: [string, number] = ['爱丽丝', 18]

// error: 不能将类型“[string, number, string]”分配给类型“[string, number]”
// 源具有 3 个元素，但目标仅允许 2 个
const tupleType2: [string, number] = ['爱丽丝', 18, 'alice']
```

## 枚举

- 问题：性别标识

![image-20210412165525442](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@0522e4baf08a27929b93006d258e0b6e7aea9d96/2021/04/12/564e9b115f76a6bb19eda8c2bc6dfafe.png)

- 声明语法

```typescript
//	enum 枚举名 {
//    	枚举项1 = 枚举值1,
//    	枚举项2 = 枚举值2,
//   	...
//	}


// 声明性别枚举
enum Gender {
  Boy = 1,
  Girl = 2,
  Unknown = 3
}
// 枚举项一般用英文和数字，而枚举值用整形数字

// 创建 用户性别变量
let usrSex: Gender = Gender.Boy;

// 判断 变量中的性别是否为 Boy
if (usrSex == Gender.Boy) {
  console.log(usrSex); // 1
} else {
  console.log(usrSex) // 2 or 3
}
```

- 使用默认枚举值

```typescript
//	enum 枚举名 {
//    	枚举项1,
//    	枚举项2,
//   	...
//	}

// 枚举值将自动生成从 0 开始的数值
enum GunType {
  M4A1,	// -> 0
  AK47,	// -> 1
  Goza	// -> 2
}
```

## void

- 概念：void 代表没有类型，一般用在无返回值函数
- 语法

```typescript
function sayHi1(): string {
  return 'hi,你好啊'
}
let re1 = sayHi1();


function sayHi1(): void {
  console.log('讨厌');
}
sayHi2();
```

## 联合类型

- 概念 ：表示取值可以为多种类型中的一种

```typescript
// let 变量名：变量类型1 | 变量类型2 = 值;
```

- 接收 **prompt()** 函数的返回值

<img src="https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@4150b8bd7bc0cf3125155eba466500f5bffab5f3/2021/04/12/1d1a9b90785d182f93dbd91219d6121d.png" alt="image-20210412173113138" style="zoom:200%;" />

- 要么是**字符串**要么是**null**，这个时候就可以用联合类型

```typescript
let dName: string | null  = prompt('请输入小狗狗名字');
console.log('hello + dName');
```

## 返回值和参数

```typescript
// 函数 返回值类型
function sayHi(): string {
  return 'hi~hello'
}
let res: string = sayHi();

// 函数 形参类型
function from(cityName: string): void {
  // void 无返回值
  console.log('你来自哪里');
  console.log(`我来自${cityName}`);
}
// ts 中 实参 必须和 形参 的 类型一致 数量一致
from('加拿大');
//from(); // 报错没传参
```

![image-20210412175333783](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@db130433d034dff1b65285d734d4ca02c29cbbfc/2021/04/12/132c762e1154d7b27301691520c8cfd9.png)

- 函数必须定义 **返回值类型**，如果没有返回值， 则定义返回值类型为 **void**

## 可选参数

- 函数 可选参数

```typescript
// 可选参数的实参可传也可不传，只需要在形参后面加一个问号即可
// function 函数名(形参?: 类型): 返回值类型 {
// }

// !表示一定存在
// function 函数名(形参!: 类型): 返回值类型 {
// }
```

- 调用

  传参 		函数名();

  不传参 	函数名(实参值);

## 默认值

- 函数 默认值  			~~形参1?: 类型 = 默认值1~~ 		带默认值的参数，本身也是可选参数

```typescript
function test(city: string = '加拿大', phone: number = 1): String {
  return 'yes'
}
```
- 调用

  ![image-20210412181303380](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@73daabe41c6f2315a159e6e9e09d252806d0d8b7/2021/04/12/4ac173037b41d6f89368591d84cac8f1.png)

## as 类型断言

直白的说就是我确定肯定一定知道它是个什么类型，不需要 ts 帮我检查

```typescript
// “尖括号” 语法
let someValue: any = "this is a string";
let strLength: number = (<string>someValue).length;

// as 语法
let someValue2: any = "this is a string";
let strLength2: number = (someValue2 as string).length;
```

## ! 非空断言操作符

 `!` 可以用于断言操作对象是非 null 和非 undefined 类型。具体而言，a! 将从 a 值域中排除 null 和 undefined
 我的建议是 慎用，使用 `!` 即告诉 ts 我的值绝不肯能为空，当然我们要为自己的行为负责，如果欺骗 ts 导致 NPE（Null Pointer Exception）那就不是 ts 的锅了。

```typescript
const a: number | null | undefined = undefined;
const b: number = a; // Error 不能将类型“undefined”分配给类型“number”。ts(2322)
console.log(b);

// ok
const c: number = a!;
```

## ? 可选链运算符
可选链说白了就是我们编写代码时如果遇到 null 或 undefined 就可以立即停止某些表达式的运行（防御性编程）。可选链的核心是新的 `?.` 运算符，它支持以下语法：

```typescript
obj?.prop
obj?.[expr]
arr?.[index]
func?.(args)
```

```typescript
// 示例
const val = a?.b;

function tryGetArrayElement<T>(arr?: T[], index: number = 0) {
  return arr?.[index];
}

let result = obj.customMethod?.();
```

## ?? 空值合并运算符
当左侧操作数为 null 或 undefined 时，其返回右侧的操作数，否则返回左侧的操作数

```typescript
const foo = null ?? 'default string';
console.log(foo); // 输出："default string"

const baz = 0 ?? 42;
console.log(baz); // 输出：0
```
`??` 与逻辑或 `||` 运算符不同，逻辑或 `||` 会在左操作数为 false 值时返回右侧操作数，如果你使用 || 来为某些变量设置默认的值时，你可能会遇到意料之外的行为。比如为 false 值（''、NaN 或 0）时
```typescript
const compProps = props.column?.editComponentProps ?? {};
const editComponent = props.column?.editComponent ?? null;
```

`??` 不能与 `&&` 或 `||` 操作符共用

```typescript
// 不能在不使用括号的情况下混用 "||" 和 "??" 操作。ts(5076)
const a = null || undefined ?? "foo";

// ok 要使用括号表格优先级
const a = (null || undefined) ?? 'foo';
```
也算是防御性编程一种吧






## Record

Record 用于定义一个对象的键值对

```typescript
interface PageInfo {
  title: string;
}

type Page = "home" | "about" | "contact";

const nav: Record<Page, PageInfo> = {
  home: { title: "home" },
  about: { title: "about" },
  contact: { title: "contact" }
};
```

Record 后面的泛型就是对象键和值的类型。

示例：比如我需要一个对象，有 ABC 三个属性，属性的值必须是数字，那么就这么写

```ts
type keys = 'A' | 'B' | 'C'
const result: Record<keys, number> = {
  A: 1,
  B: 2,
  C: 3
}
```


## 结语
> 每天下班累的只想躺着什么也不干，哪怕这一天其实没有很忙，但是那种疲惫却是避开肌肉筋骨，直到心神
