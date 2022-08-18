---
date: 2021-10-14
title: vue3 响应式原理笔记
categories: [Vue3]
tags: [Vue3, Vue3响应式原理]
cover: https://tva2.sinaimg.cn/large/006wuklaly8h5api6uklpj31c00u0wi1.jpg
---


## v2响应式原理

>[Object.defineProperty()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象。



- 对象：其核心是递归 `object` 的每一个属性，（这也是浪费性能的地方），给每个对象属性增加 `getter` 和 `setter`，当属性发生变化的时候会更新视图

缺点：`defineProperty` 只能检测到对象自带的属性，无法检测到对象属性的`新增` 和 `删除`
> 后期 `v2` 提供了 `this.$set(target, prop, val)` 来确保你` 新增的对象属性 `也具有响应式
> 官方解释：向响应式对象中添加一个属性，并确保这个新属性同样是响应式的，且触发视图更新。它必须用于向响应式对象上添加新属性，因为 Vue 无法探测普通的新增属性 (比如 `this.myObject.newProperty = ‘hi’`）

- 数组：重写了那些会改变原数组的方法，原因是 `defineProperty` 不能检测到数组长度的变化，准确的说是通过改变 `length` 而增加的长度不能监测到（`arr.length = newLength` 也不会）。比如重写的: `push、pop、shift、unshift、sort、reverse、splice` 方法，以便数组发生变化后能够给观察者发送通知，并且 `push、unshift、splice` 会给数组新增元素，我们还需要知道新增的是什么数据，需要对这些新增的数据进行观测。


## v3响应式原理

>[Proxy](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy)对象用于创建一个对象的代理，从而实现基本操作的拦截和自定义（如属性查找、赋值、枚举、函数调用等）。其实就是在对 `目标对象` 的操作之前提供了 `拦截` ，意味着你可以在这层拦截中进行各种操作，修改某些操作的 `默认` 行为。这样我们可以不直接操作 `对象本身` ，而是通过操作对象的 `代理对象` 来间接来操作对象，来实现数据的响应式。

### 语法
 ```js
const p = new Proxy(target, handler)
 ```
参数一：要使用 `Proxy` 包装的目标target `对象`（可以是任何类型的对象，包括原生数组，函数，甚至另一个代理）
参数二： `handler` 一个通常以函数作为属性的对象，各属性中的函数分别定义了在执行各种操作时代理 p 的行为

`v3` 使用了 `es6` 的 `Proxy` 来实现数据的响应式，`Proxy` 消除了之前 `v2.x` 中基于 `Object.defineProperty` 的实现所存在的限制：无法监听属性的添加和删除、数组索引和长度的变更

{%note color:yellow  注意
不过`v3`底层不是直接对`target`进行如下的简单操作。而是利用`es6`的`Reflect`。
`Reflect`是一个内置的对象，它提供拦截`JavaScript`操作的方法。这些方法与`proxy handlers`的方法相同。`Reflect`不是一个函数对象，因此它是不可构造的
详情：[Reflect](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect)
%}



## v3响应式示例
以下是一个将 `user` 对象变为响应式对象的示例

首先定义一个 `user` 对象，这是我们的目标对象，也就是 `Proxy` 的第一个参数
```ts
type userType = {
  name?: string
  age?: number
  hobby?: string
  realName: { [key: string]: string }
  [key: string]: unknown    // 动态添加属性
}

const user: userType = {
  name: 'alice',
  age: 18,
  hobby: 'cook',
  realName: {
    firstName: 'ren',
    lastName: 'pingsheng'
  }
}
```

然后再定义一个 `handle` 对象，这是 `Proxy` 的第二个参数，这里面包含 `get()、set()、deleteProperty()` 方法

>这里有一个问题，如果这个对象是个多层对象，`Proxy` 并不会代理多层对象(只会代理第一层)，因为它是 `懒代理` ，与 `v2.x` 的 `Object.defineProperty` 不同`Object.defineProperty` 方法是一开始就会对这个多层对象进行递归处理，所以可以拿到，所以这里我们需要加一个判断：如果目标对象的属性 `target[prop]` 还是个 `对象`，则对这个属性对象进行递归代理，这样就能读取到深层对象

```ts
const handler: object = {
  // 获取目标对象某个值
  get(target: userType, prop: keyof userType) {
    console.log('get 方法执行了');
    console.log('target 需要取值的目标对象', target);
    console.log('prop 设置的目标对象属性的名称', prop);
    if (typeof target[prop] === 'object' && target[prop] !== null) {
      // 递归代理，只有取到对应值的时候才会代理
      return new Proxy(target[prop] as userType, handler)
    }
    return Reflect.get(target, prop)
  },

  // 修改或添加新的属性
  set(target: userType, prop: keyof userType, val: string) {
    console.log('set 方法执行了');
    console.log('target 设置属性的目标对象', target);
    console.log('prop 设置的属性的名称', prop);
    console.log('val 设置的值', val);
    if (typeof target[prop] === 'object' && target[prop] !== null) {
      // 递归代理，只有取到对应值的时候才会代理
      return new Proxy(target[prop] as userType, handler)
    }
    return Reflect.set(target, prop, val)
  },

  // 删除目标对象上的某个属性
  deleteProperty(target: userType, prop: keyof userType) {
    console.log('del 方法执行了');
    console.log('target 删除属性的目标对象', target);
    console.log('prop 需要删除的属性的名称', prop);
    if (typeof target[prop] === 'object' && target[prop] !== null) {
      // 递归代理，只有取到对应值的时候才会代理
      return new Proxy(target[prop] as userType, handler)
    }
    return Reflect.deleteProperty(target, prop)
  }
}
```

最后实例化`Proxy`

```ts
//  proxyUser是代理后的对象。当外界每次对 proxyUser 进行操作时，就会执行 handler 对象上的一些方法。
const proxyUser = new Proxy(user, handler)
```



### 测试

#### 获取对象属性

```ts
// 获取 name 属性
console.log('proxyUser.name: ', proxyUser.name);  // alice
```

![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@287733c8d7d836fb2bc1b0283de2ff77d3743ff4/2021/10/15/867d13de5cd55d631f7ce6f514d19dd4.png)

测试结果显示成功获取到了`proxyUser.name`属性


```ts
// 获取嵌套对象属性
console.log('proxyUser.realName.firstName: ', proxyUser.realName.firstName); // ren
```

![image-20211015150020974](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@94dab9752348b002beb097cc93362c7a4e0e4321/2021/10/15/dc45884e807f7391d43d234d43cbce02.png)

测试结果显示成功获取到了`proxyUser.realName.firstNamename`属性，为什么执行了两遍？

第一遍执行传的`target`是`user`，但是过程遇到了以下判断

```ts
if (typeof target[prop] === 'object' && target[prop] !== null) {
    // 递归代理，只有取到对应值的时候才会代理
    return new Proxy(target[prop] as userType, handler)
}
```

以上的意思是：如果`target`的属性还是一个`object`且不为`null`的情况下则进行递归调用取值，此时的`target`为`target[prop]`，`handler`为自身`handler`对象，所以才会出现为什么执行了两遍



#### 修改、新增对象属性

```ts
console.log(proxyUser.name);	// alice
proxyUser.name = 'xiaoming'
console.log(proxyUser.name);	// xiaoming
```

![image-20211015151652067](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@53bd62694f902447d30e34e71255991fcbd05602/2021/10/15/8aa21b4e2af7d080a77d416c16618b19.png)



测试修改嵌套对象属性

```ts
console.log(proxyUser.realName);  // Proxy {firstName: 'ren', lastName: 'pingsheng'}
proxyUser.realName.firstName = 'yan'
proxyUser.realName.lastName = 'xiazhi'
console.log(proxyUser.realName);  // Proxy {firstName: 'yan', lastName: 'xiazhi'}
```

![image-20211015152145776](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@a808238505bd25de3e12d4fd7070e260bfd48f7c/2021/10/15/3ec25e1ba8a7a0e4f7712be467cd264a.png)

新增一个 `gender` 属性

```tsx
proxyUser.gender = 'women';
console.log(proxyUser);
// Proxy {name: 'xiaoming', age: 18, realName: {…}, gender: 'women'}

```

![image-20211015154406651](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@85a0621bcc111a3db27ca5155c556ca089300bc5/2021/10/15/93fd4984c6ad987d65449d5dd27c98f1.png)

#### 删除对象属性

```tsx
delete proxyUser.hobby
console.log('删除操作', proxyUser);
```

![image-20211015154858559](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@48ac8ed4cc04c32e6abae05db64d23eb07acf3bf/2021/10/15/a7c661f99feef633bd6c36c1e3bad1e8.png)

测试结果删除成功

以上就是`v3`响应式`原理`最简单的实现


## 总结
1. `Proxy`使用上比`Object.defineProperty`方便的多
2. `Object.defineProperty`只能代理对象上的某个属性，而`Proxy`代理整个对象
3. 如果对象内部要全部递归代理，则Proxy可以只在调用时递归，而`Object.defineProperty`需要在一开始就全部递归，`Proxy`性能优于`Object.defineProperty`
4. 对象`新增属性`时，`Proxy`可以监听到，`Object.defineProperty`监听不到
5. 数组`新增删除修改`时，`Proxy`可以监听到，`Object.defineProperty`监听不到





