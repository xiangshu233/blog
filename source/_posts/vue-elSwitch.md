---
date: 2019-11-21
title: el-table 中使用 switch 开关
categories: [Vue, Element]
tags: [vue, element]
cover: https://tva4.sinaimg.cn/mw690/006wuklaly1gjrf260zfej32dc0o0noc.jpg
---

## 需求说明
- [x] 是根据后台返回 `0(停用)` 和 `1(启用)`  动态显示开关
- [x] 对开关进行操作时请求后台，需要传两个参数：`id`，`status`
- [x] 启用时传 `1`  停用时传 `0` 并携带 `id` ,当主机下面包含通道是则不能改变`状态`，并根据给出提示
![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@09077844f9e44f3ddc2c5a5d44fa979cd7c44c1d/2020/10/13/637c81c7febcfb4b281cf2b7b13680b2.png)

## 套用
```js
<el-table-column
	prop="status"
	label="状态"
	align='center'
	width="150">
	<template slot-scope='scope'>
		<el-switch
			v-model="scope.row.status"
			active-color="#13CE66"			// 开启颜色
			inactive-color="#C0CCDA"		// 关闭颜色
			:active-value="1"			// 判定数值1为开启
			:inactive-value="0"			// 0为关闭
			@change=changeComputerStatus($event,scope.row)>
		</el-switch>
		{{ scope.row.status === 0 ? " 停用" : " 启用" }}
	 </template>
</el-table-column>
```

`@change`事件函数

```js
// 改变主机状态： 1 开启 / 0 关闭
changeComputerStatus(changeStatus, row){
	const para = {id: row.id, status: changeStatus}		// 参数
	updateComputerStatus(para).then(res => {	// 后台请求
		this.$message({
			message: res.msg,
			type: 'success'
		})
		this.getComputerPage()	// 重新获取table列表
	}).catch(() => {
		row.status = !changeStatus * 1 // 失败保持switch点击前的状态
	})
},
```

## 效果图

![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@035545a0b4e4dc4d8e02b94302e75000193c4785/2020/10/13/75ba1d748d56835654a517646a8343b6.png)


>`changeStatus`是改变的值，业务场景是如果这台主机下面有通道且是开启状态，则不能关闭该主机，执行后，后端则返回状态码`300`，进入`catch`语句此时>`changeStatus 默认为 0`，前面加上 `!`  后它的返回值却成了`boolean`类型`true`，我们需要的是 `1`，而不是`true` ,这种情况只需后面`* 1` 就可以将>`true`转换成 `1`


![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@4134178f918946ef9ecb5f8a01e11f4f0f4d10c3/2020/10/13/830d9657bcef7320973a7d222a3973d4.png)

API 参数

| 参数           | 说明                  | 类型                      | 可选值 | 默认值  |
| :------------- | :-------------------- | :------------------------ | :----- | :------ |
| active-value   | switch 打开时的值     | boolean / string / number | —      | true    |
| inactive-value | switch 关闭时的值     | boolean / string / number | —      | false   |
| active-color   | switch 打开时的背景色 | string                    | —      | #409EFF |
| inactive-color | switch 关闭时的背景色 | string                    | —      | #C0CCDA |
