---
date: 2020-8-27
title: 鼠标拾取x y坐标绘制线功能记录
categories: [Vue]
tags: [vue, svg, 坐标拾取绘线]
cover: https://cdn.xiangshu233.cn/post/banner/wallhaven-0wvvv7.png
banner: https://cdn.xiangshu233.cn/post/banner/wallhaven-0wvvv7.png
references:
  - title: 'JavaScript 和 SVG 实现点击连线'
    url: https://www.cnblogs.com/xiaozhuzhuandxiaomoney/p/7570765.html
---

## 项目需求
最近项目上有个需求是点击弹窗显示一张地图（图片），用户可以在地图上点击拾取当前坐标的 `x y` 并绘制成线，以达到在地图上做标注的功能，直接使用地图框架显得太重，于是要求手写一个 `svg` 画线功能，并将点击的 `x y` 坐标提交到后台用以回显

## 开始
好在百度了一篇有个画线功能的 [demo](https://www.cnblogs.com/xiaozhuzhuandxiaomoney/p/7570765.html) ,不过这个 `demo` 是 js 写的，还要改成 `Vue` 写法 {% emoji aru 0160 height:3em %}

## 第一版
完全按照 `demo` 逻辑书写的，`方法繁杂，可读性差、过度操纵Dom`
```js
//创建直线
var line = document.createElementNS(svgns,"line");
line.setAttribute("x1",dot1.style.left);
line.setAttribute("y1",dot1.style.top);
line.setAttribute("x2",dot1.style.left);
line.setAttribute("y2",dot1.style.top);
line.setAttribute("stroke","red");
line.setAttribute("fill","none");
svger.appendChild(line);
```

## 第二版
删除多余的动画效果，原 `demo` 点与线分别是 `两个层级div` 结构太冗余，对 `Dom` 结构进行了重新调整，原 `demo` 坐标单位是 `px`，本项目功能地图标记需要在首页展示由于大小尺寸不同 `px` 会导致标记位置发生偏移，故修改成 `%` 单位，只要保持比例，标记信息就不会偏移，以下为核心代码

```js
// 此代码是设置不同比例的地图容器
// 获取图片真实大小
const imageSize = res.availableMapImage.imageSize.split(',');  // 真实图片大小 "2577,1721"
// 1200-40 是弹窗的宽度 1200 再减去 padding 40
// 用原始图片的宽度除以弹窗的宽度获取倍数 N， 例：1.25倍
let N = imageSize[0] / (1200 - 40);
// 再用真实图片宽高除以倍数获取不同比例的图片大小
this.passMapSetting.mapSize.width = imageSize[0] / N;
this.passMapSetting.mapSize.height = imageSize[1] / N;

// posX, posY 为鼠标拾取坐标值
// 用 x, y 坐标值除以容器的宽高*100 得到百分比x y坐标
const container = this.$refs.container;
dotObj.x = ((posX / container.clientWidth) * 100);
dotObj.y = ((posY / container.clientHeight) * 100);
```

## 第三版
基本进行重写，实现 `零DOM` 操作，完全数据操纵，这也是 Vue 所提倡的：`数据驱动模型`，
**以下是点绘制成线并实现连接逻辑梳理**

首先在 `data` 中定义两个数组（用以存储点和线的数据模型）和一个 `boolean` 的值（用于清除标记函数判断）

```js
data() {
    return {
        allDotsXY: [],
        allLinesXY: [],
        rePaintFlag: true
    };
},
```
绑定点击事件，拾取当前点击的坐标值，并作为参数传给 `createDotAndLine()` 函数

> `rePaintFlag` 默认为 `true` 点击编辑回显绘制线路的时候条件成立，再次点击就表示要重绘，即清空原数据模型


```js
clickContainer(event) {
    if (this.rePaintFlag) {
    	this.removeMark();
    }
    let mousePosition = this.mousePos(event);
    this.createDotAndLine(mousePosition.x, mousePosition.y);
},

/**
* 返回当前鼠标点击的 x y
*/
mousePos(e) {
	// e.layerX —— 相对当前坐标系的border左上角开始的坐标
    if (e.layerX) {
    	return { x: e.layerX, y: e.layerY };
    }
},
```
在 `createDotAndLine` 函数中定义两个对象 `dotObj lineObj` 用来构造点的数据模型和线的数据模型，并将 `dotObj.x dotObj.y` 坐标值处理成 `%` 单位，每点击一次就往 `allDotsXY` push当前坐标值，其中定义 `lastDot` 用来存储本次点击的前一个元素，所以要在 `当前点击的坐标 push` 前存储，为`lineObj` 对象构造做准备。

```js
const dotObj = {};
const lineObj = {};
const container = this.$refs.container;

dotObj.x = ((posX / container.clientWidth) * 100);
dotObj.y = ((posY / container.clientHeight) * 100);
// 第一次为 null, 第二次获取 push 前的元素即第 0 个，每次都获取前一个元素
const lastDot = this.allDotsXY.length > 0 ? this.allDotsXY[this.allDotsXY.length - 1] : null;
this.allDotsXY.push(dotObj);
```
要想让 `两条线实现互连` 则需要这种格式的数据

```js
allLinesXY: [
                {
                    x1: 30
                    y1: 69
                    x2: 70	 //上一个线的 x2 是下一个线的 x1
                    y2: 80	 // ...
               },
               {
                    x1: 70	 //... 即第二条线的开头是第一条线的结尾
                    y1: 80	 //...
                    x2: 55
                    y2: 36
               }
            ]

// 例如： [{1234},{3456},{5678}]
```
所以只需要每次点击，把上一次点击的 `x y` 临时存储到 `lastDot` 中即可，如果 `this.allDotsXY` 长度大于 `1` 即开始构造 `lineObj` 对象

```js
lineObj.x1 = lastDot.x;
lineObj.y1 = lastDot.y;
lineObj.x2 = ((posX / container.clientWidth) * 100);
lineObj.y2 = ((posY / container.clientHeight) * 100);
```
以下是 `createDotAndLine()` 函数完整代码，第一次点击构造点数据模型并 `push` 到 `allDotsXY` 数组中，第二次点击 `allDotsXY` 满足条件开始构造线数据模型以此类推...

```js
/**
 * 创建点线
 */
createDotAndLine(posX, posY) {
	const dotObj = {};
	const lineObj = {};
	const container = this.$refs.container;

	dotObj.x = ((posX / container.clientWidth) * 100);
	dotObj.y = ((posY / container.clientHeight) * 100);
	// 第一次为 null, 第二次获取 push 前的元素即第 0 个，每次都获取前一个元素
	const lastDot = this.allDotsXY.length > 0 ? this.allDotsXY[this.allDotsXY.length - 1] : null;
	this.allDotsXY.push(dotObj);
	console.log('add-dots: ', this.allDotsXY);

	if (this.allDotsXY.length > 1) {
		lineObj.x1 = lastDot.x;
		lineObj.y1 = lastDot.y;
		lineObj.x2 = ((posX / container.clientWidth) * 100);
		lineObj.y2 = ((posY / container.clientHeight) * 100);
		this.allLinesXY.push(lineObj);
		console.log('add-lines: ', this.allLinesXY);
	}
},
```
数据模型已经构造完毕，剩下只需在 `<template>` 中使用 `v-for` 遍历两个数组即可

```js
<template>
	<!-- 点击事件容器	 -->
	<div
		class="container"
		ref="container"
		:style="{
          width: passMapSetting.mapSize.width + 'px',
          height: passMapSetting.mapSize.height + 'px'
    	}"
		@click="clickContainer($event)"
	>
        <!-- 地图	 -->
		<img class="map" :src="passMapSetting.mapUrl" />
        <!-- svg 容器	 -->
		<svg ref="svgContainer">
           <!-- 线 -->
			<line
				class="line"
				v-for="(item, index) in allLinesXY"
				:key="index+Math.random()"
				:x1="`${item.x1}%`"
				:y1="`${item.y1}%`"
				:x2="`${item.x2}%`"
				:y2="`${item.y2}%`"
			/>
            <!-- 点（圆） -->
			<circle
				v-for="(item, index) in allDotsXY"
				:key="index+Math.random()"
				:cx="`${item.x}%`"
				:cy="`${item.y}%`"
				r="7"
				fill="#52c41a"
			/>
		</svg>
	</div>
</template>

```
最后清除标记函数，这很好的体现了`Vue`数据响应式的特性，只需置空两个数组即可，让我们摆脱繁杂的 `Dom` 操作，只关注数据

```js
/**
 * 清除标记
 */
removeMark() {
	this.allDotsXY = [];
	this.allLinesXY = [];
	this.rePaintFlag = false;
}
```

{% note color:yellow
rePaintFlag 默认为 true，则点击编辑回显绘制线路的时候执行 removeMark() 手动改为 false, 防止下次点击再次执行
%}

## 效果图
![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@23a344d00588f4bde766ccc7410616e7100907ce/2020/10/13/45973bbd1c8789e797b642f7183bea97.png)

## 提交后台
后台接收字段值为字符串，父组件里点击保存按钮执行 `saveData()`
```js
<el-button icon="el-icon-check" type="primary" @click="saveData()">保 存</el-button>
```
封装好的子组件

```js
<!-- 绘制通道线路组件 -->
<passLineMarking
    ref="lineMarkingComps"
    :passMapSetting="passMapSetting"
    @saveAllDots="saveAllDots"
>
</passLineMarking>
```
执行子组件的 `handleAllDots()` 函数
```js
// 保存点数据
saveData() {
	this.$refs.lineMarkingComps.handleAllDots();
},
```
定义一个变量将 `coordinate` 将 `allDotsXY` 数组遍历拼接成字符串并提交至父组件函数 `saveAllDots()`

```js
/**
 * 处理所有点的数据
 * 并将处理好的字符串提交到父组件方法
 * saveAllDots
 */
handleAllDots() {
	let coordinate = '';
	this.allDotsXY.map((item) => {
		coordinate += `${item.x},${item.y};`;
	});
	console.log(coordinate);
    // 执行父组件函数 saveAllDots
	this.$emit('saveAllDots', coordinate);
},
```
父组件函数 `saveAllDots()`，接收来自子组件参数，赋值给 `from.coordinate` 字段，之后表单提交至后台，并关闭弹窗。
```js
//coordinate = '5.258620689655174,16.903225806451612;27.327586206896555,49.16129032258065;24.39655172413793,66.19354838709678;18.879310344827584,81.54838709677419;'
saveAllDots(coordinate) {
    this.form.coordinate = coordinate;
    this.innerVisible = false;
},
```
## 地图线路回显
接收来自后台返回的坐标串，点击弹窗 判断如果 `this.form.coordinate` 有值则表示是点击的是 `编辑按钮` 就要进行回显，执行子组件的 `showPassLine()` 函数，并把 `this.form.coordinate` 作为参数携带过去
```js
// 打开嵌套弹窗（地图）
openInnerDialog() {
	this.innerVisible = true;
	// 如果是编辑则绘制后台返回的坐标
	if (this.form.coordinate) {
		this.$nextTick(() => {
			this.$refs.lineMarkingComps.showPassLine(this.form.coordinate);
		});
	}
},
```
接收父组件传过来的参数并进行数据模型构造
```js
coordinate.slice(0, (coordinate.length - 1)).split(';')
```
`slice` 函数裁剪从零到串的长度减一就是就是裁剪掉最后一个分号（防止split分割出一个空数组），并以分号为结尾分割串返回数组类型
`coordinate` 分割后的数据结构


{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@0cd5b79a0a4e0cc9e753149093ddbff69a1de951/2020/10/13/61fe49f494adfcf8f6ba129aaee79f9a.png %}

遍历分割后的数组，利用 `数组解构` 以逗号分割 `每个元素` 复制 `x y` 并返回到 `this.allDotsXY` 数组中
```js
this.allDotsXY = coordinate.map((item) => {
    // 利用数组解构
    const [x, y] = item.split(',');
    return { x, y };
});
```
`this.allDotsXY` 处理好的数据结构

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@4bd665b3d866b1b7ea16e3211cb5c7d05784af21/2020/10/13/bb3c9b389fa0f2f7dc664bc22dc77c2c.png %}

最后根据 `this.allDotsX` 构造 `this.allLinesXY` 数据模型
```js
for (const [index, dot] of this.allDotsXY.entries()) {
    // 假设 this.allDotsXY 长度为 4 - 1 （四个点三条线，五个点四条线...）
    // 第一次循环判断 0 < 3 成立 将本次 item 属性构建到 lineObj 以及下次的元素（index + 1）组合成 x1, y1, x2, y2
    // 第二次循环判断 1 < 3 成立 ...
    // 第三次循环判断 2 < 3 成立 ...
    // 第四次循环判断 3 < 3 不成立结束构造
    if (index < this.allDotsXY.length - 1) {
        const lineObj = {};
        lineObj.x1 = dot.x;
        lineObj.y1 = dot.y;
        lineObj.x2 = this.allDotsXY[index + 1].x;
        lineObj.y2 = this.allDotsXY[index + 1].y;
        this.allLinesXY.push(lineObj);
        console.log('this.allLinesXY: ', this.allLinesXY);
    }
}
```
最终 `this.allLinesXY` 数据模型构造完毕，回到开始数据驱动模型，`数据存在则视图就会更新`，整个回显完全不需要关注视图层，只需要构建数据模型

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@f21e0b33e416a6d206e55bc9328681676167fc0e/2020/10/13/460b9c1c7ab83357809dfa41be81b9ff.png %}

`showPassLine` 函数完整代码
```js
/**
 * 回显线路（编辑）
 */
showPassLine(coordinate) {
	this.allDotsXY = coordinate.slice(0, (coordinate.length - 1)).split(';').map((item) => {
		// 利用数组解构
		const [x, y] = item.split(',');
		return { x, y };
	});
	for (const [index, dot] of this.allDotsXY.entries()) {
		if (index < this.allDotsXY.length - 1) {
			const lineObj = {};
			lineObj.x1 = dot.x;
			lineObj.y1 = dot.y;
			lineObj.x2 = this.allDotsXY[index + 1].x;
			lineObj.y2 = this.allDotsXY[index + 1].y;
			this.allLinesXY.push(lineObj);
			console.log('this.allLinesXY: ', this.allLinesXY);
		}
	}
},
```
`passLineMarking` 点击连线组件完整代码
```js
<template>
	<div
		class="container"
		ref="container"
		:style="{
      width: passMapSetting.mapSize.width + 'px',
      height: passMapSetting.mapSize.height + 'px'
    }"
		@click="clickContainer($event)"
	>
		<img class="map" :src="passMapSetting.mapUrl" />
		<svg ref="svgContainer">
			<line
				class="line"
				v-for="(item, index) in allLinesXY"
				:key="index+Math.random()"
				:x1="`${item.x1}%`"
				:y1="`${item.y1}%`"
				:x2="`${item.x2}%`"
				:y2="`${item.y2}%`"
			/>
			<circle
				v-for="(item, index) in allDotsXY"
				:key="index+Math.random()"
				:cx="`${item.x}%`"
				:cy="`${item.y}%`"
				r="7"
				fill="#52c41a"
			/>
		</svg>
	</div>
</template>

<script>
export default {
	data() {
		return {
			allDotsXY: [],	// 所有点数据模型
			allLinesXY: [],	// 所有线数据模型
			rePaintFlag: true	// 重绘标识
		};
	},

	props: {
		passMapSetting: Object
	},

	computed: {

	},

	created() {

	},

	mounted() {

	},

	methods: {
		/**
		 * 点击容器事件
		 */
		clickContainer(event) {
			if (this.rePaintFlag) {
				this.removeMark();
			}
			let mousePosition = this.mousePos(event);
			this.createDotAndLine(mousePosition.x, mousePosition.y);
		},

		/**
		 * 返回当前鼠标点击的 xy
		 */
		mousePos(e) {
			// e.layerX —— 相对当前坐标系的border左上角开始的坐标
			if (e.layerX) {
				return { x: e.layerX, y: e.layerY };
			}
		},

		/**
		 * 创建点线
		 */
		createDotAndLine(posX, posY) {
			const dotObj = {};
			const lineObj = {};
			const container = this.$refs.container;

			dotObj.x = ((posX / container.clientWidth) * 100);
			dotObj.y = ((posY / container.clientHeight) * 100);
			// 第一次为 null, 第二次获取 push 前的元素即第 0 个，每次都获取前一个元素
			const lastDot = this.allDotsXY.length > 0 ? this.allDotsXY[this.allDotsXY.length - 1] : null;
			this.allDotsXY.push(dotObj);
			console.log('add-dots: ', this.allDotsXY);

			if (this.allDotsXY.length > 1) {
				lineObj.x1 = lastDot.x;
				lineObj.y1 = lastDot.y;
				lineObj.x2 = ((posX / container.clientWidth) * 100);
				lineObj.y2 = ((posY / container.clientHeight) * 100);
				this.allLinesXY.push(lineObj);
				console.log('add-lines: ', this.allLinesXY);
			}
		},

		/**
		 * 处理所有点的数据
		 * 并将处理好的提交到父组件方法
		 * saveAllDots
		 */
		handleAllDots() {
			let coordinate = '';
			this.allDotsXY.map((item) => {
				coordinate += `${item.x},${item.y};`;
			});
			console.log(coordinate);
			this.$emit('saveAllDots', coordinate);
		},


		/**
		 * 回显线路（编辑）
		 */
		showPassLine(coordinate) {
			this.allDotsXY = coordinate.slice(0, (coordinate.length - 1)).split(';').map((item) => {
				// 利用数组解构
				const [x, y] = item.split(',');
				return { x, y };
			});
			console.log(this.allDotsXY);
			console.log(coordinate.slice(0, (coordinate.length - 1)).split(';'));

			for (const [index, dot] of this.allDotsXY.entries()) {
				// 假设 this.allDotsXY 长度为 4 - 1 （四个点三条线，五个点四条线...）
				// 第一次循环判断 0 < 3 成立 将本次 item 属性构建到 lineObj 以及下次的元素（index + 1）组合成 x1, y1, x2, y2
				// 第二次循环判断 1 < 3 成立 ...
				// 第三次循环判断 2 < 3 成立 ...
				// 第四次循环判断 3 < 3 不成立结束构造
				if (index < this.allDotsXY.length - 1) {
					const lineObj = {};
					lineObj.x1 = dot.x;
					lineObj.y1 = dot.y;
					lineObj.x2 = this.allDotsXY[index + 1].x;
					lineObj.y2 = this.allDotsXY[index + 1].y;
					this.allLinesXY.push(lineObj);
					console.log('this.allLinesXY: ', this.allLinesXY);
				}
			}
		},

		/**
		 * 清除标记
		 */
		removeMark() {
			this.allDotsXY = [];
			this.allLinesXY = [];
			this.rePaintFlag = false;
		}
	}


};
</script>

<style lang="less" scoped>
.container {
	position: relative;
	cursor: pointer;
	svg {
		height: 100%;
		width: 100%;
		position: absolute;
	}
	/deep/ .line {
		stroke: #FADB14;
		stroke-width: 5;
		fill: none;
	}
	.map {
		height: 100%;
		width: 100%;
		position: absolute;
	}
}
</style>
```
父组件引用
```js
<!-- 嵌套弹出框 -->
<el-dialog
	class="innerDialog"
	ref="innerDialog"
	width="1200px"
	:destroy-on-close="true"
	:title="passMapSetting.mapTitle"
	:close-on-click-modal="false"
	:visible.sync="innerVisible"
	append-to-body
>
	<!-- 点击连线组件 -->
	<passLineMarking
		ref="lineMarkingComps"
		:passMapSetting="passMapSetting"
		@saveAllDots="saveAllDots"
	></passLineMarking>

	<div slot="footer" class="dialog-footer btnMargin">
		<el-button icon="el-icon-magic-stick" type="warning" @click="rePaint()">重 绘</el-button>
		<el-button icon="el-icon-close" @click="innerVisible=false">取 消</el-button>
		<el-button icon="el-icon-check" type="primary" @click="saveData()">保 存</el-button>
	</div>
</el-dialog>
```
```js
// 组件引用
import PassLineMarking from './passDialogComps/passLineMarking';
// 注册组件
components: {
    PassLineMarking
},
```
父组件所依赖的方法
```js
// 打开嵌套弹窗（地图）
openInnerDialog() {
	this.innerVisible = true;
	// 如果是编辑则绘制后台返回的坐标
	if (this.form.coordinate) {
		this.$nextTick(() => {
			this.$refs.lineMarkingComps.showPassLine(this.form.coordinate);
		});
	}

},

// 处理点数据
saveData() {
	this.$refs.lineMarkingComps.handleAllDots();
},

// 保存所有点数据
saveAllDots(coordinate) {
	this.form.coordinate = coordinate;
	this.innerVisible = false;
},

// 重绘
rePaint() {
	this.$refs.lineMarkingComps.removeMark();
},
```
## 结语
整个功能从拾取坐标到绘制线路再到编辑回显整套流程已经梳理完毕。此笔记目的是为了加深印象，对数据构造、es6语法糖、Vue组件化、父子传值、数据响应、svg 有了一定的理解





