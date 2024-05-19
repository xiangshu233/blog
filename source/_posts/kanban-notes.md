---
title: 看板大屏笔记
tags: [JS, 看板, 大屏]
categories: [笔记]
date: 2023-07-25 19:12:31
description:
cover: https://cdn.xiangshu233.cn/post/banner/wallhaven-x1o6qo.jpg
banner: https://cdn.xiangshu233.cn/post/banner/wallhaven-x1o6qo.jpg
---

## Grid 网格布局

拿到大屏设计稿首先是设计页面布局，把布局中最基本的 `块（项目）` 定义好。这里用 flex 布局一把梭也能实现，但是没有 grid 强大和灵活，比如`块（项目）`大小、间距改变，这些改起来还是相当麻烦的。如果是 grid 布局，修改模块间距就方便很多，它有一个 `gap` 属性，`row-gap`属性设置行与行的间隔（行间距），`column-gap`属性设置列与列的间隔（列间距）。

```css
.container {
  row-gap: 20px;
  column-gap: 20px;
}
```

`gap`属性是`column-gap`和`row-gap`的合并简写形式，语法如下。

如果`gap`省略了第二个值，浏览器认为第二个值等于第一个值。

```css
.container {
  gap: 20px 20px;
}
// 等于
.container {
  gap: 20px;
}
```

要修改布局的占比也很简单，下面定义的是 三行两列 容器布局，比如第一行高度占比想修改的高一点，只要修改第一个数值 `grid-template-rows: 50% auto 20%;`，那第一行占比就是 50%

```css
 // 声明三行两列的网格布局
.container {
    display: grid;
    // 3 个值表示设置三行且第一行占容器30%，第二行为 auto，第三行占容器20%
    grid-template-rows: 30% auto 20%;
    // 2 个值表示设置两列且第一列的高度是第二列的两倍
    grid-template-columns: 2fr 1fr;
    gap: 20px;
  }
```

此外 grid 还提供了两个关键字：

`fr`关键字（fraction 的缩写，意为"片段"）。如果两列的宽度分别为`2fr`和`1fr`，就表示前者是后者的两倍。

`fr`关键字可以搭配绝对长度使用

```css
.container {
  display: grid;
  grid-template-columns: 150px 1fr 2fr;
}
```

`auto`关键字表示由浏览器自己决定长度，自动占满剩余长度

更改某个`块（项目）`的大小：

```css
.item-1 {
  /* 项目一占据两行两列 */
  grid-column: span 2;
  grid-row: span 2;
}
```

所以更推荐使用`gird`布局用于整个页面的布局设计，且两者在应用场景上并不冲突，grid 处理整体布局，flex 处理局部布局才是最佳实践。

还有一些其他 API 此次开发没用到就不细说了，可以去看阮一峰的 gird 教程

[阮一峰 CSS Grid 网格布局教程](https://www.ruanyifeng.com/blog/2019/03/grid-layout-tutorial.html)

tip: 有些东西没必要全部记住，用的时候再去翻就行

下面是使用 gird 布局写的一个大屏布局 demo，代码量少的同时结构也很清晰

[grid 在线地址](https://codepen.io/yanxiazhi/pen/GRwGamY)

![image-20230721173135535](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/8/image-20230721173135535.png)

## 饼图

带装饰效果的饼图，不贴代码了，直接看

还有自定义 legend，默认的 formatter 选项只有 name 这个属性，如果想显示值只能用下面这种写法了

```js
formatter(name) {
    // pieData 是数据源，通过 name 获取到 pieData 中的单个对象
    const singleData = pieData.filter(item => item.name === name)
    return `${name} | ${singleData[0].value} 评论`
},
```

[示例站点](http://echarts.zhangmuchen.top/#/detail?cid=bd637-b014-82c7-a37ce-6879ecd1)

![image-20230724160934948](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/8/image-20230724160934948.png)

### 3D环形饼图

所有的 3D环形饼图代码逻辑都是一样的，无脑 copy😁

[示例站点](http://echarts.zhangmuchen.top/#/detail?cid=7b90f-5d63-f50b-a6a55-5868b8a6)

![image-20230724171154164](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/8/image-20230724171154164.png)

## table 单元格画对角线

这个对角线画原生表格的时候很有用，它不会随单元格拉伸而导致位置偏移

[示例站点](https://codesandbox.io/s/table-dan-yuan-ge-dui-jiao-xian-3qxzkj?file=/src/demo.vue)

![image-20230726142234291](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/8/image-20230726142234291.png)

### 纯 css 动态背景

[示例站点](https://codepen.io/yanxiazhi/pen/XWoWdqr)

![image-20230823153601852](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/8/tu-chuang-post-img/image-20230823153601852.png)

[radial-gradient()](https://developer.mozilla.org/zh-CN/docs/Web/CSS/gradient/radial-gradient) CSS 函数创建一个图像，该图像由从原点辐射的两种或多种颜色之间的渐进过渡组成。

```css
/* 在容器中心的渐变，从红色开始，变成蓝色，最后变成绿色 */
radial-gradient(circle at center, red 0, blue, green 100%)

/* 设置径向渐变的形状和大小，closest-side表示渐变的尺寸将根据元素的宽度和高度中较短的那一边来确定，以确保整个渐变都能完全覆盖元素。渐变从起始颜色（淡紫色）向结束颜色（淡橙色）过渡 */
radial-gradient(closest-side, rgba(99,102,241, 1 ), rgba(235, 105, 78, 0)),
```

后续再补充。。。
