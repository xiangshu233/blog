---
title: js 中的 call、apply、bind 笔记
date: 2022-08-31 17:52
categories: [JS]
tags: [call, apply, bind]
cover: https://cdn.xiangshu233.cn/post/banner/wallhaven-mdwv39.jpg
banner: https://cdn.xiangshu233.cn/post/banner/wallhaven-mdwv39.jpg
---

## apply call

`Function.prototype.apply()`、`Function.prototype.call()`

`call()` 和 `apply()` 是 `Function` 的方法，它的第一个参数是 `this`，第二个参数是是给 调用函数 传递的参数。`call ` 和 `apply()` 都是为了改变某个函数运行时的 `context` 即上下文而存在的。

换句话说，就是为了改变函数体内部 `this` 的指向。

`call()` 需要把参数按顺序传递进去，而 `apply()` 则是 把参数放在数组里。他俩都是调用后立刻执行的

例如，有一个函数 `fun` 定义如下：

```js
window.color = "red";

const fun = function() {
    alert(this.color);
};

const newColor = {color: "blue"};

fun.call(this); // this 为 red
fun.call(window); // red
fun.call();
fun.call(null);
fun.call(undefined);
fun.call(newColor); // blue
```

其中

第一个参数 `this` 是你想指定的上下文，他可以任何一个 `js` 对象。

第二个参数是给 `调用函数` 传递的参数（如果调用函数没有参数就不传）。


{% note color:yellow 注：
如果 `call()` 方法没有参数，或者参数为 `null` 或 `undefined`，则等同于指向全局对象。另外箭头函数无法使用 `call()` `apply()` `bind()`，因为箭头函数中的 `this` 永远指向函数外最近的那个 `this`，因此不会起作用。
%}

返回值：使用调用者提供的 `this` 值和参数调用该函数的返回值。若该方法没有返回值，则返回 `undefined`。


![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets/2022/08/20220831115843.png)


**有参数的情况下**

```js
window.name = 'mark'
window.age = 18

const fun = function(from, to) {
    console.log(`姓名 ${this.name} 年龄 ${this.age} 来自 ${from} 去往 ${to}`);
}

fun('北京','上海') // mark 年龄 18 来自 北京 去往 上海

const obj = {name:'xiaoMa', age: 22}

// call 第一个参数为 this 后面的是 fun 需要的参数
fun.call(obj,'北京','上海') // xiaoMa 年龄 22 来自 北京 去往 上海
// apply 和 call 的作用一样，唯一的区别是 fun 的参数是放在数组中
fun.apply (obj, ['北京','上海']) // xiaoMa 年龄 22 来自 北京 去往 上海
```

JavaScript 中，某个函数的参数数量是不固定的，因此要说适用条件的话，当你的参数明确知道数量时，用`call()`，而不确定的时候，用`apply()`，然后把参数 `push` 进数组传递进去。

## bind

`Function.prototype.bind()`

`bind()` 方法返回一个绑定了 `this` 的新函数（原函数的拷贝），在 `bind()` 被调用时，这个新函数的 `this` 被指定为`bind()` 的第一个参数，而其余参数将作为新函数的参数，供调用时使用。

`call()` 和 `apply()` 它们两个是改变 `this` 的指向之后立即调用该函数，而 `bind()` 则不同，它是创建一个新函数，我们必须手动去调用它。

通俗讲就是通过`bind()` 方法，强制绑定了 `this`，绑定后的 `this` 不可改变

示例1：

![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets/2022/08/bind1.png)
![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets/2022/08/bind2.png)


示例2：

```js
const d = new Date();
d.getTime() // 1481869925657

const print = d.getTime;
print() // Uncaught TypeError: this is not a Date object.
```

上面代码中，我们将 `d.getTime()` 方法赋给变量 `print`，然后调用 `print()` 就报错了。这是因为 `getTime()` 方法内部的 `this`，绑定 `Date` 对象的实例，赋给变量 `print` 以后，内部的 `this` 已经不指向 `Date` 对象的实例了。

`bind()` 方法可以解决这个问题。

```js
const print = d.getTime.bind(d);
print() // 1481869925657
```

上面代码中，`bind()` 方法将 `getTime()` 方法内部的 `this` 绑定到 `d `对象，这时就可以安全地将这个方法赋值给其他变量了。

`bind` 方法的参数就是所要绑定 `this` 的对象，下面是一个更清晰的例子。

```js
const counter = {
  count: 0,
  inc: function () {
    this.count++;
  }
};

const func = counter.inc.bind(counter);
func();
counter.count // 1
```

上面代码中，`counter.inc` 方法被赋值给变量 `func`。这时必须用 `bind()` 方法将 `inc()` 内部的 `this` 绑定到 `counter`，否则则会报错

`this` 绑定其他对象也是可以的

```js
const counter = {
  count: 0,
  inc: function () {
    this.count++;
  }
};
const obj = {
    count:100
};

const func = counter.inc.bind(obj);

func();

obj.count // 101
```

上面的代码中，`bind()` 方法将 `inc()` 内部的 `this` 绑定到 `obj` 对象。调用 `func()` 后，递增的就是 `obj` 内部的 `count` 属性。

`bind()`还可以接受更多的参数，将这些参数绑定原函数的参数。

```js
const add = function (x, y) {
  return x * this.m + y * this.n;
}

const obj = {
  m: 2,
  n: 2
};

const newAdd = add.bind(obj, 5);
newAdd(5) // 20
```

上面代码中，`bind()` 方法除了绑定 `this` 对象，还将 `add()` 函数的第一个参数 `x` 绑定成 `5`，然后返回一个新函数 `newAdd()`，这个函数只要再接受一个参数 `y` 就能运行了。
