---
date: 2019-10-17
title: Vue 配合后台实现 wd 文件导出功能
categories: [Vue]
tags: [导出, 笔记]
---

## 开始
在 `vue` 框架中，与传统的根据路径下载文件 `document.getElementById("").src=''` 方式不同，有时候，我们会需要将上传的文件在后台直接进行处理再回传到前端，这种情况下文件没有实际的可获取的路径，无法通过链接方式下载。但是可以通过将其转成blob对象，添加到 `iframe` 标签中来模拟下载（或者pdf预览）。具体的接收方式如下:

## 效果图
![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@4558c546d365b220a822fe2c5d93f143eab53972/2020/10/14/0f0b293e3508d8d69dae7c886fb13c98.png)

## 代码

新建 `fileDownload.js` 插入以下代码
```js
import axios from 'axios'

/**
 * 封装导出Excel文件请求
 * @param url
 * @param data
 * @returns {Promise}
 */
export function exportExcel(url,params) {
	return new Promise((resolve, reject) => {
		axios({
			method: 'post',
			url: url, // 请求地址
			data: params, // 参数
			responseType: 'blob' // 表明返回服务器返回的数据类型
		}).then( response => {
				console.log(response);
				resolve(response.data)
				let blob = new Blob([response.data], {
					type: 'application/vnd.ms-excel'
				})
				// 获取响应头里的 filename
				let fileName = response.headers['content-disposition'].split(";")[1].split("filename=")[1];
				fileName = decodeURI(fileName);	// 解码
				if (window.navigator.msSaveOrOpenBlob) {
					navigator.msSaveBlob(blob, fileName)
				} else {	// 非 ie下载
					var link = document.createElement('a')
					link.href = window.URL.createObjectURL(blob)
					link.download = fileName
					link.click()
					//释放内存
					window.URL.revokeObjectURL(link.href)
				}
			},
			err => {
				reject(err)
			}
		)
	})
}
```
在需要的组件中引用上面的 `fileDownload.js`
```js
import { exportExcel } from '@/utils/fileDownload';
```
调用

```html
 <el-button
 	type="success"
 	size ="mini"
 	icon="el-icon-download"
 	@click='onDownload()'>导出
 </el-button>
```

```js
 // 导出
onDownload(){
    let url = '/roof/api/driving/queryDataExcel'  			// 请求接口
    let params = {} 							// 参数
    exportExcel(url, params)
}
```

## 问题笔记

### vue axios 设置 `responseType: 'blob'` 无效
![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@ab59656b8af824691ea6f81aae2aee9205212306/2020/10/14/24d7bdf2bb071e0ef9dfe37a65711f1c.png)


{% note color:yellow `mock`模块会影响原生的`ajax`请求，使得服务器返回的`blob`类型变成乱码 %}

去除 mock 后
![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@809e3021f73776dda6686a6191573d038a646b60/2020/10/14/84de21e5029c15794cd3b76c285b2ecb.png)

### 后台通过响应头返回的文件名导致前台获取导致乱码
`F12` 查看响应头没问题
![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@f9f092c8fc1d0fa54c5a3d8bfd43fecdca2989bc/2020/10/14/70e5329ffd07d4b4496942c2875d3cdb.png)
但是 `console.log()` 打印后却出现乱码
![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@48446677b1bd0f32a33a8d7f937588fccacc53f7/2020/10/14/887615754f29892a4938847ed648aec3.png)
最终无解，只能另辟蹊径
![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@25190c0ab019774b0f6e7260b2073c49f8c9d437/2020/10/14/2f9525c32fd25207d49d853ef64c1f39.png)
让后端使用 `URLEncoder.encode(fileName, "UTF8")` 对文件名进行编码处理!!一堆百分号那种东西!!直接返回给前端
![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@678b453cd77d7b105847979969830435325a797c/2020/10/14/caf5e5095a8908ef06e3eb984d73bb7d.png)
前端则使用 `decodeURI(fileName)` 进行解码，效果图看 gif

```js
// 获取响应头里的 filename
let fileName = response.headers['content-disposition'].split(";")[1].split("filename=")[1];
	fileName = decodeURI(fileName);	// 解码
```
总结完毕！

## 结语
>在狂热思绪下喊出的爱情宣言中，存在着爱情的实体