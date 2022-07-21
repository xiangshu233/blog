---
title: Vue h() render() 渲染函数入门
date: 2022-06-25 12:00
categories: [vue3]
tags: [vue3, tsx, jsx, h(), render()]
cover: https://tva2.sinaimg.cn/large/006wuklaly8h3ke68t83xj31hc0u0tr2.jpg
banner: https://tva2.sinaimg.cn/large/006wuklaly8h3ke68t83xj31hc0u0tr2.jpg
references:
  - title: 'V3 渲染函数'
    url: https://v3.cn.vuejs.org/guide/render-function.html#%E6%B8%B2%E6%9F%93%E5%87%BD%E6%95%B0
  - title: 在 Vue3.0 + ts 中如何使用 h() 函数
    url: https://segmentfault.com/a/1190000040819959
  - title: 如何在 Vue3 `script setup` 中使用 render()
    url: https://stackoverflow.com/questions/67918363/how-to-use-render-function-in-script-setup-vue3
---

## 必要的前置知识

### DOM 树

在深入渲染函数之前，了解一些浏览器的工作原理是很重要的。以下面这段 HTML 为例：

```html
<div>
  <h1>My title</h1>
  Some text content
  <!-- TODO: Add tagline -->
</div>
```

当浏览器读到这些代码时，它会建立一个 [”DOM 节点“ 树](https://javascript.info/dom-nodes) 来保持追踪所有内容，如同你会画一张家谱树来追踪家庭成员的发展一样。

上述 HTML 对应的 DOM 节点树如下图所示

![DOM Tree Visualization](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets/20220624160608.png)

**每个元素都是一个节点。**

**每段文字也是一个节点。**

**甚至注释也都是节点。**

**一个节点就是页面的一个部分。**

就像家谱树一样，每个节点都可以有孩子节点 (也就是说每个部分可以包含其它的一些部分)。

高效地更新所有这些节点会是比较困难的，不过所幸你不必手动完成这个工作。你只需要告诉 Vue 你希望页面上的 HTML 是什么，这可以是在一个模板里：

```html
<h1>{{ blogTitle }}</h1>
```

或者一个渲染函数里：

```js
render() {
  return h('h1', {}, this.blogTitle)
}
```

在这两种情况下，Vue 都会自动保持页面的更新，即便 `blogTitle` 发生了改变。

### 虚拟 DOM 树

Vue 通过建立一个**虚拟 DOM** 来追踪自己要如何改变真实 DOM。请仔细看这行代码：

```js
return h('h1', {}, this.blogTitle)
```

`h()` 到底会返回什么呢？其实不是一个*实际*的 DOM 元素。它更准确的名字可能是 `createNodeDescription`，因为它所包含的信息会告诉 Vue 页面上需要渲染什么样的节点，及其子节点的描述信息。我们把这样的节点描述为“虚拟节点 (virtual node)”，也常简写它为 **VNode**。“虚拟 DOM”是我们对由 Vue 组件树建立起来的整个 VNode 树的称呼。

### `h()` 参数

`h()` 函数是一个用于创建 VNode 的实用程序。也许可以更准确地将其命名为 `createVNode()`，但由于频繁使用和简洁，它被称为 `h()` 。它接受三个参数：

```js
// @returns {VNode}
h(
  // {String | Object | Function} tag
  // 一个 HTML 标签名、一个组件、一个异步组件、或
  // 一个函数式组件。
  //
  // 必需的。
  'div',

  // {Object} props
  // 与 attribute、prop 和事件相对应的对象。
  // 这会在模板中用到。
  //
  // 可选的。
  {},

  // {String | Array | Object} children
  // 子 VNodes, 使用 `h()` 构建,
  // 或使用字符串获取 "文本 VNode"
  // 或者有插槽的对象。
  //
  // 可选的。
  [
    'Some text comes first.',
    h('h1', 'A headline'),
    h(MyComponent, {
      someProp: 'foobar'
    })
  ]
)
```

如果没有 prop，那么通常可以将 children 作为第二个参数传入。如果会产生歧义，可以将 `null` 作为第二个参数传入，将 children 作为第三个参数传入。

## 简单示例

```html
<template>
  <VNode />
</template>
```
```ts
<script setup lang="ts">
  import { h } from 'vue';
  import { NButton } from 'naive-ui';

  const VNode = () => {
    return h(
      NButton,
      {
        size: 'small',
        type: 'info',
        onClick: () => console.log('我是h()生成的'),
      },
      {
        default: () => '我是h()生成的',
      }
    );
  };
</script>
```

## 计数器案例

![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets/GIF.gif)

```html
<template>
  <VNode />
</template>
```
```ts
<script setup lang="ts">
  import { NButton, NSpace } from 'naive-ui';
  import { h, ref } from 'vue';

  const counter = ref(0);

  const VNode = () => {
    return h(NSpace, {}, () => [
      h('span', null, `当前计数：${counter.value}`),
      h(
        NButton,
        {
          size: 'small',
          type: 'error',
          onClick: () => counter.value++,
        },
        {
          // 默认插槽
          default: () => 'counter 加 1',
        }
      ),
      h(
        NButton,
        {
          size: 'small',
          type: 'info',
          onClick: () => counter.value--,
        },
        {
          default: () => 'counter 减 1',
        }
      ),
    ]);
  };
</script>
```
在写这段的时候遇到了一个警告
"Non-function value encountered for default slot. Prefer function slots for better performance."
我在 stackoverflow 找到同样的问题，但是我没太明白导致这个原因（有大佬明白请评论指点🙏）

>This is inefficient because the child slot is rendered before the HelloWorld component could even use it. The child slot is essentially rendered in the parent, and then passed to the child. Wrapping the child slot generation in a function defers the work until the child is rendered.
>机翻：
>这是低效的，因为子槽在HelloWorld组件使用它之前就已经呈现了。子槽实际上是在父槽中呈现的，然后传递给子槽。在函数中包装子槽生成会推迟工作，直到渲染子槽为止。

{% link https://stackoverflow.com/questions/69875273/non-function-value-encountered-for-default-slot-in-vue-3-composition-api-comp Non-function&nbsp;value&nbsp;encountered&nbsp;for&nbsp;default&nbsp;slot %}

解决方案
与其在父级中渲染子槽（即，直接传递一个数组 VNodes 作为 slots 参数），不如将其包装在一个函数中：
```js
// src/components/Composite.js
export default defineComponent({
  setup(props, { slots }) {
    return () =>          👇
      h(HelloWorld, {}, () => [h("div", {}, ["Div 1"]), h("div", {}, ["Div 2"])]);
  }
});
```
线上 demo
https://codesandbox.io/s/hardcore-gould-ho47tm?file=/src/components/Composite.js


## setup script 中书写 h() 函数
```ts
<script setup lang="ts">
import { ref,h } from 'vue'

const props = defineProps<{
    text: string
}>()

const handleClick = () => {
    console.log('click!!')
}

const root = h('button', {type:'button', onClick: handleClick, class: 'btn btn-primary'}, props.text)

</script>

<template>
  <root/>
</template>
```

{% note color:yellow
警告 在 setup script 中书写 h() 函数只是一个特殊的实现，此方法不应优先使用，因为它会使代码的可读性降低。 而且`setup script`的出现是为了解决`setup()`需要导出的麻烦。如果你的组件不需要`<template>`，你根本不需要`setup script`，你应该使用`jsx/tsx`文件
%}

## Tag 标签三种写法

![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets/20220625113159.png)

### template 方式

```html
<n-tag round :bordered="false" type="success">
  Checked
  <template #icon>
    <n-icon :component="CheckmarkCircle" />
  </template>
</n-tag>
```

### h() 函数方式

```ts
render() {
  return h(
    NTag,
    // 注意这么写的时候，即便没有 props，
    // 第二个参数也是必须的(传 null)，因为h函数发现第二个参数如果是对象，
    // 那么默认其就是 props，
    {
      round: true,
      bordered: false,
      type: 'success',
    },
    {
      // 使用箭头函数保存 `this` 的值
      default: () => 'Checked',
      // 插槽以函数的形式传递
      icon: () =>
        h(NIcon, {
          component: CheckmarkCircle,
        }),
    }
  );
}
```

### jsx/tsx 方式

使用 `jsx/tsx ` 要在 `script`标签上声明 `lang="tsx"` / `lang="jsx"`

```js
<script lang="tsx"></script>
```
```jsx
render() {
  return (
    <NTag
      round={true}
      bordered={false}
      type="success"
      v-slots={{
        icon: () => <NIcon component={CheckmarkCircle} />,
      }}
    >
      Checked
    </NTag>
  );
}
```