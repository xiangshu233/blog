---
title: 菜单标题超出显示 tooltip
tags: [JS, Vue]
categories: [笔记]
date: 2023-08-25 14:00:14
description:
cover: https://cdn.xiangshu233.cn/post/banner/wallhaven-0qe6zr.jpg
---

## 在线Demo

{% link https://codesandbox.io/s/ce-bian-bu-ju-cai-dan-guo-chang-xian-shi-tooltip-6fqvxt desc:true %}

## 需求

菜单标题过长用 tooltip 显示用户体验更好。虽然`<a>`标签的 title 属性 hover 后也能显示全部 title，但是要 hover  1-2s 才显示，体验不行，另外只有菜单过长的用 tooltip 显示才更合理，即菜单有`...`的

## 分析需求

注：本项目 UI 使用的是 antdv，各个 UI 使用上区别不大，逻辑不变。

分析需求可以得到的关键点是：**如何知道这个元素内容超出了元素容器**，如果超过了就显示 tooltip 不超过不显示，这里你需要了解两个 DOM 元素属性：

- `scrollWidth`属性表示元素内容在不使用滚动条的情况下，需要的总宽度。它包括了元素内容的实际宽度以及在不换行情况下可能产生的溢出部分的宽度（**元素内容总体宽度，包括被隐藏的那部分**）。
- `clientWidth`属性表示元素内容的可见宽度，不包括滚动条或边框的宽度。它是指元素内部的可视区域的宽度，不包括被溢出隐藏的内容部分（**元素内容可见宽度，不包括被隐藏的那部分**）。

{% del 到这里其实本文就结束了（bushi） %} {% emoji blobcat ablobcatwave %}

通过将这两个属性进行比较，可以判断元素是否出现了水平方向上的溢出。如果`scrollWidth`大于`clientWidth`，则表示元素内容超出了可见区域，然后手动控制显隐 tooltip 即可。

## 代码

```js
// 用于手动控制 tooltip 显隐
const showTooltip = ref(false)

// 存储菜单文本 dom 元素
const menuTextRef = ref(null)

// 控制菜单文本溢出时显示 tooltip
function hoverMenu() {
  nextTick(() => {
    if (menuTextRef) {
      // 如果文本内容的整体宽度大于其可视宽度，则显示 tooltip: true / false
      showTooltip.value = menuTextRef.value?.scrollWidth > menuTextRef.value?.clientWidth
    }
  })
}

// 菜单文本动态样式
const getSubTextStyle = computed(() => {
  if (!routerStore.isCollapsed) {
    return {
      width: '90px',
      display: 'inline-block',
      overflow: 'hidden',
      textOverflow: 'ellipsis',
    }
  }
  else {
    return {
      width: '',
    }
  }
})
```

```html
<a-menu-item
  v-if="props.item?.children === null"
  :key="props.item.treeId"
>
  <router-link
    :to="props.item?.menuPath"
    @mouseover="hoverMenu"
    @mouseleave="showTooltip = false"
  >
    <Icon
      class="anticon"
      :icon="props.item?.iconCls"
      width="20"
      height="20"
    />
    <a-tooltip
      :visible="showTooltip"
      placement="top"
    >
      <template #title>
        {{ props.item?.menuName }}
      </template>
      <span
        ref="menuTextRef"
        :style="getSubTextStyle"
      >
        {{ props.item?.menuName }}
      </span>
    </a-tooltip>
  </router-link>
</a-menu-item>
```

`menuTextRef`是文本元素的 ref ，用于获取`scrollWidth`和`clientWidth`

{% note color:yellow 注：
  菜单跳转使用的是`<router-link>`组件，事件绑定在子标签下会无效，因为`<router-link>`默认会渲染成`<a>`标签来处理路由导航，而`<a>`标签会阻止其内部子元素触发的大多数事件。
  `<a>`标签是一个具有默认行为和语义的HTML元素，其中包含了一些默认的事件处理逻辑，例如点击后进行页面导航。当你在`<router-link>`的子标签上绑定事件时，这些事件很可能会被`<a>`标签捕获并阻止冒泡，因此无法触发到绑定的事件处理函数。
  所以 `@mouseover`、`@mouseleave`只能绑定在`<router-link>`组件上。
%}

```html
<router-link
  @mouseover="hoverMenu"
  @mouseleave="showTooltip = false"
>
```

这样就达到了鼠标移入移出显隐 tooltip 效果了{% emoji blobcat ablobcatattentionreverse %}

## 完善细节

菜单在折叠状态下的子菜单是用 Popover 组件展示的，既然没有了宽度限制就没有必要再使用 tooltip 来显示那些放不下的菜单了

![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/8/tu-chuang-post-img/test.gif)

```html
<span
  ref="menuTextRef"
  :style="getSubTextStyle"
>
```

```js
// 菜单文本动态样式
const getSubTextStyle = computed(() => {
  if (!routerStore.isCollapsed) {
    return {
      width: '90px',
      display: 'inline-block',
      overflow: 'hidden',
      textOverflow: 'ellipsis',
    }
  }
  else {
    return {
      width: '',
    }
  }
})
```

给菜单绑定一个`:style`，通过`isCollapsed`来判断菜单是否在折叠状态，这里设置的宽度其实就是`clientWidth`。

代码中得知菜单在展开状态下宽度是`90px`（clientWidth 宽度根据自己代码进行微调），折叠状态下则将`width`设置为空字符串（**通常用于移除元素上已经存在的宽度限制**）。通过将`width`设置为空字符串，可以使元素在没有具体宽度限制的情况下自动适应内容的宽度，这样得到的结果`scrollWidth === clientWidth`始终是相等的，条件不成立为`showTooltip.value = false`，这样就做到了折叠不显示 tooltip 的效果了。

```js
 showTooltip.value =
  menuTextRef.value?.scrollWidth > menuTextRef.value?.clientWidth // false
```

![GIF 2023-8-24 15-56-54](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/8/tu-chuang-post-img/GIF%202023-8-24%2015-56-54.gif)

 {% del 可以的又水了一篇 （不愧是我） %} {% emoji blobcat ablobcatrainbow %}

## {% emoji blobcat ablobcatheart %}

{% poetry  author:叔本华 footer:诗词节选 %}
很多事只是最初看起来有意义，但经过多次重复就慢慢失去了意义。
{% endpoetry %}
