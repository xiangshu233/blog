---
date: 2022-10-16
title: vue3 中的 v-model
categories: [Vue3]
tags: [vue3, v-model]
cover: https://cdn.xiangshu233.cn/post/banner/wallhaven-lqq8ky.jpg
cover_info:
  meta: Vue3、JavaScript
  title: Vue3 中的 v-model
banner: https://cdn.xiangshu233.cn/post/banner/wallhaven-lqq8ky.jpg
references:
  - title: '组件事件'
    url: https://cn.vuejs.org/guide/components/events.html#usage-with-v-model
---

## 在原生组件中使用 v-model

```html
<input v-model="name" />
```

v-model 是本质上是一个语法糖，上面的代码其实等价于下面这段 (编译器会对 `v-model` 进行展开)：

```html
<input
  :value="name"
  @input="name = $event.target.value"
/>
```
{% image  https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2022/10/v-model.gif %}

这样最简单的一个双向绑定就实现了，在输入框里更改内容，setup 中的  name 也会改变，数据改变视图也会相应改变

## 在自定义组件中使用 v-model

```html
<!-- parent.vue -->
<child v-model="age" />

<!-- 等价于 -->
<child
  :modelValue="age"
  @update:modelValue="newValue => age = newValue"
/>
```

```html
// child.vue
<script setup lang="ts">
  const props = defineProps<{
    modelValue: number;
  }>();

  const emit = defineEmits<{
    (e: 'update:modelValue', modelValue: number): void;
  }>();

  const changAge = () => {
    emit('update:modelValue', 18);
  };
</script>

<template>
  <p>{{ props.modelValue }}</p>
  <button @click="changAge">点击按钮(changAge)</button>
</template>
```

`v-model` 用在自定义组件上时，v-model默认绑定的 `prop` 名是 `modelValue`

事件也由 `@input` 更改为`@update: modelValue`

## v-model 的参数

vue3 移除了 model 选项，这样就无法在组件内修改默认 prop 名，如果要修改默认 prop 名，可以通过给 `v-model:xxx` 指定一个参数来更改名字：

```html
<child v-model:name="name" />
```

在这个例子中，子组件应声明一个 `name` prop，并通过触发 `update:name` 事件更新父组件值：

```html
<script setup lang="ts">
  const props = defineProps<{
    name: string;
  }>();

  const emit = defineEmits<{
    (e: 'update:name', name: string): void;
  }>();

  const changName = () => {
    emit('update:name', 'mark');
  };
</script>

<template>
  <p>这是子组件</p>
  <button @click="changName">点击</button>
</template>
```

vue3 同时也移除了 `.sync` 修饰符，我们先来回顾一下`.sync` 的作用：

`.sync` 可以简单的理解为：可以同时双向绑定多个 `prop`，而并不像 `v-model` 那样，一个组件只能有一个。

.sync 本质

```html
<!--语法糖.sync-->
<my-component :first-name.sync="first" :last-name.sync="last" />
<!--编译后的写法-->
<my-component
  :first-name="first"
  @update:first-name="(val) => first = val"
  :last-name="last"
  @update:last-name="(val) => last = val"
>
```

`v-bind.sync` 在 2.x 中的使用情况相当混乱, 因为用户以为可以像 `v-bind` 一样使用它 (完全不看我们在文档中写的). 我认为最好的解释就是:

> 认为 `v-bind:title.sync="title"` 是一种具有额外功能的正常绑定是错误的想法, 因为这跟双向绑定完全不同. `.sync` 修饰符的工作原理就像另一种用于创建双向绑定的语法糖 `v-model`. 主要区别在于 `.sync` 可以在单个组件上定义多个双向绑定, 但 `v-model` 只能定义一个.

那么问题来了: 如果告诉用户不要将 `v-bind.sync` 认为是 `v-bind`, 应该把它认为是 `v-model`, 那它是不是应该成为 `v-model` 的一部分 ?

...

现在，在 vue3 里，你可以在组件上书写多个 `v-model` 来替代 `.sync` 了

```html
<my-component
  v-model:first-name="first"
  v-model:last-name="last"
/>
```

```ts
const first = ref('王');
const last = ref('羲之');
```

```ts
// my-component.vue
<script setup lang="ts">
  const props = defineProps<{
    firstName: string;
    lastName: string;
  }>();

  const emit = defineEmits<{
    (e: 'update:firstName', firstName: string): void;
    (e: 'update:lastName', lastName: string): void;
  }>();

  const changName = () => {
    emit('update:firstName', '李');
    emit('update:lastName', '白');
  };
</script>
```
```html
<template>
  <p>{{ props.firstName }}{{ props.lastName }}</p>
  <button @click="changName">点击按钮(changName)</button>
</template>
```
{% image   https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2022/10/sync.gif %}

## 总结

vue2 和 vue3 中 v-model 区别:

1. 在自定义组件中 v-model 默认绑定的 prop 名从 value 变为 modelValue
2. 事件名也由 `@input` 更改为`@update: modelValue`
3. vue3 移除了 model 选项，修改默认 prop name 可以通过给 `v-model:xxx` 指定一个参数来更改名字
4. vue3 同时也移除了 `.sync` 修饰符，你可以在组件上书写多个 v-model 来替代 .sync

