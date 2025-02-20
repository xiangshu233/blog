---
title: 浅谈 Atomic CSS
tags: [原子化 CSS, UnoCSS, Windi CSS, Tailwind CSS]
categories: [CSS]
cover: https://03c068f.webp.li/i/2025/02/07/18ygwl-gf.png
date: 2024-11-14 23:12:30
---

## Atomic CSS 的诞生背景

在看这篇文章之前，推荐可以先看一下 2022 年 2 月的这篇专访：[The Making of Atomic CSS: An Interview With Thierry Koblentz](https://css-tricks.com/thierry-koblentz-atomic-css) 在这篇里面提及了更多 Atomic CSS 出现的背景以及早期在 Yahoo! 内部的应用。
Atomic CSS 的概念由 Yahoo! 工程师 Thierry Koblentz（蒂埃里·科布伦茨 以下简称 TK）于 2013 年提出，最早见于他的经典文章 [挑战 CSS 最佳实践](https://www.smashingmagazine.com/2013/10/challenging-css-best-practices-atomic-approach/)，这篇文章为许多后来流行 CSS 库铺平了道路，当时，TK 的主管希望能在不更改 stylesheet TK 开发了一个「utility-sheet」静态 CSS 文件，包含各种 utility class，让工程师可以灵活调整样式。
随着需求增多，Yahoo! 的工程主管提出能否全面使用 utility class 重构首页。TK 因此开发了一套名为 Stencil 的静态 CSS 系统，并设定了固定的设计规则，比如 margin 只能用特定的 class 表示，比如 `margin-left-1`、`margin-left-2`、`margin-left-3`，每个值是 4 的倍数（即 4px、8px、12px），从而强制设计遵循一致的间距规则。然而，静态系统无法满足每个团队的不同需求，因为不同设计团队会有不同的 padding、margin、字体和颜色需求。因此，TK 进一步开发了 Atomizer 工具，它能根据页面内容动态生成 CSS。

Atomizer 的语法称为 ACSS，类似于现在的 Tailwind CSS，受到 Emmet 命名风格的启发。ACSS 在 Yahoo! 内部推广初期面临阻力，但实践证明，它能够有效减少代码体积，使项目跨页面保持一致性，实现「page agnostic」的特性，非常适合复杂的大型项目。

> “Page agnostic” 是指一种设计或代码能够在不同页面之间保持一致性和通用性，不依赖于特定页面的上下文或结构。在 CSS 设计中，page agnostic 意味着组件样式不会因为页面环境的变化而不同，这样无论将组件放在哪个页面，它的样式和表现都可以保持不变。

## 什么是 Atomic CSS？

这边直接取用 [Let's Define Exactly What Atomic CSS is ](https://css-tricks.com/lets-define-exactly-atomic-css/) 这篇文章给的定义：

> Atomic CSS is the approach to CSS architecture that favors small, single-purpose classes with names based on visual function.Atomic CSS is the approach to CSS architecture that favors small, single-purpose classes with names based on visual function.
>
> 原子 CSS 是一种 CSS 架构方法，倾向于使用基于视觉功能命名的小型单用途类

例如说像这样子的东西就是 Atomic CSS：

```css
.bg-blue { background-color: #357edd; }
.margin-0 { margin: 0; }
```

![image-20241114213316490](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2024/11/202411150154630.png)


## Atomic CSS 到底想解决什么问题？

请看 [挑战 CSS 最佳实践](https://www.smashingmagazine.com/2013/10/challenging-css-best-practices-atomic-approach/)

Atomic CSS 主要是为了解决传统 CSS 代码中类名重复、冗长和可维护性差的问题。它提倡使用非常小而专一的样式规则（原子级别的类），每个类只负责单一的样式属性，从而避免了复杂的、庞大的 CSS 文件。通过这种方式，开发者可以组合多个原子类来构建组件，而不需要为每个新组件或页面单独编写新的 CSS 规则。

Atomic CSS 解决的问题：
 - 冗余和膨胀：通过原子类减少重复的样式定义。
 - 上下文依赖和维护困难：原子类与上下文无关，避免了修改上下文时影响其他部分。
 - 样式冲突：原子类避免了因复杂选择器而引发的样式冲突。

适用场景：
 - 大型项目，尤其是当团队多人协作时，能大大减少 CSS 冲突和重复定义。
 - 快速开发原型或小型项目时，原子 CSS 让开发变得更为灵活高效。

## 跟 Inline Style 有什么不同？

其实本质上是一样的，都是把 style 限制在很小的 scope 里面，但 Atomic CSS 解决了 inline style 的几个坏处：

1. CSS 的优先顺序很高，很难盖过去
2. 很冗长
3. 不支持 pseudo-class 或是 pseudo-element

## 我对 Atomic CSS 的看法

首先，我认为 Atomic CSS 带来了两个独特的好处：

1. 降低 CSS 大小
2. 最大程度缩减 scope，让维护变得容易

第一点是显而易见的，这个应该就不用再多提了，CSS 档案大小会小很多，这一点是其他 CSS 的解决方案没办法做到的。

第二点是它通过使用单一功能的原子级 class，使每个 CSS 类只负责一个明确的小功能，例如设置特定的颜色或边距。这种方式避免了传统 CSS 中容易出现的样式层叠和覆盖问题，也减少了组件间样式的相互干扰（即「样式泄漏」）。

由于每个 class 都是独立、单一功能且不会影响其他样式，工程师只需关注单一 class 的作用范围，不用担心样式会在其他组件上意外生效。这种独立、清晰的样式结构让 CSS 维护更加直观和简单，即便项目规模庞大，也能够更轻松地定位和修改样式。

有些人可能会疑惑说：「但你讲的这个，CSS-in-JS（react） 或是 CSS modules（.vue 的 scoped） 也都做得到啊」，没错，这两个解决方案也可以解决 scope 的问题。但 Atomic CSS 刚诞生的时候似乎这些解决方案都还不存在（或是还在非常早期），所以这里比较的对象是传统 CSS 的解决方案（像是 OOCSS、BEM、SMACSS 这些管理 CSS 的方法）。

## 原子化框架/引擎

### TailwindCSS（先行者）

最大的优势是我认为它的 DX（开发人员体验）更为突出，例如说它使用了更好看懂的 class name，文档也更加完整，很快就可以查到什么语法应该怎样写，其次 TailwindCSS 生态比较好，社区非常活跃。
{% image https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets/2024/11/image-20241114103324214.png %}

### WindCSS (走向衰败 在 2023 年 3 月停止了*维护*)

[Windi CSS](https://cn.windicss.org/) 是从零开始编写的 Tailwind CSS 的替代方案。它零依赖，也不要求用户安装 PostCSS 和 Autoprefixer。更为重要的是，它支持 **按需生成**。Windi CSS 不会一次生成所有的 CSS，而是只会生成你在代码中实际使用到的原子化 CSS。它比 Tailwind 要快了 [20~100 倍](https://twitter.com/antfu7/status/1361398324587163648)。它直接影响了两个 Atomic CSS 框架 Tailwind CSS 和 UnoCSS，并 [因此](https://twitter.com/adamwathan/status/1371542711086559237?s=20) 启发后来的 Tailwind [JIT 按需引擎](https://tailwindcss.com/docs/just-in-time-mode) 功能（早期版本的 Tailwind 没有 JIT 按需引擎， Tailwind 在初始构建时会提前生成所有内容，导致生成了数 MB 的 CSS），另外 [Windi CSS](https://cn.windicss.org/) 也引入了许多创新，如 [自动值推导](https://cn.windicss.org/features/value-auto-infer.html)，[可变修饰组](https://windicss.org/features/variant-groups.html)，[Shortcuts](https://windicss.org/features/shortcuts.html)，[在 DevTools 中进行设计](https://twitter.com/antfu7/status/1372244287975387145)，[属性化模式](https://twitter.com/windi_css/status/1387460661135896577) 等来增加开发者体验。

#### JIT 按需生成

"按需生成" 的想法引入了一种全新的思维方式。让我们先来对比下这些方案：

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2024/11/202411150152324.png %}

传统的方式不仅会消耗不必要的资源（生成了但未使用），甚至有时更是无法满足你的需求，因为总会有部分需求无法包含在内。

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2024/11/202411150151653.png %}

通过调换 "生成" 和 "扫描" 的顺序，"按需" 会为你节省浪费的计算开销和传输成本，同时可以灵活地实现预生成无法实现的动态需求。另外，这种方法可以同时在开发和生产中使用，提供了一致的开发体验，使得 HMR (Hot Module Replacement, 热更新) 更加高效。

因此，通过按需生成方式，Windi CSS 获得了比传统的 Tailwind CSS [快 100 倍左右](https://twitter.com/antfu7/status/1361398324587163648) 的性能。

### UnoCSS (Windi CSS 的 "精神继承者")

建议你阅读由 UnoCSS 创建者 [Anthony Fu](https://antfu.me/) 撰写的博客文章 [重新想象原子 CSS](https://antfu.me/posts/reimagine-atomic-css)，以更好地理解 UnoCSS 背后的动机。

[**UnoCSS**](https://github.com/antfu/unocss) 具有高性能且极具灵活性的即时原子化 CSS 引擎。UnoCSS 是一个**引擎**，而非一款**框架**，因为它**并未提供核心工具类**，所有功能可以通过预设和内联配置提供。

> [**UnoCSS**](https://github.com/antfu/unocss) 是由 [Windi CSS](https://windicss.org/) 团队的一员（[Anthony Fu](https://antfu.me/)）发起的，我们在 Windi CSS 中的工作获得了很多灵感。虽然 Windi CSS 自 2023 年 3 月起不再积极维护，但你可以将 UnoCSS 视为 Windi CSS 的 *"精神继承者"*。
>
> UnoCSS 继承了 Windi CSS 的按需特性、[属性化模式](https://unocss-cn.pages.dev/presets/attributify)、[快捷方式](https://unocss-cn.pages.dev/config/shortcuts)、[变体组](https://unocss-cn.pages.dev/transformers/variant-group)、[编译模式](https://unocss-cn.pages.dev/transformers/compile-class)等多种特性。此外，UnoCSS 从一开始就以最大的可扩展性和性能为目标构建，使我们能够引入如 [纯 CSS 图标](https://unocss-cn.pages.dev/presets/icons)、[无值属性化](https://unocss-cn.pages.dev/presets/attributify#valueless-attributify)、[标签化](https://unocss-cn.pages.dev/presets/tagify)、[网络字体](https://unocss-cn.pages.dev/presets/web-fonts) 等新功能。
>
> 最重要的是，UnoCSS 被提取为一个原子 CSS 引擎，所有功能都是可选的，这使得创建自己的规范、自己的设计系统和自己的预设变得容易——通过你想要的功能组合实现。

{% image https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets/2024/11/why-unocss.svg why-uno-css %}

我们设想 UnoCSS 能够通过预设模拟大多数已有原子化 CSS 框架的功能。也有可能会被用作创建一些新的原子化 CSS 框架的引擎。例如：

```js
import { defineConfig, presetUno } from 'unocss'

export default defineConfig({
  // ...UnoCSS options
  presets: [
    // 此预设尝试提供流行的实用程序优先框架的通用超集，包括 Tailwind CSS、Windi CSS、Bootstrap、Tachyons 等
    presetUno(),
})
```

这样你就可以在你的项目里无缝使用任何原子化 CSS，不过 UnoCss 并没有像 Tailwindcss 那样详细的文档，正如开头所说 UnoCSS 是一个**引擎**，而非一款**框架**，你配置了什么预设就去看相关预设的文档，UnoCss 没必要再写一遍文档。所以就会出现一种奇特的画风，用着 UnoCss 查着 TailwindCss 的文档{% emoji blobcat ablobcatattentionreverse %}

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2024/11/202411142054453.png %}


## UnoCSS 预设

我们将通过 **`uno.config.ts`** 配置文件来了解 UnoCSS 最主要的功能

### 默认预设

UnoCSS 的默认预设，是 Tailwind CSS / Windi CSS 紧凑预设。

```js
import { presetUno } from 'unocss'

export default defineConfig({
  presets: [
    presetUno(),
  ],
})
```

该 [预设](https://unocss-cn.pages.dev/presets/uno) 试图提供一种常见的实用程序优先框架的超集，包括 Tailwind CSS、Windi CSS、Bootstrap、Tachyons 等。

例如，`ml-3`（Tailwind CSS）、`ms-2`（Bootstrap）、`ma4`（Tachyons）和 `mt-10px`（Windi CSS）都是有效的。

这里建议只使用 Tailwind CSS、Windi CSS 预设即可，另外你不必担心记不住这些 Class，配合 [Tailwind 文档 ](https://www.tailwindcss.cn/docs/installation)和 [UnoCSS 插件](https://marketplace.visualstudio.com/items?itemName=antfu.unocss) 可以迅速上手

```css
.ma4 { margin: 1rem; }
.ml-3 { margin-left: 0.75rem; }
.ms-2 { margin-inline-start: 0.5rem; }
.mt-10px { margin-top: 10px; }
```
{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2024/11/202411142222605.gif 使用 UnoCSS 插件 hover 原子样式可以看到 CSS 源 这非常有用 %}


### 纯 CSS 图标

关于实现原理推荐阅读：[纯 CSS 图标](https://antfu.me/posts/icons-in-pure-css)

{% note color:blue INFO
你可以单独使用此预设作为现有 UI 框架的补充，以获得纯 CSS 图标！
%}


从最初的 `<img>` 到 雪碧图 再到 `iconfont`，以及各种 `<IconSvg>` 组件，无一例外它们都需要先把资源文件放到本地 `assets/xxx` 目录下，虽然后来的 font 库，可以做到不使用本地图片了，但是可定制性差，数量少。而纯 CSS 图标使用方式就简单多了，你只需要到 [Iconify](https://icon-sets.iconify.design/) （超过 **200,000** 个开源矢量图标）站点，找到任意 icon 点击 > 选择 UnoCss > copy！然后粘贴到你的项目中即可完成图标的引入。

![image-20241114233216367](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2024/11/202411142332406.png)

强烈推荐使用 [Iconify IntelliSense](https://marketplace.visualstudio.com/items?itemName=antfu.iconify)，你可以获得获取图标的内嵌预览、自动补全和悬停信息。

![preview](https://github.com/antfu/vscode-iconify/blob/main/screenshots/preview-1.png?raw=true)

```html
<!-- 来自 Phosphor 图标库的基本锚点图标 -->
<div class="i-ph-anchor-simple-thin" />
<!-- 来自 Material Design 图标库的橙色警报 -->
<div class="i-mdi-alarm text-orange-400" />
<!-- 大型 Vue 标志 -->
<div class="i-logos-vue text-3xl" />
<!-- 亮色模式下的太阳，暗色模式下的月亮，来自 Carbon -->
<button class="i-carbon-sun dark:i-carbon-moon" />
<!-- Twemoji 的笑脸，悬停时变成泪水 -->
<div class="i-twemoji-grinning-face-with-smiling-eyes hover:i-twemoji-face-with-tears-of-joy" />
```

![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2024/11/202411142311344.gif)

> unocss 使用 [Iconify](https://iconify.design/) 作为图标数据源。您需要按照 `@iconify-json/*` 模式在 `devDependencies` 中安装相应的图标集。例如，Material Design Icons 的
> 图标集为 `@iconify-json/mdi`，Tabler 的图标集为 `@iconify-json/tabler`。您可以参考 [Icônes](https://icones.js.org/) 或 [Iconify](https://icon-sets.iconify.design/) 查看所有可用集合。

```bash
pnpm add -D @unocss/preset-icons @iconify-json/[你想要的集合]
```

如果你希望一次性安装 Iconify 上所有可用的图标集（约 130MB）：

```bash
pnpm add -D @iconify/json
```

或者，如果你更喜欢从 CDN 获取它们，您可以指定 `cdn` 选项，从 `v0.32.10` 开始。我们推荐 [esm.sh](https://esm.sh/) 作为 CDN 提供商。

```js
presetIcons({
  cdn: 'https://esm.sh/'
})
```

```js
import { presetIcons  } from 'unocss'

export default defineConfig({
  presets: [
    presetIcons ({ /* preset options */ }),
    // ...
  ],
})
```
*其他高级用法请看 [Icon 预设](https://unocss-cn.pages.dev/presets/icons)*


### 属性化模式

```js
import { presetAttributify } from 'unocss'

export default defineConfig({
  presets: [
    presetAttributify({ /* preset options */ }),
    // ...
  ],
})
```

[属性化模式](https://unocss-cn.pages.dev/presets/attributify) 继承自 WindCSS，这是我最喜欢的模式之一，想象一下有这样一个使用 Tailwind CSS 实用程序的按钮。当列表变得更长时，阅读和维护变得非常困难。

#### Attributify 模式

```html
<button class="bg-blue-400 hover:bg-blue-500 text-sm text-white font-mono font-light py-2 px-4 rounded border-2 border-blue-200 dark:bg-blue-500 dark:hover:bg-blue-600">
  Button
</button>
```

使用 attributify 模式，您可以将实用程序分开成属性：

```html
<button
  bg="blue-400 hover:blue-500 dark:blue-500 dark:hover:blue-600"
  text="sm white"
  font="mono light"
  p="y-2 x-4"
  border="2 rounded blue-200"
>
  Button
</button>
```

例如，`text-sm text-white` 可以分组为 `text="sm white"`，而不会重复相同的前缀。

#### 前缀自引用

对于具有与前缀相同的实用程序的实用程序，如 `flex`、`grid`、`border`，提供了一个特殊的 `~` 值。

```html
<!-- befor -->
<button class="border border-red">
  Button
</button>

<!-- after -->
<button border="~ red">
  Button
</button>
```

案例：

```html
<van-divider>系统主题色</van-divider>
<div flex="~" justify="center">
  <div grid="~ cols-8 gap-2">
    <span
      v-for="(item, index) in designStore.appThemeList"
      :key="index"
      h="8"
      w="8"
      items-center
      border="2 rounded-md"
      flex="~"
      justify="center"
      :style="{ 'background-color': item }"
      @click="togTheme(item)"
    >
    <!--  -->
      <i
        v-show="item === designStore.appTheme"
        class="i-ic:sharp-check" text-2xl text-white
      />
    </span>
  </div>
</div>
```

#### 无值属性化

除了 Windi CSS 的 attributify 模式，此预设还支持无值的属性。

```html
<!-- befor -->
<div class="m-2 rounded text-teal-400" />

<!-- after -->
<div m-2 rounded text-teal-400 />
```
{% note color:yellow
 注意 如果属性模式的名称与元素或组件的属性发生冲突，你可以添加 `un-` 前缀以指定为 UnoCSS 的 attributify 模

  ```html
  <a text="red">This conflicts with links' `text` prop</a>
  <!-- to -->
  <a un-text="red">Text color to red</a>
```
%}



### 快捷方式

快捷方式顾名思义就是将多个规则组合成单个简写，灵感来自于 [Windi CSS](https://windicss.org/features/shortcuts.html)。

```js
export default defineConfig({
  // ...UnoCSS options
  presets: [
  ],

  // 一些实用的自定义组合
  shortcuts: {
    'm-0-auto': 'm-0 ma', // margin: 0 auto
    'wh-full': 'w-full h-full', // width: 100%, height: 100%
    'flex-center': 'flex justify-center items-center', // flex布局居中
    'flex-x-center': 'flex justify-center', // flex布局：主轴居中
    'flex-y-center': 'flex items-center', // flex布局：交叉轴居中
    'text-overflow': 'overflow-hidden whitespace-nowrap text-ellipsis', // 文本溢出显示省略号
    'text-break': 'whitespace-normal break-all break-words', // 文本溢出换行
  },
})
```

### 变体组

```js
import { transformerVariantGroup } from 'unocss'

export default defineConfig({
  presets: [
    // 启用 Windi CSS for UnoCSS 的变体组功能(就是简写，具体看链接): https://unocss.dev/transformers/variant-group#usage
    transformerVariantGroup(),
  ],
})
```

通过用括号将它们分组来应用相同变体的实用程序，这样你可以少些一些 hover :)

```html
<div class="hover:(bg-gray-400 font-medium) font-(light mono)"/>

<!-- 被转换为 -->
<div class="hover:bg-gray-400 hover:font-medium font-light font-mono"/>
```

### 指令转换器

UnoCSS 的 [指令转换器](https://unocss-cn.pages.dev/transformers/directives#%E6%8C%87%E4%BB%A4%E8%BD%AC%E6%8D%A2%E5%99%A8)，用于 `@apply`、`@screen` 和 `theme()` 指令：`@unocss/transformer-directives`。

```js
import { transformerDirectives } from 'unocss'

export default defineConfig({
  // ...
  transformers: [
    transformerDirectives(),
  ],
})
```

#### @apply

该指令可以让你在 class 里面直接写原子样式，但请勿滥用，因为这样写你仍然需要给 class 起名字不是吗？🫠

```css
.custom-div {
  @apply text-center my-0 font-medium;
}
```

将被转换为：

```css
.custom-div {
  margin-top: 0rem;
  margin-bottom: 0rem;
  text-align: center;
  font-weight: 500;
}
```

#### [@screen](https://unocss-cn.pages.dev/transformers/directives#screen)

`@screen` 指令允许您创建媒体查询，其中引用您的断点名称来自 [`theme.breakpoints`](https://unocss-cn.pages.dev/config/theme)。

```css
.grid {
  --uno: grid grid-cols-2;
}
@screen xs {
  .grid {
    --uno: grid-cols-1;
  }
}
@screen sm {
  .grid {
    --uno: grid-cols-3;
  }
}
/* ... */
```

将被转换为：

```css
.grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
}
@media (min-width: 320px) {
  .grid {
    grid-template-columns: repeat(1, minmax(0, 1fr));
  }
}
@media (min-width: 640px) {
  .grid {
    grid-template-columns: repeat(3, minmax(0, 1fr));
  }
}
/* ... */
```

`@screen` 还支持 `lt`、`at` 变体，响应式用的比较少这里不再赘述，可自行看 [媒体查询指令 screen](https://unocss-cn.pages.dev/transformers/directives#screen)

## 一些实用技巧

### 任意值

很多时候，预定义的类名不足以完成这项工作。我们可能想要向预定义的顺风类添加自定义值。这可以通过使用`property-[value]`语法来实现，这就是所谓的“任意值”。你可以这样做：

```html
<!-- 这是 Tailwind 任意值写法 -->
<div class="w-[100vw] bg-[#f7f8fa]"></div>

<!-- 这是 windi 任意值写法 -->
<div class="w-50px bg-#f7f8fa"></div>

<!-- 你甚至可以使用 CSS 变量 -->
<div class="color-[var(--color-text-2)]"></div>
```

两者在 UnoCSS 都是支持的，为了保证统一最好统一使用方括号 ( `[]` )

### 伪类 / 伪元素

```html
<button class="bg-purple-500 border border-blue-500 text-white text-2xl uppercase p-6 rounded-md m-4 transition-colors hover:bg-purple-800 hover:border-blue-200 hover:text-gray-200">Click me!</button>

<!-- 这里你可以用 UnoCSS 的变体组和属性模式来简写 -->
<button
  border="1 border-blue-500 rounded-md"
  p="6"
  m="4"
  bg="purple-500"
  uppercase
  class="transition-colors text-(white 2xl) hover:(bg-purple-800 border-blue-200 text-gray-200)"
>Click me!</button>
```

基于父状态的样式

例如，当父元素悬停时，我们可以通过将父元素转变为组并使用`group`和`group-hover:`实用程序类来更改子元素的样式：

```html
<button class="group">
  <span>Click!</span>
  <span class="ml-2 inline-block group-hover:rotate-90">
    &rarr;
  </span>
</button>
```

![Tailwind CSS, group hover example using button.](https://miro.medium.com/v2/resize:fit:900/1*qWF68_HWwl7hA1ar0Gxtgg.gif)

### 动画实用类

Tailwind 有一些非常有用且易于使用的动画实用程序类。例如，我们可以添加过渡颜色类并设置 300 毫秒的持续时间，以在悬停时创建平滑的颜色变化效果。我们还可以传递动画曲线和动画延迟：

```html
<div class="... hover:bg-gray-300 transition-colors duration-300 ease-in-out" />
```

几乎所有可设置动画的属性都可供您使用（完整列表[请参见此处](https://tailwindcss.com/docs/transition-property)）。

### 简单的渐变

你可以使用[渐变色标](https://tailwindcss.com/docs/gradient-color-stops#middle-color)创建复杂的渐变。为此，我们可以使用 `bg-gradient-to-` class 并将其与 `t` （上）、 `r` （右）、 `b` （下）和 `l` （左）组合。我们还可以用`tr` （右上角）、 `bl` （左下角）等来表示角点。

然后我们可以组合： `from` 、 `to` 和 `via`来制作一些令人惊叹的渐变。

让我们看一些例子：

```html
{ /* the first "to" 👇🏽 is specifiying the direction */}
<div class="bg-gradient-to-r from-indigo-500 ...">
{ /* the "from-" sets which color to start at and then fades out */}
```

渲染的输出将是一个以靛蓝开始并逐渐渐变为透明的渐变：

![Untitled](https://cdn.builder.io/api/v1/image/assets%2FYJIGb4i01jvw0SRdL5Bt%2Fc7a923f46af04f66bf9ba4179472bbd1?width=300)

要设置结尾，我们可以使用 `to-` ：

```html
<div class="bg-gradient-to-r from-indigo-500 to-pink-400...">
```

这将渲染一个以靛蓝开始并逐渐淡化为粉红色的渐变：

![Untitled](https://cdn.builder.io/api/v1/image/assets%2FYJIGb4i01jvw0SRdL5Bt%2Fea83c9602f7847eaaca8e8391bd0519d?width=300)

要设置三种颜色，我们可以通过使用 `via` 来控制中间的颜色：

```html
<div class="bg-gradient-to-r from-indigo-500 via-green-400 to-pink-400...">
```

这将呈现几乎彩虹的渐变，如下所示：

![Untitled](https://cdn.builder.io/api/v1/image/assets%2FYJIGb4i01jvw0SRdL5Bt%2F665e22e445df440ba6c0341491a38835?width=300)

### 轻松截断文本

另一个漂亮的实用程序类是`line-clamp` ，它允许您通过简单地添加一个数字（例如`line-clamp-3`来截断多行文本：

```html
<article class="mt-20 border border-slate-300 rounded-md p-4 ml-6 text-white w-60">
  <p class="line-clamp-3">
    Nulla dolor velit adipisicing duis excepteur esse in duis nostrud
    occaecat mollit incididunt deserunt sunt. Ut ut sunt laborum ex
    occaecat eu tempor labore enim adipisicing minim ad. Est in quis eu
    dolore occaecat excepteur fugiat dolore nisi aliqua fugiat enim ut
    cillum. Labore enim duis nostrud eu. Est ut eiusmod consequat irure
    quis deserunt ex. Enim laboris dolor magna pariatur. Dolor et ad sint
    voluptate sunt elit mollit officia ad enim sit consectetur enim.
  </p>
</article><span class="line-clamp-1 mr-2 max-w-500px text-base font-bold">{{ assignment.name }}</span>
```

渲染结果将在 3 行文本后放置一个省略号：

![Untitled](https://cdn.builder.io/api/v1/image/assets%2FYJIGb4i01jvw0SRdL5Bt%2F67e62443191443c1b62a85e49f51302f?width=450)

### **元素之间的空间**

我们可以仅在父级上使用`space-x-*, space-y-*`属性，而不是在每个子级上使用`gap, margin, padding`属性。

```html
<ul class="flex flex-row space-x-5">
  <li class="size-16 bg-red-400">Item 1</li>
  <li class="size-16 bg-red-500">Item 2</li>
  <li class="size-16 bg-red-600">Item 3</li>
</ul>
```

![Tailwind css, space between elements using space-x-5 class.](https://miro.medium.com/v2/resize:fit:1050/1*wTK_kErTmq9llSUAztEHKQ.png)

## 结语

「每样工具都有它适合的地方」这句话大家都知道，但重点是「那它到底适合什么地方？不适合什么地方？它解决了什么问题？又额外创造了哪些问题？」，基于这些问题去讨论一项技术，才能更深入地去了解它。

Atomic CSS 是在维护大型专案的时空背景之下诞生的，如果你没有碰到那种「牵一发而动全身，改一个地方要检查好多地方会不会坏掉」的状况，那你用了Atomic CSS，可能确实感觉不到它的好处，因为它想解决的问题你根本没有碰到。

对于那些「它想解决的问题，你的专案没碰到」的状况下，导不导入的差异本来就不大，有些甚至还会增加不必要的复杂度。

若是在一个「相同的元件却四处分散，当你改 HTML 时需要同时改很多地方」的专案用上了Atomic CSS，那确实是不适合，官方文件也不推荐这样做。如果硬要用，那碰到维护性的问题时并不是 Atomic CSS 的错，而是当时选择技术的人的错（就跟你说不适合了还要用）。

又或是你写了一个UI library，而这个 library 又需要支援一些 UI 的客制化，如果你用 Atomic CSS 来做样式，那你要怎么完成这件事？难道要把每一个HTML 元素都开放传 class name 进去吗？在这个状况下，像是 [antd](https://ant.design/docs/react/customize-theme-cn) 那样使用传统的 CSS 解决方案说不定比较适合，因为可以直接改原本的 Less 档案，就能轻松客制化。

（[daisyUI](https://daisyui.com/) 是靠着把 HTML 一起开放出来，借此达成客制化，我上面指的案例比较像是写一个 React component，把细节包在里面的那种）

每个专案都有不同适合的技术与工具，在做选择时应该先了解每个专案的需求，以及每一项技术的优缺点，才能挑到相对合适的技术。

最后，从 Atomic CSS 的历史中，我觉得最值得学习的其实是「Tools, not rules（“工具” 应该是用来服务于开发者的，而不是限制他们的“规则”）」那一段。以前的最佳实践不一定适用于现在的状况，以前的 class name 不是这样用的，不代表现在就不行。我们不该墨守成规，不该执着在那些规则上面；如果别的做法有显而易见的好处，那为何不呢？

