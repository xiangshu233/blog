---
date: 2020-10-13
title:  在 vue 项目中使用 iconSvg
categories: [Vue, iconSvg封装]
tags: [Vue, iconSvg]
---

以前我的项目 Icon 都是使用 [iconfont ](https://www.iconfont.cn/home/index?spm=a313x.7781069.1998910419.2) 来下载 SVG，然后在项目中使用这些 SVG

```js
<img :scr=svgLogo />
...
<script>
import svgLogo from '@/assets/svg/handsome.svg'
</script>
```
代码现场手打，复制绝对报错。你想想每使用一个 `svg` 就要来这么一段，一看就让人内牛满面，后来知道了这个东西：`svg-sprite-loader`，大概的意思就是把 `svg` 转成了 `html` 的 `symbol` 标签，当然 98% 的人不会关心你到底是 `symbol` 还是 `png`，最主要是，引入要`优雅` `流畅` `富有艺术气息`。
下面是封装好的 `iconSvg` 组件 是不是感觉 `优雅` `流畅` `富有艺术气息`了呢

```html
<icon-svg icon-class="xxxxxxx" />
```
>icon-class 里是 svg 的文件名


本文尽量简洁干练，为了方便大部分着急解决问题、准备{% kbd Ctrl %} + {% kbd C %} + {% kbd V %} 连击直接带走的朋友，接下来我们开始吧
```Bash
Vue Create vue-demo
```
![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@74b3db48b0cd7df9fc783bd2d4b0942e3c488252/2020/10/13/cd804bc58e0bf99106fbdde9ce319ddf.png)
好好好，有话好好说
![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@4ddaaeea22658d3d1ad17ceee5d7676e40c56550/2020/10/13/52ba6cc2cfb9e0b03c08320808aa50a0.png)

## 安装插件

安装 `svg-sprite-loader`
```Bash
yarn add svg-sprite-loader --save-dev
```
## 配置插件

在 `vue.config.js` 添加如下配置

``` js
// webpack相关配置
chainWebpack: (config) => {
	// 配置别名
    config.resolve.alias.set("@", resolve("src"));
    // set svg-sprite-loader
	config.module.rules.delete("svg");		// 干掉module下名字叫svg的默认配置项
	config.module
		.rule("svg-smart")
		.test(/\.svg$/)				// 正则匹配文件名规则，就是匹配 .svg 文件
		.include.add(resolve("@/icons/svg"))	// 处理你的 svg 目录
		.end()
		.use("svg-sprite-loader")
		.loader("svg-sprite-loader")
		.options({
			symbolId: "[name]"
		});

    // other set ...
}
```

>[chainWebpack详解](https://cli.vuejs.org/zh/guide/webpack.html)


## 创建iconSvg组件

在 `src/components` 文件夹内新建 `iconSvg.vue` 文件，复制以下内容

```js
<template>
	<svg class="svg-icon" aria-hidden="true">
		<use :xlink:href="iconName"></use>
	</svg>
</template>

<script>
	export default {
		name: 'icon-svg',
		props: {
			iconClass: {
			type: String,
			required: true
			}
		},
		computed: {
			iconName() {
			return `#${this.iconClass}`
			}
		}
	}
</script>

<style>
	.svg-icon {
		width: 1em;
		height: 1em;
		font-size: 18px;
		vertical-align: -0.15em;
		fill: currentColor;
		overflow: hidden;
	}
</style>
```



## 创建svg目录

在 `src` 下新建 `icons/svg` 目录，并把需要的 `.svg` 文件放入 `svg` 文件夹内
![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@5b15fcc8cbdf79bbad436a5643402e01c1a1a529/2020/10/13/f5a01aeea5805fdf8c189c39c11cecdb.png)
在 `/icons` 目录下新建 `index.js` 放入以下代码

``` js
import Vue from 'vue'
import IconSvg from '@/components/iconSvg/IconSvg'
// 全局注册icon-svg
Vue.component('icon-svg', IconSvg);

// 这个我也不知道啥意思
const req = require.context('./svg', false, /\.svg$/)
const requireAll = requireContext => requireContext.keys().map(requireContext)
requireAll(req)
```

## 引用
在 `main.js` 中引用 `icons` 文件夹
``` js
import '@/icons'
```
{% image https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@7ba34ff267df2a55702f5325919d1048746c09d3/2020/10/13/976b85158272c8ebb1d5591e5ba94e64.png %}


## 使用

之后再需要的页面引用 即可

```js
<icon-svg icon-class="zanwushuju" />
```

## 结语
如今的我，谈不上幸福，也谈不上不幸