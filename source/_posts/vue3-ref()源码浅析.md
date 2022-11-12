---
date: 2021-11-03
title: vue3 ref() 响应式源码浅析
categories: [Vue3]
tags: [vue3, ref]
cover: https://tva4.sinaimg.cn/large/006wuklaly8h5apgldretj31hc0u0akq.jpg
---

## 官方解释
> `ref` 接收一个内部值并返回一个响应式且可变的ref对象。`ref` 对象具有指向内部值的单个property `.value`
> 如果将对象分配为 ref 值，则通过 [reactive](https://v3.cn.vuejs.org/api/basic-reactivity.html#reactive) 函数使该对象具有高度的响应式。

语法：`const xxx = ref(initValue)`

## ref()

- 返回一个包含响应式数据的**引用对象**
- JS中操作数据需要加 `xxx.value`，模板中读取数据不需要加（内部解析模板时会自动添加 `.value`），直接`<div>{{ xxx }}</div>`
- 接收的数据可以是 `基本类型` 也可以是 `对象类型(复杂类型)`
- 基本类型的数据：响应式依然是靠 `es5` 的 `Object.definProperty()` 的 `get()` 与 `set()` 实现的
- 对象类型的数据：内部“求助”了 `v3` 中的 `reactive()` 函数（复杂类型一律用 `reactive()`）
- 为非对象的基本类型创建响应式，其内部使用 `getter/setter` 的原因是 `proxy` 只能代理对象，无法直接对基本类型进行代理


下面来测试一下：

```js
const p = 1
const p_ref = ref(p)
console.log('reffffffffff', p_ref);

// ref()是基于 Object.defineProperty 的 get、set 方法进行数据劫持，来达到响应式
// 触发 getter 操作
console.log(p_ref.value)	// 1
// 触发 setter 操作
p_ref.value = 2		// 2
```

打印显示：
![image-20211102155157982](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@7caf64719b2255021d1c3fd06324783ed82c5464/2021/11/02/3a6f3f057054933aee6cf8cc6186f5e9.png)

可以看到经过`ref()`加工后的数据变成了一个**RefImpl**（引用实例对象），该对象作为一个**响应式的引用**维护着它内部的值，这就是`ref（referenced）`名称的来源。它只包含一个名为`value`的`property`（属性），使用时需要加`.value`


## 源码

### ref()
```ts
export function ref(value?: unknown) {
  return createRef(value, false)
}
```
当使用`ref()`创建一个响应式数据时，`ref()`会调用`createRef()`函数来创建一个`RefImpl`对象

### createRef()
```ts
function createRef(rawValue: unknown, shallow: boolean) {
  if (isRef(rawValue)) {
    return rawValue
  }
  return new RefImpl(rawValue, shallow)
}
```

`createRef()` 接收一个 `rawValue`， `rawValue` 如果已经是一个 `RefImpl` 对象了，也就是其属性 `__v_isRef` 为 `true`，就不再创建新的 `RefImpl` 对象，而是直接返回 `rawValue`。否则，会创建一个 `RefImpl` 对象。

```ts
// isRef 用来判断 rawValue 是不是 RefImpl 对象
export function isRef<T>(r: Ref<T> | unknown): r is Ref<T>
export function isRef(r: any): r is Ref {
  return Boolean(r && r.__v_isRef === true)
}
```

  >凡是一个 `ref` 对象都应当有 `__v_isRef` 属性，且为 `true` ，它用于 `createRef` 函数判断其是否为 `RefImpl`
  ![image-20211103092252795](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@8c8045686ff7963402b966075550b72382d71f29/2021/11/03/dffff8209b8af4b91694cc6bafb19a80.png)


### RefImpl 类
```ts
class RefImpl<T> {
  private _value: T
  private _rawValue: T

  public dep?: Dep = undefined
  public readonly __v_isRef = true

  constructor(value: T, public readonly _shallow: boolean) {
    this._rawValue = _shallow ? value : toRaw(value)
    this._value = _shallow ? value : toReactive(value)
  }

  get value() {
    trackRefValue(this)
    return this._value
  }

  set value(newVal) {
    newVal = this._shallow ? newVal : toRaw(newVal)
    if (hasChanged(newVal, this._rawValue)) {
      this._rawValue = newVal
      this._value = this._shallow ? newVal : toReactive(newVal)
      triggerRefValue(this, newVal)
    }
  }
}
```

`RefImpl` 有两个私有属性

`_value` 是存放当前值的

`_rawValue` 是存放原始值的

公共的只读变量 `__v_isRef` 是用来标识该对象是一个 `ref` 响应式对象的标记

最后 `dep` 属性不知道干啥的，还没研究

```ts
constructor(value: T, public readonly _shallow: boolean) {
  this._rawValue = _shallow ? value : toRaw(value)
  this._value = _shallow ? value : toReactive(value)
}
```

构造函数参数：

`value` 是 `ref()` 接收的值

`_shallow` 在 `createRef()` 中默认传的 `false`，表示 `ref()` 是递归监听，深度响应式（即无论对象嵌套多深，一旦变更都会触发响应式，并更新UI）

既然是 `_shallow = false`，那实例化的时候肯定走 `toRaw()` 函数和 `toReactive()`函数了

`toRaw()` 函数也是 `v3` 的一个 `api`，它的作用是获取 `ref` 的原始值

例子：

```ts
let state = ref({ name: 'alice', age: 18 });	// RefImpl
// 获取原始值，更改原始值的时候，UI 不会触发更新
let obj2 = toRaw(state.value);	// ref 的话记得加 .value，否则 UI 还是会更新
console.log('obj2', obj2);	// obj2 {name: 'alice', age: 18}
```

此时原始值存放在 `this._rawValue` 属性中

{%note color:yellow 注意：
  如果给 `toRaw()` 传入普通对象，那它就原样返回这个普通对象，不做任何加工处理
%}

```ts
this._value = _shallow ? value : toReactive(value)
```

这一行是 `ref()` 的**关键**，主要体现在 `toReactive()` 函数中

```ts
export const toReactive = <T extends unknown>(value: T): T =>
  isObject(value) ? reactive(value) : value
```

`toReactive()` 函数会判断 `ref()` 接收的值是不是一个 `对象`，如果是对象则通过 `reactive()` API 创建一个代理（proxy）对象并返回，就是开头所说的 “ 如果将对象分配为 ref 值，则通过 [reactive](https://v3.cn.vuejs.org/api/basic-reactivity.html#reactive) 函数使该对象具有高度的响应式”，否则直接返回原参数

最后不管是对象还是基本类型数据保存在 `this._value` 属性中

```ts
// RefImpl 类里的 get set
get value() {
  trackRefValue(this)	// 这个先不管
  return this._value	// 直接返回
}

set value(newVal) {		// 拦截 set 操作，接收 set 的值
  newVal = this._shallow ? newVal : toRaw(newVal)	 // ref()返回的 RefImpl 实例对象中 _shallow 属性一定是 false，返回获取原始值赋给 newVal
  if (hasChanged(newVal, this._rawValue)) {	// 这里是新旧值进行比较
    this._rawValue = newVal	// 更新原始值
    this._value = this._shallow ? newVal : toReactive(newVal)	// 到这里又会进入 toReactive() 跟构造参数中是一个意思
    triggerRefValue(this, newVal) // 这个还不清楚
  }
}
```
在对某个 `对象属性` 进行存取行为的时候会触发 `getter/setter`

`xxx.value` 的时候触发 `getter`

`xxx.value = 222` 的时候触发 `setter`


## 整个ref()运行流程
```ts
export function ref(value?: unknown) {
  return createRef(value, false)
}

function createRef(rawValue: unknown, shallow: boolean) {
  if (isRef(rawValue)) {
    return rawValue
  }
  return new RefImpl(rawValue, shallow)
}

// RefImpl 类
class RefImpl<T> {
  private _value: T
  private _rawValue: T

  public dep?: Dep = undefined
  public readonly __v_isRef = true

  constructor(value: T, public readonly _shallow: boolean) {
    this._rawValue = _shallow ? value : toRaw(value)
    this._value = _shallow ? value : toReactive(value)
  }

  get value() {
    trackRefValue(this)
    return this._value
  }

  set value(newVal) {
    newVal = this._shallow ? newVal : toRaw(newVal)
    if (hasChanged(newVal, this._rawValue)) {
      this._rawValue = newVal
      this._value = this._shallow ? newVal : toReactive(newVal)
      triggerRefValue(this, newVal)
    }
  }
}
```

## 总结

1. 既接收`基本类型`也接收`对象类型`（遇到`对象类型`内部自动调`reactive()` API）

2. 在基本类型的数据的情况下，响应式依然是靠`es5`的`Object.definProperty()`的`get()`与`set()`实现的

3. 返回的是一个**RefImpl**（引用实例对象）

4. `RefImpl`对象中的`value`属性存放的是原始值

5. `ref()`接收基本类型的情况下，修改响应式数据，原数据不会发生改变，UI会刷新；修改原数据，响应式数据不会变，UI也不会刷新，得出结论：两者无任何关联，属**复制**关系，因为在`JS`中的基本类型，是按值传递的

   ```js
   const a = 1;
   function foo(x) {
       x = 2;
   }
   foo(a);	// 在 a 传入的那一刻，a 与 x 就已经毫无关系，属副本
   console.log(a); // 仍为 1, 未受 x = 2 赋值所影响
   ```

6. `ref()`接收对象类型的情况下，修改响应式数据，原数据会发生改变，UI也会刷新；修改原数据的情况下，虽然响应式数据发生了变化，但是UI却没有更新，详情：[JS基础之传参（值传递、对象传递）](https://www.cnblogs.com/xcsn/p/9158727.html)

7. 基本数据类型交给`ref()`去做，对象（复杂类型）交给`reactive()`去做


`v3`的`ref()` API 源码的分析总结完毕🎉！

## 结语
> 小时候不理解老人晒太阳，一坐就是半天，长大才明白：目之所及，皆是回忆；心之所想，皆是过往；眼之所看，皆是遗憾
