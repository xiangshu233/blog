---
date: 2020-11-11
title: 高德地图 JsApi 绘制折线功能
categories: [Vue, 高德地图JsApi]
tags: [高德地图Api, 绘制折线, Vue]
cover: https://tva4.sinaimg.cn/mw690/006wuklaly1gjqead7pd4j31hc0u07e9.jpg
---
> 本 DEMO 使用高德最新`JS API 2.0`版本，使用 NPM 方式使用 Loader

## 安装
```bash
yarn add @amap/amap-jsapi-loader --save
```

## 加载高德地图

```js
import AMapLoader from '@amap/amap-jsapi-loader';

AMapLoader.load({
  key: "a0fe39f6c90ba997c0833b6b1cd00bfd",
  version: "2.0",
  plugins: ['AMap.Polyline', 'AMap.CircleMarker'], // 插件列表
  AMapUI: {               // 是否加载 AMapUI，缺省不加载
    version: '1.1',       // AMapUI 缺省 1.1
    plugins:[],           // 需要加载的 AMapUI ui插件
  },
}).then((AMap)=>{
  this.initAMap(AMap);
}).catch(e => {
  console.log(e);
});
```

## 初始化AMap

`DOM` 地图容器
{% note color:yellow 要给该容器设置 `高度` 否则无法加载地图实例。 %}

```html
<div id="container"></div>
```

`DATA` 变量

```js
data() {
  return {
    map: null,	// 地图实例
    polyline: null, // 折线实例
    polylinePath: [], // 折线路径列表
  }
},
```

给地图实例添加 `点击` 事件，获取经纬度组合成 `数组` 传递给 ` this.drawCircleMarker(position)`、`this.drawPolyline(position)` 函数

```js
initAMap(AMap) {
  this.map = new AMap.Map('container', {
    mapStyle: 'amap://styles/light',	// 主题样式
    center: [117.224618, 31.8431],		// 位置
    resizeEnable: true,
    zoom: 16
  });
  // 地图点击事件
  this.map.on('click', (e) => {
    const position = [e.lnglat.getLng(), e.lnglat.getLat()];
    this.drawCircleMarker(position);
    this.drawPolyline(position);
  });
},
```

## 具体实现函数

绘制圆点 `AMap.CircleMarker` ，在 `点击` 事件中点击一次创建一个 `CircleMarker` 实例，并添加到地图实例中

```js
drawCircleMarker(position) {
  // 圆点配置项
  const options = {
    center: position, // 圆点位置（经纬度）
    radius: 9,
    strokeColor: '#FFF',
    strokeWeight: 2,
    strokeOpacity: 0.5,
    fillColor: '#5555ff',
    fillOpacity: 1,
    zIndex: 99,
    bubble: true,
    cursor: 'pointer',
    clickable: true
  }
  this.map.add(new AMap.CircleMarker(options)); // 往地图中添加圆点实例
},
```

绘制折线 `AMap.Polyline`，同上，`首次` 点击创建折线实例并添加到地图实例中，`第二次` 点击，该折线实例必定存在，则无需重复创建折线实例。这里使用 `Polyline` API 提供的 `setPath()` 成员函数，该成员函数接受组成该折线的 `节点数组`



>这里在写的时候遇到一个问题是 `setPath()` 无法保存之前的路径，每次点击之前的路径都会消失，最后得知问题所在是 `setPath()` 是覆盖之前的路径不是累加，所以就要把当前点击的路径 `push` 到 `this.polylinePath` 数组中，再把整个路径数组传给 `setPath()`

PS：高德提供的API太少了，希望以后推出 `addPath()`


{% image https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@0a476179ce09a62dc52cdf3c1f33c9432f77f0bf/2020/11/11/fe66f2174863a2253bbb21eaac29def2.png %}

```js
drawPolyline(path) {
  if (!this.polyline) {
    const options = {
      strokeWeight: 10, // 轮廓线宽度
      strokeColor: '#fa8c16', // 线条颜色
      strokeOpacity: 1, // 轮廓线透明度0表示完全透明，1表示不透明。默认为0.5
      lineJoin: 'round', // 折线拐点连接处样式
      lineCap: 'round', // 折线两端线帽的绘制样式
      showDir: true // 是否延路径显示白色方向箭头
    }
    this.polyline = new AMap.Polyline(options);
    this.map.add(this.polyline);
  }
  // 清除地图覆盖物后需要重新挂载折线实例
  if (!this.polyline._map) {
    this.map.add(this.polyline);
  }
  this.polylinePath.push(path); // 点击一次把路径点 push 到 this.polylinePath 数组中
  this.polyline.setPath(this.polylinePath); // 把路径点列表传给 setPath()
},
```

## 清除地图覆盖物

```js
clearMap() {
  this.polylinePath = [];
  this.map.clearMap()  	// API提供的清除函数（删除地图上所有的覆盖物）
},
```
## 地图销毁
```js
  this.map.destroy()	// 注销地图对象，并清空地图容器
```
## 效果图

![GIF](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@300ba388c86219c561684f9a2587f8831bfc1ddd/2020/11/11/347170ba58d7eba26fce38d8ce88634e.png)



总结完毕🎉！

## 结语
> 「小时候不理解老人晒太阳，一坐就是半天，长大才明白：目之所及，皆是回忆；心之所想，皆是过往；眼之所看，皆是遗憾。」———— 佚名

















