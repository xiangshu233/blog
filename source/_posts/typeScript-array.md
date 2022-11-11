---
title: typeScript 中的数组类型定义
date: 2022-03-03
categories: [TS]
tags: [二维数组, array, typeScript]
cover: https://tva4.sinaimg.cn/large/006wuklaly8h5apkbhc2hj31hc0u0dsr.jpg
banner: https://tva4.sinaimg.cn/large/006wuklaly8h5apkbhc2hj31hc0u0dsr.jpg
---

## 声明和初始化数组

在 `TypeScript` 中声明和初始化数组也很简单，和声明数字类型和字符串类型的变量也差不多，只不过在指定数组类型时要在类型后面加上一个中括号 `[]`

语法格式

```ts
const array_name: dataype[] = [val, val2];
```

示例

声明一个 `string` 类型的数组

```ts
const character: string[] = ["杨过", "小龙女"];
```

## 一维数组类型

声明一个 `number` 类型的数组

```ts
const numb: Number[] = [2, 4, 6, 8];
// 等同于
const numb2: Array<number> = [2, 4, 6, 8];
```

声明一个 `string` 类型的数组

```ts
const str: String[] = ['西安', '北京', '上海'];
// 等同于
const str2: Array<string> = ['西安', '北京', '上海'];
```

声明一个 `string` 或者 `number` 类型的数组

```ts
const array2: (string | number)[] = ['孟浩然', 99];
// 等同于
const array: Array<string | number> = ['孟浩然', 99];
```

除了使用中括号 `[]` 的方法来声明数组，你还可以使用 `数组泛型` 来定义数组

语法格式

```ts
const array_name: Array<dataype> = [val, val2];
```

示例

声明一个 `number` 类型的数组

```js
const numArr: Array<number> = [1, 2, 3, 4, 5];
```

## 多维数组类型

TypeScript 支持多维数组。一个数组的元素可以是另外一个数组，这样就构成了多维数组。多维数组的最简单形式是二维数组。

语法格式

```ts
const array_name: Array<Array<datatype>> = [[val1, val2, val3],[v1, v2, v3]];
// 等同于
const array_name: datatype[][] = [[val1, val2, val3]];
```

声明一个二维数组

```ts
const test: Array<Array<string>> = [['狮子头'], ['清蒸鲈鱼']];
// 等同于
const test: string[][] = [['狮子头', '清蒸鲈鱼', '鲜椒牛蛙'], ['北京烤鸭'], ['地锅鸡', '饿了']];
```

{% note color:yellow 注意： 以下示例中**类型**在数组中的，则会限制内层数组的元素数量 %}


`Array<[string]>` : 表示内层数组的元素是 `string` 类型，限制元素数量是 1 个，输入多个会报错

```ts
const test3: Array<[string]> = [['甘雨'], ['我的']];
// 等同于
const test3: [string][] = [['甘雨']];


// error
// 不能将类型“[string, string]”分配给类型“[string]”。
// 源具有 2 个元素，但目标仅允许 1 个。
const test3: [string][] = [['甘雨', '我的']];
```

`Array<[string, string]>` ： 表示内层数组的元素是 `string` 类型，限制元素数量是 2 个

```ts
const test5: Array<[string, string]> = [['刻晴', '八重神子'], ['雷电', '莫娜']];
// 等同于
const test5: [string, string][] = [['刻晴', '八重神子'], ['雷电', '莫娜']];
```
{% note color:blue 建议： 在定义数组类型的时候使用**数组泛型**定义，这样显得更直观一点 %}
