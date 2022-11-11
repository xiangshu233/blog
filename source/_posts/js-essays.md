---
date: 2021-01-06
title: js 随笔记录
categories: [JS]
tags: [js, 笔记]
cover: https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@c24a79e61ff7cd15b6d1ce8c1f9e4a6b256a73d0/2022/02/15/1847503944e96b962bfeb3fc7b780a22.png
---

## 获取当前日期的前后日期

```js
/**
 * getAfterDateStr 获取当前日期 的 前后日期
 * @param {number} i 正数为之后的日期 负数为之前的日期 0（不传）为当前日期
 */
export function getAfterDateStr(i = 0) {
	let date = new Date();
	const curDateAfter = date.setDate(date.getDate() + i);
	date = new Date(curDateAfter);

	const	year = date.getFullYear();
	const	month = date.getMonth() + 1;
	const	day = date.getDate();
	return `${year}-${(month < 10 ? '0' + (month) : month)}-${(day < 10 ? '0' + day : day)}`;
}
```

## js 中去除字符串中所有HTML标签

适用场景：对于获取了一大堆字符串又不想要里面的 html 标签

```js
const chars = '<p align="center"><br></p><p align="center"></p><p align="center"></p><p><b>产品特点</b></p><p>主要用于基坑内支撑，采矿支柱及其他支撑设备的内力监测。</p><p><b> </b></p><p><b>技术参数</b></p><table border="1" cellspacing="0" cellpadding="0" align="left" width="423"><tbody><tr><td width="168">'

// 截取html标签,截取空格等特殊标签
const detail = chars.replace(/<[^>]+>/g,"").replace(/&nbsp;/ig,"").substring(0, 55);

// 产品特点主要用于基坑内支撑，采矿支柱及其他支撑设备的内力监测...
console.log(detail);
```

## 过滤掉数组里的重复值

ES6 新特性 Set，不过它并不能很好处理非基本类型的数组，以下只针对基本数据类型

```js
const chars = [
    'AAA'
    'BBB'
    'AAA'
    'CCC'
    'DDD'
]
const filter = Array.form(new Set(chars)); // chars ['AAA', 'BBB', 'CCC', 'DDD']
const filter2 = [...new Set(chars)] // chars ['AAA', 'BBB', 'CCC', 'DDD']
```

箭头函数与普通函数对比

```js
return this.allLinesXY.filter((item) => item.itemIndex === 0);

return this.allLinesXY.filter(function test(item) {
	return item.itemIndex === 0;
});
```

## ES6 剩余参数学习记录

剩余参数将`多个元素`合并成一个`数组`

应用场景一：假设我们有这么一组数据，将第一个值赋值给`班主任`变量，第二个值赋值给`班长`变量，剩下的归为`学生`这时我们就可以使用剩余参数`...`

```js
const list = ['班主任','班长','学生1','学生2','学生3','学生4'];
const [classTeacher, Monitor, students] = list;
console.log(classTeacher, Monitor, students);	// '班主任','班长','学生1'
// 剩余参数
const [classTeacher, Monitor, ...students] = list;
console.log(classTeacher, Monitor, students);	// 班主任 班长 (4) ['学生1', '学生2', '学生3', '学生4']
```

应用场景二：将传入的多个数字进行排序（因为传入的参数个数是不确定的，所以剩余参数就派上用场了）

```js
// ...nums不管你传入了多少个参数，都放到nums数组中
function sortNums(...nums){
	if(nums.length === 0) {
        return []
    }else{
        // js的sort是有问题的，需要改造一下 正序排列a-b,倒序排列b-a
        return nums.sort((a,b) =>  a - b )
    }
}
console.log(sortNums(1,2,10))   // (3) [1, 2, 10]
```

## 扩展参数（将一个数组打散）

应用场景一：将班主任、班长、学生数组合并成一个数组

```js
const classTeacher = '班主任'
const monitor = '班长'
const students = ['学生1','学生2','学生3','学生4']
const team = [boss,monitor,...students]
console.log(team)    // ['班主任', '班长', '学生1','学生2','学生3','学生4']
```

应用场景二：将两个数组合并为一个数组

```js
const food = ["香辣鸡腿堡", "墨西哥鸡肉卷", "香辣烤翅"]
const drink = ["百事可乐", "橙汁"]
// concat方法
// const kfc = food.concat(drink)
// console.log(kfc)  // (5) ["香辣鸡腿堡", "墨西哥鸡肉卷", "香辣烤翅", "百事可乐", "橙汁"]
const kfc = [...food, ...drink]
console.log(kfc)  // (5) ["香辣鸡腿堡", "墨西哥鸡肉卷", "香辣烤翅", "百事可乐", "橙汁"]
```

还可以在新生成的数组中添加数据：

```js
const kfc = [...food,"圣代", "吗媞娜", ...drink]
console.log(kfc)  // (5) ["香辣鸡腿堡", "墨西哥鸡肉卷", "香辣烤翅", "百事可乐", "橙汁"]
```

## const 声明

> `const`与`let`的行为一样，区别的是：使用`const`声明的时候必须同时初始化变量，且不能修改定以后的变量值，否则报错
>
> 但是`const`声明限制只适用于它指向的变量引用。如果`const`声明的变量是对象，改变他内部的属性是不会违反`const`的限制。

```js
const  phone = 'IPhone 12';
phone = 'IPhone 13' // 报错
console.log(phone)


const obj = {
    name: '张小飞',
    age:22
}

obj.name = '张飞';

console.log(obj) // { name: '张飞', age: 22 }
```

# JS基础类型和引用类型

JS的数据类型分为`基础类型`和`引用类型`
区别：
`ES5`中的`基础类型`包括：`number`、`string`、`null`、`undefined`、`boolean`。`ES6`新增了一种基础类型`symbol`,基础类型的存储是存放在`栈`中，原因是基础类型存储的空间很小，存放在`栈`中方便查找，且不易于改变

```js
const a = 10;
let b = a;
b = 20;
console.log(a); // 10
```

![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@409b7d2647895ae5385d27a6d12c1bce8cd1e36f/2020/10/21/2267397827b1e1ba7fcdc291b66bd8a6.png)

引用类型使之多个值构成的对象，也就是对象类型比如：`Object`、`Array`、`Function`、`Data`等，JS的引用数据类型是存储在`堆`中，也就是说存储的变量处的值是一个`指针（point）`，指向存储对象的内存地址。存在`堆`中的原因是：引用的值的大小会改变，所以不能放在`栈`中，否则会降低变量查询的速度

![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@2fb8659ee73c8f52c23708473765df2103ed7d14/2020/10/21/df393656e3fc43c020f259fba9cb9924.png)

```js
const obj1 = new Object();
const obj2 = obj1;
obj2.name = "我有名字了";
console.log(obj1.name); // 我有名字了
```

## 类型推断

> 判断一个`变量`的类型是什么类型的时候可以通过`typeof`判断
>
> 但是`typeof`对引用类型是没有作用的，当我们想知道一个对象实例是什么对象类型时，可以通过`instanceof`来判断

```js
var  name = '张三'
var  price = 222

console.log(typeof name)  // string
console.log(typeof price)  // number
// 对象实例
class Student{
    constructor(name,age) {
        this.name = name;
        this.age = age
    }
}

class Animal {
    constructor(name,age) {
        this.name = name;
        this.age = age
    }
}
// 对象实例推断
const A = new Student('菲菲',22)
const B = new Animal('大黄，2')
console.log(A instanceof Animal)  // false
console.log(B instanceof Animal)  // true
```

## 字符串截取

保留小数点后六位

第一种：

```js
const str = "117.19828632156754,39.12896448609261;117.19754514351357,39.12933467928258;";
console.log('第一种：', jiequ(str)); // ["117.198286", "39.128964", "117.197545", "39.129334"]

function jiequ(str) {
	const list = str.split(/[,;]/); // 本次分割会有一个空数组
	list.pop(); // 删除最后一个元素
	for (let i = 0; i < list.length; i++) {
		const index = list[i].indexOf('.');	// 检索每个元素 '.' 首次出现的位置 3
		list[i] = list[i].substr(0,index + 7);  // 0, 3+7  即 117.198286
	}
	return list
},
```

第二种：

此方法会将 `Number` 进行四舍五入

```js
const str = "117.19828632156754,39.12896448609261;117.19754514351357,39.12933467928258;";
console.log('第二种：', jiequ2(str)); // ["117.198286", "39.128964", "117.197545", "39.129334"]

function jiequ2(str) {
	const list = str.split(/[,;]/)
	list.pop();
	return list.map((v) => (v * 1).toFixed(6))
},
```

## 一维数组转二维数组

```js
/**
 * @method 一维数组转二、三、四，五维数组
 * @param {*} num
 * @param {*} arr
 * @date 2020-11-10 13:48:05
*/
function arrTrans(num, arr) {
	const newArr = [];
	while(arr.length > 0) {
		newArr.push(arr.splice(0, num));
	}
	return newArr;
}
const str = '117.218964,31.842125,117.212655,31.841815,117.208621,31.842726,117.207677,31.843911';
const arr = JSON.parse(JSON.stringify(str.split(',')).replaceAll("\"", "")); // \" 转义 “
// arr: (8) [117.218964, 31.842125, 117.212655, 31.841815, 117.208621, 31.842726, 117.207677, 31.843911]
console.log('arrTrans:',arrTrans(2, arr));
// arrTrans: (4) [Array(2), Array(2), Array(2), Array(2)]
```

## 例子

### 求数组最大值

`apply()` 则是参数数组（或者类数组）—— 尽管如此，在这些参数传递给调用函数时，仍然是以参数列表的形式传递的（这一点很重要）。

```js
const arr = [1, 2, 3, 4, 5];

Math.max.apply(null, arr);  // 5
// 也可以使用延展操作符，因为 max() 只接受参数列表
const arr2 = [1, 3, 2];
Math.max(...arr2);
```

### 借用其他object里的方法

在javascript OOP中，我们经常会这样定义：

```js
// 原型模式
function cat() {

}
cat.prototype = {
    food: 'fish',
    say: function() {
        console.log("I love "+this.food);
    }
}
var blackCat = new cat;
blackCat.say(); // I love fish

// es6 类的写法
class cat {
    constructor(food){
        this.food = food;
    }
    say() {
        return `I love ${this.food}`;
    }
}
const blackCat = new cat ("fish");
console.log(blackCat.say()); // I love fish

const whiteDog = {food: "bone"};
console.log(blackCat.say.call(whiteDog)); // I love bone
```

但是如果我们有一个对象 `whiteDog = {food:"bone"}`，我们不想对它重新定义 `say` 方法，那么我们可以通过 `call()` 或 `apply()` 用 `blackCat`的 `say()` 方法：`blackCat.say.call(whiteDog)`；

所以，可以看出 `call()` 和 `apply()` 是为了动态改变 `this` 而出现的，当一个 `object` 没有某个方法，但是其他的有，我们可以借助 `call()` 或`apply()` 用其它对象的方法来操作。

## 如何在多个分隔符上分割字符串

假设我们要在分隔符上分割字符串，第一想到的就是使用 `split` 方法，`split` 可以同时拆分多个分隔符, 使用正则表达式就可以实现：

```js
// 用逗号(,)和分号(;)分开。

const list = "apples,bananas;cherries"
const fruits = list.split(/[,;]/)
console.log(fruits); // ["apples", "bananas", "cherries"]
```

## 以YYYY-MM-DD的方式，输出当天的日期

```js
const d = new Date();
const year = d.getFullYear();
let month = d.getMonth() + 1;
month = month < 10 ? `0${month}` : month;
let day = d.getDate();
day = day < 10 ? `0${day}` : day;
console.log(`${year}-${month}-${day}`); // 2021-01-05
```

未完！

## 结语
> 我并不怕死，但若是受伤流血，变成残废，我可不要
