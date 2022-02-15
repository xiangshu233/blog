---
date: 2021-08-11
title: typeScript 数据类型笔记
categories: [typeScript]
tags: [超集, typeScript]
cover: https://tva4.sinaimg.cn/mw690/006wuklaly1gjrf2170u8j31xs0u0kiq.jpg
---


## 数据类型 | 元组

- 概念：就是一个规定了元素数量和每个元素类型的“数组”，而每个元素的类型，可以不相同
- 语法：

```typescript
// let 元组名: [类型1, 类型2, 类型3] = [值1, 值2, 值3];
let tup1: [string, number, boolean] = ['哈哈~~', 18, true];
```

## 数据类型 | 枚举

- 问题：性别标识

![image-20210412165525442](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@0522e4baf08a27929b93006d258e0b6e7aea9d96/2021/04/12/564e9b115f76a6bb19eda8c2bc6dfafe.png)

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

## 数据类型 | void

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

## 数据类型 | 联合类型

- 概念 ：表示取值可以为多种类型中的一种

```typescript
// let 变量名：变量类型1 | 变量类型2 = 值;
```

- 接收 **prompt()** 函数的返回值

<img src="https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@4150b8bd7bc0cf3125155eba466500f5bffab5f3/2021/04/12/1d1a9b90785d182f93dbd91219d6121d.png" alt="image-20210412173113138" style="zoom:200%;" />

- 要么是**字符串**要么是**null**，这个时候就可以用联合类型

```typescript
let dName: string | null  = prompt('请输入小狗狗名字');
console.log('hello + dName');
```

## 数据类型 | 返回值和参数

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

![image-20210412175333783](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@db130433d034dff1b65285d734d4ca02c29cbbfc/2021/04/12/132c762e1154d7b27301691520c8cfd9.png)

- 函数必须定义 **返回值类型**，如果没有返回值， 则定义返回值类型为 **void**

## 数据类型 | 可选参数

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

## 数据类型 | 默认值

- 函数 默认值  			~~形参1?: 类型 = 默认值1~~ 		带默认值的参数，本身也是可选参数

```typescript
function test(city: string = '加拿大', phone: number = 1): String {
  return 'yes'
}
```

- 调用

  ![image-20210412181303380](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@73daabe41c6f2315a159e6e9e09d252806d0d8b7/2021/04/12/4ac173037b41d6f89368591d84cac8f1.png)

## Record

Record 用于定义一个对象的键值对

```ts
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