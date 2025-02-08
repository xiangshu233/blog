---
title: 动态组件渲染为 Cascader，所有选项为空的 Bug
tags: [vue3, element-plus]
categories: [笔记, Vue3]
date: 2024-02-09 17:12:15
description:
cover: https://03c068f.webp.li/i/2025/02/08/vlw84-p3.jpg
---

今天封装一个简易动态表单组件的时候遇到了一个的 Bug 分享下，避免踩坑。我使用动态组件来加载一些表单组件，对于大多数组件来说，插槽（slot）是不必要的，但有些控件则需要。比如 `el-select` ，需要 `slot` 来挂载选项

```html
<Component
  v-bind="column.attrs"
  :is="mapComponentType(column.attrs)"
  v-model="formModel[column.formItem.prop]"
  v-on="handleEvents(column.events)"
>
  <template v-if="column.attrs.component === 'Select'">
    <el-option
      v-for="option in column.attrs.options"
      :key="option[column.attrs.fieldNames?.value ?? 'dictCode']"
      :label="option[column.attrs.fieldNames?.label ?? 'dictLabel']"
      :value="option[column.attrs.fieldNames?.value ?? 'dictCode']"
    />
  </template>
</Component>
```

在 `Component` 渲染为 `select` 的时候单独处理 `el-option`，其他组件的时候则不渲染。这样看着似乎没有任何问题，因为我们的认知中 `v-if` 是不会渲染这部分内容的，但是当 `Component` 渲染为 `Cascader` 组件的时候，展开 `Cascader Options` 发现是空白的

![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2024/01/202402102122227.png)

一开始毫无头绪，完全不知道哪里出了问题，代码看着也很正常，也没有报错信息。于是我就去 `element-plus` Github 翻 `issues`，果然还真有人遇到了一样的问题，这里有个搜索 `issues` 技巧

可以根据你的问题 `关键字` 去快速搜索到目标 `issue`

![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2024/01/202402102122065.png)



https://github.com/element-plus/element-plus/issues/5428

可以看到和我遇到的是同样问题

![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2024/01/202402102123563.png)

这个回答说 `<slot>` 引起的，当我把 `Component` options 删掉后，果然 `Cascader` 正确渲染出来了，看来果然是 `<slot>` 引起的

![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2024/01/202402102123018.png)

![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2024/01/202402102123803.png)


{% note color:yellow 注意
  这里不能直接注释掉 options，因为 `注释` 也是一个元素，具体原因接着往下看
%}

![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2024/01/202402102123185.png)

![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2024/01/202402102124629.png)

可以看到你即便是**注释掉**它也会占据 default slot，接着看下面一个回答

![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2024/01/202402102124298.png)

https://github.com/element-plus/element-plus/blob/089064c7f29ce284a807cbd20efc3c802bab23fa/packages/components/cascader/src/cascader.vue#L150

可以看到 `Cascader` 组件的源码 `renderLabel` 这里接收了一个 `default slot`，再来说说上面的为什么加了 `v-if` 后它还是被当做 `default slot` 渲染了，其实很简单，你只需要检查开发工具上的元素即可看到图中 `v-if` 是个注释标签，他被渲染进了一个 class 名为 `el-cascader-node__label` span 标签中，阻挡了默认内容

![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2024/01/202402102124899.jpg)

我们期望的渲染应该是这样的



![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2024/01/202402102124588.png)

在 `Cascader` 组件源码里也能找到同样的做法

![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2024/01/202402102124255.png)



这个注释的意思是在 `el-radio` 组件内部使用一个空的 `<span />` 元素进行占位。

解释：
在 `Vue` 中，如果你想要**防止一个组件渲染其默认插槽的内容**，但又不想完全移除这个插槽（可能是因为样式或脚本上的需要），你可以插入一个空的元素来**“占位”**。这里使用了一个空的 `<span />` 元素。



打开这个 [#2485](https://github.com/vuejs/vue-next/pull/2485) pr 找到 [#2347](https://github.com/vuejs/core/issues/2347) 这个 issue，它里面也有一个 `v-if` 注释，跟我们的情况一样，同样是阻止了插槽组件的默认插槽渲染。这下明白了吧，**包括注释也会阻止插槽组件使用默认值**

另外这个 issue 的标题为：**任何内容都会阻止插槽组件使用默认值#2347**，标题已经告诉我们答案了，即任何内容都会阻止插槽组件的 `default slot` 渲染，包括注释

![image](https://user-images.githubusercontent.com/40221744/95645115-9bf5bf00-0aee-11eb-984b-f85642bf8019.png)

![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2024/01/202402102125306.png)

再看这个回答，他给出了两个环境示例（可以自行去查看）

**global** 环境下默认插槽没有被渲染出来

**prod** 环境下则渲染出来了，这个回答里也明确说了生产环境下注释会被删掉，自然就能渲染出来了

（所以这只是一个 dev 下的 bug ？）


知道了根本原因就好办了。

接下来回到我们的动态表单组件中去，我能想到最简单最稳妥方法就是单独处理 `select` 这类组件了，其他组件照常渲染。

（如果还有更好的解决方案请在评论区指指点点我）

![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2024/01/202402102125464.png)


最后，2024 新年快乐
