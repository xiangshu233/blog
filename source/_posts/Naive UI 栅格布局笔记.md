---
title: Naive UI 栅格布局笔记
date: 2022-05-13 18:27
categories: [vue]
tags: [Naive UI, 布局]
cover: https://link.jscdn.cn/1drv/aHR0cHM6Ly8xZHJ2Lm1zL3UvcyFBaFhWN0U3bHBTaWtsbkNaWjYxY0lLczdEUGlpP2U9Yldkd0Fp.jpg
cover_info:
  title: Naive UI 栅格布局笔记
---


基于 CSS Grid，响应式，远离 IE

`'screen'` 根据屏幕断点进行响应式布局

## 屏幕断点

屏幕断点分为

| 断点前缀 | 最小宽度 |                css                 |
| :------: | :------: | :--------------------------------: |
|   `s`    |  640px   | @media (min-width: 640px) { ... }  |
|   `m`    |  768px   | @media (min-width: 768px) { ... }  |
|   `l`    |  1024px  | @media (min-width: 1024px) { ... } |
|   `xl`   |  1280px  | @media (min-width: 1280px) { ... } |
|  `2xl`   |  1536px  | @media (min-width: 1536px) { ... } |

``` html
  <n-grid
    class="mt-4"
    cols="0 s:1 m:1 l:12 xl:12 2xl:24"
    responsive="screen"
    :x-gap="16"
    :y-gap="16"
  >
    <n-gi span="0 s:1 m:1 l:8 xl:8 2xl:16">
      <Map />
    </n-gi>

    <n-gi span="0 s:1 m:1 l:4 xl:4 2xl:8">
      <StateInfo />
      <LatestAlarm />
    </n-gi>
  </n-grid>
```

先看下`Grid `的 ApI

## Grid Props

| 名称            | 类型                            | 默认值 | 说明                                                         |
| --------------- | ------------------------------- | ------ | ------------------------------------------------------------ |
| cols            | number \| ResponsiveDescription | 24     | 显示栅格的数量                                               |
| collapsed       | boolean                       | false  | 是否默认折叠                                                 |
| collapsed-rows  | number                          | 1      | 默认展示的行数                                               |
| responsive      | 'self' \| 'screen'              | self   | 'self' 根据自身宽度进行响应式布局，'screen' 根据屏幕断点进行响应式布局 |
| item-responsive | boolean                         | false  | 子元素是否可具有响应式宽度                                   |
| x-gap           | number \| ResponsiveDescription | 0      | 横向间隔槽                                                   |
| y-gap           | number \| ResponsiveDescription | 0      | 纵向间隔槽                                                   |



cols 默认显示 24 个栅格

```html
<n-grid cols="0 s:1 m:1 l:12 xl:12 2xl:24"></n-grid>
```

- 0 表示 s（640px）以下不显示
- s（640px）到 m（768px） 区间 为  1 个栅格
- m（768px）到 l（1024px） 区间 为  1 个栅格
- l（1024px）到 xl（1024px） 区间 为  12 个栅格
- xl（1280px）到 2xl（1536px） 区间 为  12 个栅格
- 2xl（1536px） 以上为  24 个栅格

综上

## GridItem Props

| 名称   | 类型                            | 默认值 | 说明                              |
| ------ | ------------------------------- | ------ | --------------------------------- |
| offset | number \| ResponsiveDescription | 0      | 栅格左侧的间隔格数                |
| span   | number \| ResponsiveDescription | 1      | 栅格占据的列数，为 0 的时候会隐藏 |
| suffix | boolean                          | false  | 栅格后缀                          |

```html
// `n-grid-item` 可以被简写为 `n-gi`
<n-gi span="0 s:1 m:1 l:8 xl:8 2xl:16"></n-gi>
```

这段和`grid`基本一个意思

- s 以下不显示
- s 到 m 占据 1 个栅格
- m 到 l 占据  1 个栅格
- l 到 xl 占据  8 个栅格
- xl 到 2xl 占据  8 个栅格
- 2xl 占据以上  16 个栅格

综上









