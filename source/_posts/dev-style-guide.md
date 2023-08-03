---
title: 前端开发风格指南
tags: [代码规范]
categories: [前端工程化, JS]
date: 2023-03-14 20:33:45
description:
cover: https://cdn.xiangshu233.cn/post/banner/wallhaven-4y8q3g.jpg
banner: https://cdn.xiangshu233.cn/post/banner/wallhaven-4y8q3g.jpg
---

## 前言

本约定不是固执己见的，均收集于社区主流规范和 vue 官方风格指南。

更细粒度风格指南请看：[这里是官方的 Vue 特有代码的风格指南](https://cn.vuejs.org/style-guide/)

虽然学习和遵守规范的过程可能有些痛苦，但我坚信它会对你的开发生涯带来巨大的好处。请不要排斥规范，因为它能够让你编写出更优雅的代码，给你带来身心愉悦。合理的规范有助于团队之间更好地沟通协作，提高开发效率，并且能够早期发现潜在的 BUG 和错误。

## 了解前端工程化

现在前端的工作与以前的前端开发已经完全不同了。

刚接触前端的时候，做一个页面，是先创建 HTML 页面文件写页面结构，再在里面写 CSS 代码美化页面，再根据需要写一些 JavaScript 代码增加交互功能，需要几个页面就创建几个页面，相信大家的前端起步都是从这个模式开始的。

而实际上的前端开发工作，早已进入了前端工程化开发的时代，已经充满了各种现代化框架、预处理器、代码编译…

最终的产物也不再单纯是多个 HTML 页面，经常能看到 MPA / SPA / SSG / CSR / SSR 等词汇的身影。

| 名词 |          全称           |              中文               |
| :--: | :---------------------: | :-----------------------------: |
| MPA  | Multi-Page Application  |     多页面应用（传统开发）      |
| SPA  | Single-Page Application |       单页面应用（现在）        |
| SSG  | Static-Site Generation  | 静态页面生成（Hexo / VuePress） |

| 名词 |         全称          |              中文              |
| :--: | :-------------------: | :----------------------------: |
| CSR  | Client-Side Rendering | 客户端渲染（Js / Vue / React） |
| SSR  | Server-Side Rendering |     服务端渲染（Nuxt.js）      |

## 传统开发的弊端

### 某个场景

如果一个 HTML 文件中引入两个 JS 文件，两个文件中都声明了一个重名的变量，则第二个 JS 文件中的变量则会覆盖第一个变量，后续有业务依赖这个变量则会导致代码逻辑出错。

原因是 JavaScript 的加载顺序是从上到下，当使用 `var` 声明变量时，如果命名有重复，那么后加载的变量会覆盖掉先加载的变量。

这是使用 `var` 声明的情况，它允许使用相同的名称来重复声明，那么换成 `let` 或者 `const` 呢？

虽然不会出现重复声明的情况，但同样会收到一段报错：

```bash
Uncaught SyntaxError: Identifier 'xxx' has already been declared (at lib-2.js:1:1)
```

这次程序直接崩溃了，因为 `let` 和 `const` 无法重复声明，从而抛出这个错误，程序依然无法正确运行。

### 更多问题

以上只是一个最简单的案例，就暴露出了传统开发很大的弊端，然而并不止于此，实际上，存在了诸如以下这些的问题：

1. 如本案例，可能存在同名的变量声明，引起变量冲突
2. 引入多个资源文件时，比如有多个 JS 文件，在其中一个 JS 文件里面使用了在别处声明的变量，无法快速找到是在哪里声明的，大型项目难以维护
3. 类似第 1 、 2 点提到的问题无法轻松预先感知，很依赖开发人员人工定位原因
4. 大部分代码缺乏分割，比如一个工具函数库，很多时候需要整包引入到 HTML 里，文件很大，然而实际上只需要用到其中一两个方法
5. 由第 4 点大文件延伸出的问题， `script` 的加载从上到下，容易阻塞页面渲染
6. 不同页面的资源引用都需要手动管理，容易造成依赖混乱，难以维护
7. 如果要压缩 CSS 、混淆 JS 代码，也是要人力操作使用工具去一个个处理后替换，容易出错

当然，实际上还会有更多的问题会遇到。

## 工程化带来的优势

为了解决传统开发的弊端，前端也开始引入工程化开发的概念，借助工具来解决人工层面的烦琐事情。

### 开发层面的优势

在传统开发的弊端里，主要列举的是开发层面的问题，工程化首要解决的当然也是在开发层面遇到的问题。

在开发层面，前端工程化有以下这些好处：

1. 引入了模块化和包的概念，作用域隔离，解决了代码冲突的问题
2. 按需导出和导入机制，让编码过程更容易定位问题
3. 自动化的代码检测流程，有问题的代码在开发过程中就可以被发现
4. 编译打包机制可以让使用开发效率更高的编码方式，比如 Vue 组件、 CSS 的各种预处理器
5. 引入了代码兼容处理的方案（ e.g. Babel ），可以让自由使用更先进的 JavaScript 语句，而无需顾忌浏览器兼容性，因为最终会帮转换为浏览器兼容的实现版本
6. 引入了 Tree Shaking（摇树） 机制，清理没有用到的代码，减少项目构建后的体积

还有非常多的体验提升，列举不完。而对应的工具，根据用途也会有非常多的选择，在后面的学习过程中，会一步一步体验到工程化带来的好处。

### 团队协作的优势

除了对开发者有更好的开发体验和效率提升，对于团队协作，前端工程化也带来了更多的便利，例如下面这些场景：

#### 统一的项目结构

以前的项目结构比较看写代码的人的喜好，虽然一般在研发部门里都有 “团队规范” 这种东西，但靠自觉性去配合的事情，还是比较难做到统一，特别是项目很赶的时候。

工程化后的项目结构非常清晰和统一，以 Vue 项目来说，通过脚手架创建一个新项目之后，它除了提供能直接运行 Hello World 的基础代码之外，还具备了如下的统一目录结构：

- `src` 是源码目录
- `src/main.ts` 是入口文件
- `src/views` 是路由组件目录
- `src/components` 是子组件目录
- `src/router` 是路由目录

一个完整的 Vue3 + TypeScript + Vite 项目的目录结构大致如下：

```
.
├── build # 打包脚本相关
│   ├── script # 脚本
│   └── vite # vite配置
├── mock # mock文件夹
├── public # 公共静态资源目录
├── src # 主目录
│   ├── api # 接口文件
│   ├── assets # 资源文件
│   │   ├── icons # icon sprite 图标文件夹
│   │   ├── images # 项目存放图片的文件夹
│   │   └── svg # 项目存放svg图片的文件夹
│   ├── components # 公共组件
│   ├── styleS # 样式文件
│   ├── directives # 指令
│   ├── enums # 枚举/常量
│   ├── hooks # hook
│   │   ├── component # 组件相关hook
│   │   ├── core # 基础hook
│   │   ├── event # 事件相关hook
│   │   ├── setting # 配置相关hook
│   │   └── web # web相关hook
│   ├── layout # 布局文件
│   ├── main.ts # 主入口
│   ├── router # 路由配置
│   ├── settings # 项目配置
│   │   ├── componentSetting.ts # 组件配置
│   │   ├── designSetting.ts # 样式配置
│   │   ├── projectSetting.ts # 项目配置
│   │   └── siteSetting.ts # 站点配置
│   ├── store # 全局状态管理
│   ├── utils # 工具类
│   └── views # 页面
├── types # 类型文件
├── vite.config.ts # vite配置文件
└── windi.config.ts # windcss配置文件
```

#### 统一的代码代码风格

不管是接手其他人的代码或者是修改自己不同时期的代码，可能都会遇到这样的情况，例如一个模板语句，上面包含了很多属性，有的人喜欢写成一行，属性多了维护起来很麻烦，需要花费较多时间辨认：

```html
<template>
  <div class="list">
    <!-- 这个循环模板有很多属性 -->
    <div class="item" :class="{ `top-${index + 1}`: index < 3 }" v-for="(item, index)
    in list" :key="item.id" @click="handleClick(item.id)">
      <span>{{ item.text }}</span>
    </div>
    <!-- 这个循环模板有很多属性 -->
  </div>
</template>
```

而工程化配合统一的代码格式化规范，可以让不同人维护的代码，最终提交到 Git 上的时候，风格都保持一致，并且类似这种很多属性的地方，都会自动帮格式化为一个属性一行，维护起来就很方便：

```html
<template>
  <div class="list">
    <!-- 这个循环模板有很多属性 -->
    <div
      class="item"
      :class="{ `top-${index + 1}`: index < 3 }"
      v-for="(item, index) in list"
      :key="item.id"
      @click="handleClick(item.id)"
    >
      <span>{{ item.text }}</span>
    </div>
    <!-- 这个循环模板有很多属性 -->
  </div>
</template>
```

同样的，写 JavaScript 时也会有诸如字符串用双引号还是单引号，缩进是 Tab 还是空格，如果用空格到底是要 4 个空格还是 2 个空格等一堆 “没有什么实际意义” 、但是不统一的话协作起来又很难受的问题……

在工程化项目这些问题都可以交给程序去处理，或者是在提交代码的时候配合 Git Hooks 自动格式化，都可以做到统一风格。

#### 代码健壮性有保障

传统的开发模式里，只能够写 JavaScript ，而在工程项目里，可以在开发环境编写带有类型系统的 TypeScript ，然后再编译为浏览器能认识的 JavaScript 。

在开发过程中，编译器会检查代码是否有问题，比如在 TypeScript 里声明了一个布尔值的变量，然后不小心将它赋值为数值：

```typescript
// 声明一个布尔值变量
let bool: boolean = true

// 在 TypeScript ，不允许随意改变类型，这里会报错
bool = 3
```

编译器检测到这个行为的时候就会抛出错误：

```
# ...
return new TSError(diagnosticText, diagnosticCodes);
           ^
TSError: ⨯ Unable to compile TypeScript:
src/index.ts:2:1 - error TS2322: Type 'number' is not assignable to type 'boolean'.

2 bool = 3
  ~~~~
# ...
```

它的报错是在编译阶段发生的，而不是在线上阶段，从而得以及时发现问题并修复，减少线上事故的发生。

## 约定

个人认为目前代码规范分为**硬性**和**软性**。

所谓硬性规范，是可以**靠工具强制保证效果的**，比如缩进几个字符、每行多少字母、句尾加分号等，可以用 Lint 工具保证落地效果的规范；

所谓软性规范，是指诸如方法命名、文件命名、套路代码等无法用工具强行约束的规则。这种规则只能算是一种**约定**，更多的是要靠**自觉**，除了 Code Review（代码评审） 之外，暂时也想不到其他有效的方法来保证其落地效果。

现在我们需要从**开发环境**到**代码书写规范**制定一套有利于团队开发效率的约定

## VPN

VPN 应该是每一个开发人员必备，毕竟很多技术都是从外面过来的，上 Github、Google、甚至一些文档都需要。

tips：

搜索不要用百度！不要用百度！不要用百度！你一定会搜到一堆你不想要的答案。Google 配合英文关键词搜索可以找到大部分问题答案，实在不行还有 gtp、copilot

## 命令行工具

### Windows

在前端工程化开发过程中，已经离不开各种命令行操作，例如：管理项目依赖、本地服务启动、打包构建，还有拉取代码 / 提交代码这些 Git 操作等等。

在 Windows 平台，可以使用自带的 CMD 或者 Windows PowerShell 工具。

但为了更好的开发体验，推荐使用以下工具（需要下载安装），可以根据自己的喜好选择其一：

注：从Windows 11 22H2 版本开始，Windows Terminal 将正式成为 Windows 11 的默认设置

|       名称       | 简介                                  |                           下载                            |
| :--------------: | :------------------------------------ | :-------------------------------------------------------: |
| Windows Terminal | 由微软推出的强大且高效的 Windows 终端 | [前往 GitHub 下载](https://github.com/microsoft/terminal) |
|      CMDer       | 一款体验非常好的 Windows 控制台模拟器 |   [前往 GitHub 下载](https://github.com/cmderdev/cmder)   |

### macOS

如果使用的是 Mac 系统，可以直接使用系统自带的 “终端” 工具进行开发。

TIP

其实只要能正常使用命令行，对于前端工程师来说就可以满足日常需求，但选择更喜欢的工具，可以让自己的开发过程更为身心愉悦！

## 安装  Node 环境

这里使用 NVM 来管理 Node 版本

### 什么是 NVM

顾名思义，NVM 是一种用于管理设备上的 Node 版本的工具。

你设备上的不同项目可能使用不同版本的 Node。对这些不同的项目仅使用一个版本（由 `npm` 安装的版本）可能无法为你提供准确的执行结果。

例如，如果你将 **10.0.0** 的 Node 版本用于使用 **12.0.0** 的项目，则可能会出现一些错误。如果你用 npm 将 Node 版本更新到 **12.0.0**，并且你将它用于使用 **10.0.0** 的项目，你可能无法获得预期的体验。

事实上，你很可能会收到一条警告：

```bash
This project requires Node version X
```

无需使用 npm 为你的不同项目安装和卸载 Node 版本，你可以使用 NVM，它可以帮助你有效地管理每个项目的 Node 版本。

[NVM](https://github.com/nvm-sh/nvm) 允许你安装不同版本的 Node，并根据你正在通过命令行处理的项目在这些版本之间切换。

### 安装 NVM

NVM 主要在 Linux 和 Mac 上得到支持。它不支持 Windows。但是 coreybutler 创建了一个类似的工具，用于在 Windows 中提供 NVM 体验，叫作 [nvm-windows](https://github.com/coreybutler/nvm-windows)。

在 [nvm-windows 仓库的 Readme 文件](https://github.com/coreybutler/nvm-windows#readme)中，单击 “Download Now!”：

这将打开一个显示不同 NVM 版本的页面。

安装最新版本的 `nvm-setup.exe` 文件

打开你下载的文件，然后完成安装向导（会自动配置环境变量）

完成后，你可以通过运行以下命令确认 NVM 已安装：

```bash
nvm -v
```

这应该会显示你所安装的 NVM 版本

### 使用 NVM

安装了 NVM 后，你现在可以在你的 Windows、Linux 或 Mac 设备中安装、卸载和切换不同的 Node 版本。

你可以像这样安装 最新 Node 版本

```bash
nvm install latest
```

你也可以安装指定版本

```bash
nvm install vX.Y.Z
```

你还可以通过运行以下命令将版本设置为默认版本：

```bash
nvm alias default vX.Y.Z
```

如果你想在任何时候使用特定版本，你可以在终端中运行以下命令：

```bash
nvm use vA.B.C
```

NVM 使管理需要不同 Node.js 版本的不同项目变得更加容易。

## 包管理器

包管理器（ Package Manager ）是用来管理依赖包的工具，比如：发布、安装、更新、卸载等等。

Node 默认提供了一个包管理器 `npm` ，在安装 Node.js 的时候，默认会一起安装 npm 包管理器，可以通过以下命令查看它是否正常。

```bash
npm -v
```

如果正常，将会输出相应的版本号。

其他的包管理器 还有 yarn、pnpm，使用方式都大同小异，本**约定**使用 pnpm 来作为团队协作包管理器。理由可以看[官方文档](https://www.pnpm.cn/)，它们之间的渊源也可以自行搜索，这里不再赘述。

### 安装 pnpm

通过 npm 安装 pnpm

```bash
npm install -g pnpm
```

### 使用 pnpm

| npm 命令        | pnpm 等价命令                                     |
| --------------- | ------------------------------------------------- |
| `npm install`   | [`pnpm install`](https://www.pnpm.cn/cli/install) |
| `npm i <pkg>`   | [`pnpm add`](https://www.pnpm.cn/cli/add)        |
| `npm run <cmd>` | [`pnpm`](https://www.pnpm.cn/cli/run)            |

其他命令可以查阅[官方文档](https://www.pnpm.cn/)

## 安装  VSCode

本约定统一使用 VSCode 作为前端代码编辑器，点击下载：[Visual Studio Code](https://code.visualstudio.com/Download)

### 添加  VSCode  插件

好的插件能让我们的开发事半功倍

#### Chinese (Simplified)

VSCode 安装后默认是英文本，需要自己进行汉化配置， VSCode 的特色就是插件化处理各种功能，语言方面也一样。

点击下载：[Chinese (Simplified)](https://marketplace.visualstudio.com/items?itemName=MS-CEINTL.vscode-language-pack-zh-hans)

#### Volar

Vue 官方推荐的 VSCode 扩展，用以代替 Vue 2 时代的 [Vetur](https://marketplace.visualstudio.com/items?itemName=octref.vetur) ，提供了 Vue 3 的语言支持、 TypeScript 支持、基于 [vue-tsc](https://github.com/johnsoncodehk/volar/tree/master/packages/vue-tsc) 的类型检查等功能。

点击下载：[Volar](https://marketplace.visualstudio.com/items?itemName=johnsoncodehk.volar)

TIP

Volar 取代了 Vetur 作为 Vue 3 的官方扩展，如果之前已经安装了 Vetur ，请确保在 Vue 3 的项目中禁用它。

#### Auto Close Tag

可以快速完成 HTML 标签的闭合，除非通过 `.jsx` / `.tsx` 文件编写 Vue 组件，否则在 `.vue` 文件里写 `template` 的时候肯定用得上。

点击下载：[Auto Close Tag](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-close-tag)

#### Auto Rename Tag

假如要把 `div` 修改为 `section`，不需要再把 `<div>` 然后找到代码尾部的 `</div>` 才能修改，只需要选中前面或后面的半个标签直接修改，插件会自动把闭合部分也同步修改，对于篇幅比较长的代码调整非常有帮助。

点击下载：[Auto Rename Tag](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-rename-tag)

#### console helper

快速打印 `console.log()`，选中变量后按下快捷键，会在代码下方插入一行带颜色的 `console` 语句

快捷方式：

- macOS: `cmd` + `shift` + `l`
- Windows: `ctrl` + `l`

点击下载：[console helper](https://marketplace.visualstudio.com/items?itemName=AT-9420.console-helper)

#### DotENV

高亮显示 `.ENV` 配置文件内容

点击下载：[DotENV](https://marketplace.visualstudio.com/items?itemName=mikestead.dotenv)

#### Error Lens

更好的报错提示，将报错信息显示在当前报错位置

点击下载：[Error Lens](https://marketplace.visualstudio.com/items?itemName=usernamehw.errorlens)

#### indent-rainbow

增强代码缩进提示

点击下载：[indent-rainbow](https://marketplace.visualstudio.com/items?itemName=oderwat.indent-rainbow)

#### JavaScript (ES6) code snippets

ES6 语法的一些快捷代码片段，提高开发效率

点击下载：[JavaScript (ES6) code snippets](https://marketplace.visualstudio.com/items?itemName=xabikos.JavaScriptSnippets)

#### Path Intellisense

路径提示插件，引入组件的时候会有提示功能

点击下载：[JavaScript (ES6) code snippets](https://marketplace.visualstudio.com/items?itemName=christian-kohler.path-intellisense)

#### Windi CSS IntelliSense

[Windi CSS](https://cn.windicss.org/guide/) IntelliSense 通过为 VSCode 用户提供自动完成、语法突出显示、代码折叠和构建等高级功能来增强 Windi 开发体验。

点击下载：[Windi CSS IntelliSense](https://marketplace.visualstudio.com/items?itemName=voorjaar.windicss-intellisense)

#### Trailing Spaces

删除页面多余的空格

#### Vitesse Theme

一款 [antfu](https://github.com/antfu) 开发的 VSCode 主题，主题自己定义，但最好不用亮色主题，因为太亮会吸引虫子（bug）。

#### Carbon Product Icons

项目目录图标主题

#### ESLint  for  VSCode

这是 [ESLint](https://vue3.chengpeiquan.com/upgrade.html#eslint) 在 VSCode 的一个扩展， TypeScript 项目基本都开了 ESLint ，编辑器也建议安装该扩展支持以便获得更好的代码提示。

点击下载：[VSCode ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

点击访问：[ESLint 官网](https://eslint.org/) 了解更多配置。

#### StyleLint  for  VSCode

Stylelint 的官方 VSCode  扩展，

点击下载：[Stylelint](https://marketplace.visualstudio.com/items?itemName=stylelint.vscode-stylelint)

#### EditorConfig for VS Code

一个可以让编辑器遵守协作规范的插件，详见 [项目代码风格统一神器](https://chengpeiquan.com/article/editorconfig.html) 。

在项目根目录下再增加一个名为 `.editorconfig` 的文件。

这个文件的作用是强制编辑器以该配置来进行编码，比如缩进统一为空格而不是 Tab ，每次缩进都是 2 个空格而不是 4 个等等。

```
# 官网是这么介绍 EditorConfig 的：
# EditorConfig帮助开发人员在不同的编辑器和IDE之间定义和维护一致的编码样式。
# EditorConfig 项目由用于定义编码样式的文件格式和一组文本编辑器插件组成，这些插件使编辑器能够读取文件格式并遵循定义的样式。
# EditorConfig 文件易于阅读，并且与版本控制系统配合使用。
# 不同的开发人员，不同的编辑器，有不同的编码风格，而 EditorConfig 就是用来协同团队开发人员之间的代码的风格及样式规范化的一个工具，
# 而 .editorconfig 正是它的默认配置文件

#EditorConfig 的匹配规则是从上往下，即先定义的规则优先级比后定义的优先级要高。

# 告诉 EditorConfig 插件，这是根文件，不用继续往上查找
root = true

# 匹配全部文件
[*]

# 设置字符集
charset=utf-8

# 结尾换行符，可选"lf"、"cr"、"crlf"
end_of_line=LF

# 在文件结尾插入新行
insert_final_newline=true

# 缩进风格，可选"space"、"tab"
indent_style=space

# 缩进的空格数
indent_size=2

max_line_length = 100

# 匹配 yml 和 yaml、json 结尾的文件
[*.{yml,yaml,json}]
indent_style = space
indent_size = 2

[*.md]
# 删除一行中的前后空格
trim_trailing_whitespace = false

[Makefile]
indent_style = tab

```

点击下载：[EditorConfig for VSCode](https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig)

#### File Nesting Updater

使用 VS Code 的[文件嵌套功能](https://code.visualstudio.com/updates/v1_64#_explorer-file-nesting)，使文件树更清晰的配置。

配置：

默认情况下，它将每 12 小时检查一次更新。您也可以通过执行 command 手动完成`File Nesting Updater: Update config now`。

```json
// setting.json
{
  "fileNestingUpdater.autoUpdate": true,
  "fileNestingUpdater.autoUpdateInterval": 720,
  "fileNestingUpdater.promptOnAutoUpdate": true,
  "fileNestingUpdater.upstreamRepo": "antfu/vscode-file-nesting-config",
  "fileNestingUpdater.upstreamBranch": "main"
}
```

点击下载：[File Nesting Updater](https://marketplace.visualstudio.com/items?itemName=antfu.file-nesting)

![img](https://user-images.githubusercontent.com/11247099/157142238-b00deecb-8d56-424f-9b20-ef6a6f5ddf99.png)

#### Vue Devtools

Vue Devtools 是一个浏览器扩展，支持 Chrome 、 Firefox 等浏览器，需要先安装才能使用。

点击安装：[Vue Devtools 的浏览器扩展](https://devtools.vuejs.org/guide/installation.html)

当在 Vue 项目通过 `npm run dev` 等命令启动开发环境服务后，访问本地页面（如： `http://localhost:3000/` ），在页面上按 F12 唤起浏览器的控制台，会发现多了一个名为 `vue` 的面板。

面板的顶部有一个菜单可以切换不同的选项卡，菜单数量会根据不同项目有所不同，例如没有安装 Pinia 则不会出现 Pinia 选项卡，这里以其中一部分选项卡作为举例。

Components 是以结构化的方式显示组件的调试信息，可以查看组件的父子关系，并检查组件的各种内部状态：

![Vue Devtools 的 Components 界面](https://vue3.chengpeiquan.com/assets/img/vue-devtools-components-dark.jpg)

## 初始化 Vue3 项目

输入以下命令创建一个基于 vite 的 vue3 脚手架

```bash
pnpm create vite
```

### 项目配置 ESLint

使用 [antfu](https://github.com/antfu) 大佬的预设

antfu：Vue、Vite、Nuxt 核心团队成员。VueUse、Slidev、Vitest、UnoCSS 作者

本约定使用 ESLint 和 StyleLint 来作为代码格式检查工具。为什么没有 Prettier 可以看看 antfu 的一篇文章：

 [为什么我不使用 Prettier](https://antfu.me/posts/why-not-prettier-zh)

> 我的观点如下：
>
> 1. 只单纯使用 Prettier 十分合理 - 开箱即用是个很棒的功能
> 2. 如果你需要使用 ESLint，它也可以像 Prettier 一样格式化代码 - 而且更加可配置
> 3. Prettier + ESLint 仍然需要大量的配置 - 它并没有让你的生活变得更简单
> 4. 你可以在 ESLint 中完全控制代码风格，但在 Prettier 中却无法做到，这两者混合在一起感觉很奇怪
> 5. 我不认为 Parse 两次代码会更快

基于 [@antfu/eslint-config](https://github.com/antfu/eslint-config) 的预设配置，减少大量复杂配置

从结果来看，使用 ESLint 其实也可以非常简单：

```bash
pnpm add -D eslint @antfu/eslint-config
```

```
// .eslintrc
{
  "extends": "@antfu"
}
```

你通常不需要`.eslintignore`，因为它已由预设提供。

这样就可以了。配合 IDE 扩展，还可以在保存时触发自动修复。

```json
// settings.json
{
  "prettier.enable": false,
  "editor.formatOnSave": false,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }
}
```

### 项目配置 StyleLint

更多规则可以看 [styleLint 文档](https://stylelint.bootcss.com/)

安装相关工具包

注：这里限制下 stylelint 版本要在 14版本内，高本版删除了大量规则导致检查无效

```bash
pnpm add -D stylelint@14.16.1 stylelint-config-standard@29.0.0 stylelint-config-html@1.1.0 stylelint-order@5.0.0 stylelint-config-recess-order@3.1.0 stylelint-less@1.0.6 postcss@8.4.21 postcss-html@1.5.0 postcss-less@6.0.0
```

- stylelint：主功能包
- stylelint-config-standard：stylelint 基础规则库，是 stylelint 的推荐配置
- stylelint-order：css 属性顺序检查插件（先写定位，再写盒模型，再写内容区样式，最后写 CSS3 相关属性）
- stylelint-config-recess-order：针对 css 属性排序的共享规则配置，避免长串 css 属性顺序规则书写。与 stylelint-order 配合使用
- stylelint-less：让 stylelint 支持 less 语法
- postcss：postcss 默认只支持解析 css 文件，用于 postcss-html 和 postcss-less 的支持
- postcss-html：解析 `<style>` 类 vue、html 文件标签中的样式
- postcss-less：解析 `<style lang="less">` 下的 less 样式



```json
// package.json
"scripts": {
   // 检测
  "stylelint": "stylelint src/**/*.{html,vue,css,sass,scss,less} --cache --cache-location node_modules/.cache/stylelint/",
   // 修复
  "stylelint:fix": "stylelint src/**/*.{html,vue,css,sass,scss,less} --cache --cache-location node_modules/.cache/stylelint/ --fix"
},

"devDependencies": {
  "postcss": "8.4.21",
  "postcss-html": "1.5.0",
  "postcss-less": "6.0.0",
  "stylelint": "14.16.1",
  "stylelint-config-html": "1.1.0",
  "stylelint-config-recess-order": "3.1.0",
  "stylelint-config-standard": "29.0.0",
  "stylelint-less": "1.0.6",
  "stylelint-order": "5.0.0",
}
```

### 项目配置 only-allow

统一规范团队包管理器

首先我们来提一个团队开发中很常见的需求：一般来说每个团队都会统一规定项目内只使用同一个包管理器，譬如 npm、yarn、pnpm 等，如果成员使用了不同的包管理器，则可能会因为 *lock file* 失效而导致项目无法正常运行，虽然这种情况一般都可以通过项目的上手文档来形容共识，但有没有更好的解决方案，比如在项目安装依赖时检测如果使用了不同的包管理器就抛出错误信息？

当然是可以的，pnpm 就有一个包叫做 [only-allow](https://www.npmjs.com/package/only-allow) ，连 [vite](https://github.com/vitejs/vite/blob/c7fc1d4a532eae7b519bd70c6eba701e23b0635a/package.json#L16) 都在使用它

```bash
pnpm install only-allow -D
```

在 `package.json` 中添加如下命令

```json
// package.json
"scripts": {
  "preinstall": "npx only-allow pnpm",
},
```

测试：

![20230215143542.png](https://s1.locimg.com/2023/02/15/17c39f3032b4d.png)

此时触发 `preinstall` 钩子他会打断安装，强制让你使用 pnpm 包管理器

#### 使用钩子函数的坑

经过验证，貌似有个 npm 本身的 bug，就是 preinstall 的执行是在 install 之后，这样提示我换用其他 pm 之前，其实已经安装完成了

npm 官方已经接收到这个 issue 了。目前还没有好的解决办法，或者使用 npm 版本 < 7 的（这个问题不大）

### 项目配置 Husky

这是一个 git hook 的工具，可以通过在 git 操作期间的一些钩子，做一些额外的操作，比如执行 lint test 等。[点击查看所有 git hooks](https://link.juejin.cn/?target=https%3A%2F%2Fgit-scm.com%2Fdocs%2Fgithooks) [点击查看 `husky` 文档](https://link.juejin.cn/?target=https%3A%2F%2Ftypicode.github.io%2Fhusky%2F%23%2F%3Fid%3Darticles)

一般情况可能用到以下钩子：

- `pre-commit`：代码提交之前触发，可以通过此钩子判断代码是否符合规范。
- `commit-msg`：对 commit 的信息校验，可以通过此钩子判定 commit 是否合法。
- `pre-push`： 代码提交之前触发，可以通过此钩子对业务代码执行一些测试。

经过上述操作后，从代码编辑阶段解决了代码格式化的问题。但是总有一些小伙伴写出不符合规则的代码，并且提交到 git 仓库。

这个时候可以通过 git hook 来拦截提交的代码并执行之前配置的格式化代码相关命令，让提交的代码都是符合规范的。

详细教程请看这篇文章：

[前端工程化配置(上) 构建代码检查工作流：husky + lint-staged 配置](https://www.xiangshu233.cn/%E5%89%8D%E7%AB%AF%E5%B7%A5%E7%A8%8B%E5%8C%96%E9%85%8D%E7%BD%AE%EF%BC%88%E4%B8%8A%EF%BC%89%20%E6%9E%84%E5%BB%BA%E4%BB%A3%E7%A0%81%E6%A3%80%E6%9F%A5%E5%B7%A5%E4%BD%9C%E6%B5%81/)

Husky 的作用就是在执行 `git commit`的时候，按照配置自动修复暂存区的文件代码格式。

注：类型问题不会自动修复，需要手动

### 项目配置 commitlint（代码提交规范）

`commitlint` 用来规范团队成员的提交格式信息。团队中规范了 commit 可以更清晰的查看每一次代码提交记录

全局安装这个包：

```bash
npm install -g commitizen
```

详细教程请看这篇文章：

[前端工程化配置(下) 规范仓库提交记录 commitlint + commitizen + cz-git + 配置](https://www.xiangshu233.cn/%E5%89%8D%E7%AB%AF%E5%B7%A5%E7%A8%8B%E5%8C%96%E9%85%8D%E7%BD%AE%EF%BC%88%E4%B8%8B%EF%BC%89%20%E8%A7%84%E8%8C%83%E4%BB%93%E5%BA%93%E6%8F%90%E4%BA%A4%E8%AE%B0%E5%BD%95/)

#### Git 贡献提交规范

- `feat` 增加新功能
- `fix` 修复问题/BUG
- `style` 代码风格相关无影响运行结果的
- `perf` 优化/性能提升
- `refactor` 重构
- `revert` 撤销修改
- `test` 测试相关
- `docs` 文档/注释
- `chore` 依赖更新/脚手架配置修改等
- `workflow` 工作流改进
- `ci` 持续集成
- `types` 类型定义文件更改
- `wip` 开发中

## 代码书写风格

这些规则有助于防止错误，因此请不惜一切代价学习并遵守这些规则。例外情况可能存在，但应该非常罕见，并且只有那些同时具有 JavaScript 和 Vue 专业知识的人才能做到。

### 组件模板

- 所有组件使用 `SFC` 或 `TSX` 形式编写；
- `SFC` 中的标签顺序统一为 `<script>`、`<template>`、`<style>`，这样更符合“**先声明，后使用**”的逻辑，典型案例如下：

```html
<!-- Bad. What is 'routes'?  -->
<template>
  <div id="nav">
    <router-link
      v-for="route in routes"
      :to="route.path"
      :key="route.path"
    >
      {{ route.name }}
    </router-link>
  </div>
  <router-view />
</template>


<script setup lang="ts">
// It's better to import at top
import { routes } from "./router";
</script>
```

### 组件命名

凡是跟组件名相关的（组件名、文件名及在模板中使用），都必须采用大驼峰 `PascalCase` 形式

拿一个 Login 组件举例

![QQ截图20230216170652.png](https://s1.locimg.com/2023/02/16/c6651f0aca5b7.png)

![QQ截图20230216170737.png](https://s1.locimg.com/2023/02/16/6c88d13f2fcf8.png)

### 全局组件

**应用特定于应用程序的样式和约定的基本组件（又名表示组件、哑组件或纯组件）都应以特定前缀开头，例如`Base`、`App`或`V`。**

```
<!-- Bad -->

src/components/
|- MyButton.vue
|- VueTable.vue
|- Icon.vue
```

```
<!-- Good -->

src/components/
|- BaseButton.vue
|- BaseTable.vue
|- BaseIcon.vue

src/components/
|- AppButton.vue
|- AppTable.vue
|- AppIcon.vue

src/components/
|- VButton.vue
|- VTable.vue
|- VIcon.vue
```

在编写全局组件的时候目录结构应有一个统一对外暴露的接口

```
src/components/
|- CountTo
    |- CountTo.vue
    |- index.ts
```

这样外部调用只需

```typescript
import { CountTo } from '@/components/CountTo'
```

```html
<CountTo :start-val="1" :end-val="100" class="text-3xl"/>
```

如果是大型全局组件应按照以下目录划分

```
src/components/
|- Form
	|- src
        |- hooks // 通用逻辑抽离成 hook
        	|- useForm.ts
        	|- useFormEvents.ts
        |- types	// ts 类型定义
        	|- from.ts
        	|- index.ts
        |- BasicForm.vue // 组件
        |- helper.ts // 组件公共函数
        |- props.ts // 组件 props
    |- index.ts // 入口
```

### 使用多词组件名称

用户组件名称应始终为多词，根组件除外。这[可以防止与现有和未来的 HTML 元素发生冲突](https://html.spec.whatwg.org/multipage/custom-elements.html#valid-custom-element-name)，因为所有 HTML 元素都是一个单词。

```html
<!-- Bad -->
<Item />
```

```html
<!-- Good -->
<TodoItem />
```

### 紧密耦合组件

与父组件紧密耦合的子组件，命名要**以父组件名为前缀**，例：`TodoList`、`TodoListItem`、`TodoListItemButton`，详见 [紧密耦合的组件名称](https://cn.vuejs.org/style-guide/rules-strongly-recommended.html#tightly-coupled-component-names)；

如果组件仅在单个父组件的上下文中有意义，则该关系应该在其名称中显而易见。由于编辑器通常按字母顺序组织文件，这也使这些相关文件彼此相邻。

```
<!-- Bad -->

components/
|- TodoList.vue
|- TodoItem.vue
|- TodoButton.vue

components/
|- SearchSidebar.vue
|- NavigationForSearchSidebar.vue
```

```
<!-- Good -->

components/
|- TodoList.vue
|- TodoListItem.vue
|- TodoListItemButton.vue

components/
|- SearchSidebar.vue
|- SearchSidebarNavigation.vue
```

### 组件名称中的单词顺序

一般化描述放前，特殊化描述放后，例：`SearchButtonClear`、`SettingsCheckboxLaunchOnStartup`，详见 [组件名称中的单词顺序](https://cn.vuejs.org/style-guide/rules-strongly-recommended.html#order-of-words-in-component-names)

**组件名称应以最高级别（通常是最通用的）词开头，并以描述性修饰词结尾。**

```
<!-- Bad -->

components/
|- ClearSearchButton.vue
|- ExcludeFromSearchInput.vue
|- LaunchOnStartupCheckbox.vue
|- RunSearchButton.vue
|- SearchInput.vue
|- TermsCheckbox.vue
```

```
<!-- Good -->

components/
|- SearchButtonClear.vue
|- SearchButtonRun.vue
|- SearchInputQuery.vue
|- SearchInputExcludeGlob.vue
|- SettingsCheckboxTerms.vue
|- SettingsCheckboxLaunchOnStartup.vue
```

### 自闭组件

**[没有内容的组件在单文件组件](https://cn.vuejs.org/guide/scaling-up/sfc.html)、字符串模板和 [JSX](https://cn.vuejs.org/guide/extras/render-function.html#jsx-tsx) 中应该是自关闭的——但绝不是在 DOM 模板中。**

自关闭的组件表明它们不仅没有内容，而且本来**就**没有内容。这是一本书中的空白页和标有“此页有意留白”的页之间的区别。没有不必要的结束标记，您的代码也更清晰。

注意：

该特性只能在 Vue 的 `template` 和 `JSX/TSX` 中实现，因为 HTML 不允许自定义元素是自关闭的——只有[官方的“void”元素](https://www.w3.org/TR/html/syntax.html#void-elements)才可以

```html
<!-- Bad -->

<!-- In Single-File Components, string templates, and JSX -->
<MyComponent></MyComponent>
```

```html
<!-- Good -->

<!-- In Single-File Components, string templates, and JSX -->
<MyComponent/>
```

### 全字组件名称

**组件名称应该更喜欢完整的单词而不是缩写。**

编辑器中的自动完成功能使编写较长名称的成本非常低，而它们提供的清晰度是无价的。特别是，应始终避免使用不常见的缩写。

```
 <!-- Bad -->

components/
|- SdSettings.vue
|- UProfOpts.vue
```

```
<!-- Good -->

components/
|- StudentDashboardSettings.vue
|- UserProfileOptions.vue
```



### 多属性元素

**具有多个属性的元素应该跨越多行，每行一个属性。**

在 JavaScript 中，将具有多个属性的对象分成多行被广泛认为是一个很好的约定，因为它更容易阅读。我们的模板和[JSX](https://cn.vuejs.org/guide/extras/render-function.html#jsx-tsx)值得同样的考虑。

```html
 <!-- Bad -->

<img src="https://vuejs.org/images/logo.png" alt="Vue Logo">
<MyComponent foo="a" bar="b" baz="c"/>
```

```html
<!-- Good -->

<img
  src="https://vuejs.org/images/logo.png"
  alt="Vue Logo"
>

<MyComponent
  foo="a"
  bar="b"
  baz="c"
/>
```

### 模板简单表达式

**组件模板应该只包含简单的表达式，将更复杂的表达式重构为计算属性或方法。**

模板中的复杂表达式会降低它们的声明性。我们应该努力描述应该出现*什么*，而不是我们*如何计算该值。*计算属性和方法还允许重用代码。

```js
 // Bad

{{
  fullName.split(' ').map((word) => {
    return word[0].toUpperCase() + word.slice(1)
  }).join(' ')
}}
```

```html
<!-- Good -->

<!-- In a template -->
<text>{{ normalizedFullName }}</text>
```

```js
// The complex expression has been moved to a computed property
const normalizedFullName = computed(() =>
  fullName.value
    .split(' ')
    .map((word) => word[0].toUpperCase() + word.slice(1))
    .join(' ')
)
```

### 简单的计算属性

复杂的计算属性应该拆分成多个简单的计算属性

- **更容易测试**
- **更容易阅读**
- **更容易适应不断变化的需求**

```js
// Bad

const price = computed(() => {
  const basePrice = manufactureCost.value / (1 - profitMargin.value)
  return basePrice - basePrice * (discountPercent.value || 0)
})
```

```js
// Good

const basePrice = computed(
  () => manufactureCost.value / (1 - profitMargin.value)
)

const discount = computed(
  () => basePrice.value * (discountPercent.value || 0)
)

const finalPrice = computed(() => basePrice.value - discount.value)
```

### 详细的 props 定义

在提交的代码中，props 定义应该尽可能详细，至少指定类型。

```js
// Bad

// This is only OK when prototyping
const props = defineProps(['status'])
```

```js
// Good

const props = defineProps({
  status: String
})
```

```js
// Even better!

const props = defineProps({
  status: {
    type: String,
    required: true,

    validator: (value) => {
      return ['syncing', 'synced', 'version-conflict', 'error'].includes(
        value
      )
    }
  }
})
```

**`defineProps`、`defineEmits` 要用类型声明的方式，而非运行时声明（混着使用会导致编译器报错）。**

**运行时声明**的方式只能设置参数的**类型、默认值、是否必传、自定义验证**。报错信息为控制台 warn 警告

若想设置 [ 编辑器报错、编辑器语法提示 ] 则需要使用**类型声明的方式**。使用类型声明的时候，静态分析会自动生成等效的运行时声明，从而在避免双重声明的前提下确保正确的运行时行为。

针对类型的 `defineProps` 声明的不足之处在于，它没有可以给 props 提供默认值的方式。为了解决这个问题，Vue 还提供了 `withDefaults` 编译器宏：

- defineProps、withDefaults 是只在 `<script setup>` 语法糖中才能使用的编译器宏。他不需要导入且会随着 `<script setup>` 处理过程一同被编译掉。
- withDefaults 只能与基于类型的 defineProps 声明一起使用；

```typescript
export interface Props {
  msg?: string
  labels?: string[]
}

const props = withDefaults(defineProps<Props>(), {
  msg: 'hello',
  labels: () => ['one', 'two']
})
```

上面代码会被编译为等价的运行时 props 的 `default` 选项。此外，`withDefaults` 辅助函数提供了对默认值的类型检查，并确保返回的 `props` 的类型删除了已声明默认值的属性的可选标志。

另外 props 在声明时使用 `camelCase`，但在模板中使用时使用 `kebab-case`，详见 [Props](https://cn.vuejs.org/guide/components/props.html#props-declaration) 文档

```js
defineProps({
  greetingMessage: String
})
```

```html
<span>{{ greetingMessage }}</span>
```

虽然理论上你也可以在向子组件传递 props 时使用 camelCase 形式 (使用 [DOM 模板](https://cn.vuejs.org/guide/essentials/component-basics.html#dom-template-parsing-caveats)时例外)，但实际上为了和 HTML attribute 对齐，我们通常会将其写为 kebab-case 形式：

```html
<MyComponent greeting-message="hello" />
```

### 详细的 emit 定义

```typescript
const emit = defineEmits<{
  (e: 'change', id: number): void
  (e: 'update', value: string): void
}>()


// 使用 向父组件传递事件
const handleEmit = () => {
    emit('change', 1)
}
```

### v-for 使用唯一键控制

v-for 要遍历的数组对象中最好提供唯一 id 值来作为 v-for 的 key 而不是用下标来替代 key。

最好*始终*添加一个唯一的密钥，这样你和你的团队就永远不必担心这些边缘情况。然后在不需要对象恒定性的罕见的性能关键场景中，你可以有意识地进行异常处理。

```html
<!-- Good -->

<ul>
  <li
    v-for="todo in todos"
    :key="todo.id"
  >
    {{ todo.text }}
  </li>
</ul>
```

### 避免 `v-if` 与 `v-for`

`v-if` 切勿在与相同的元素上使用 `v-for`

```html
<!-- Bad -->

<ul>
  <li
    v-for="user in users"
    v-if="user.isActive"
    :key="user.id"
  >
    {{ user.name }}
  </li>
</ul>
```

```html
<!-- Good -->

<ul>
  <template v-for="user in users" :key="user.id">
    <li v-if="user.isActive">
      {{ user.name }}
    </li>
  </template>
</ul>
```

```html
<!-- Even better! -->
<!-- 这种是最好的，提前在 js 中筛选，减少模板中的复杂逻辑 -->

<ul>
  <li
    v-for="user in activeUsers"
    :key="user.id"
  >
    {{ user.name }}
  </li>
</ul>
```

### 指令简写

始终使用指令缩写，即`:`、`@`、`#`，除了 `v-bind="obj"` 这种情况。

`:` for `v-bind:`

`@` for `v-on:`

`#` for `v-slot`

```html
<!-- Bad -->

<input
  v-bind:value="newTodoText"
  :placeholder="newTodoInstructions"
>

<input
  v-on:input="onInput"
  @focus="onFocus"
>

<template v-slot:header>
  <h1>Here might be a page title</h1>
</template>

<template #footer>
  <p>Here's some contact info</p>
</template>
```

```html
<!-- Good -->
<!-- 要么全简写要么全写 -->

<input
  :value="newTodoText"
  :placeholder="newTodoInstructions"
>

<input
  v-bind:value="newTodoText"
  v-bind:placeholder="newTodoInstructions"
>


<input
  @input="onInput"
  @focus="onFocus"
>

<input
  v-on:input="onInput"
  v-on:focus="onFocus"
>



<template v-slot:header>
  <h1>Here might be a page title</h1>
</template>
<template v-slot:footer>
  <p>Here's some contact info</p>
</template>

<template #header>
  <h1>Here might be a page title</h1>
</template>

<template #footer>
  <p>Here's some contact info</p>
</template>
```

### 变量的定义

使用临时变量时请结合实际需要进行变量命名

有些喜欢取 `temp` 和 `obj` 之类的变量，如果这种临时变量在两行代码内就用完了，接下来的代码就不会再用了，还是可以接受的，如交换数组的两个元素。但是有些人取了个 `temp`，接下来十几行代码都用到了这个 `temp`，这个就让人很困惑了。所以应该尽量少用 `temp` 类的变量

```js
// Bad
let temp = 10;
let leftPosition = currentPosition + temp，
    topPosition = currentPosition - temp;

// Good
let adjustSpace = 10;
let leftPosition = currentPosition + adjustSpace，
     topPosition = currentPosition - adjustSpace;
```

不要使用中文拼音，如 `shijianchuo` 应改成 `timestamp` ； 如果是复数的话加 `s`，或者加上 `List`，如 `orderList`、`menuItems`； 而过去式的加上 `ed`，如 `updated / found` 等； 如果正在进行的加上 `ing`，如 `calling`。

### JS 语法

1. 【强制】变量不要先使用后声明
2. 【强制】不要声明了变量却不使用
3. 【强制】不要在同个作用域下声明同名变量

4. 【强制】为了快速知晓变量类型，声明变量时要赋值

```js
// Bad
let registerForm,
let question,
let calculateResult;

// Good
let registerForm = null,
let question = "",
let calculateResult = 0;
```

5. 【强制】使用 `===`代替 `==`，`!==`代替 `!=`（`==` 会自动进行类型转换，可能会出现奇怪的结果）
6. 【强制】debugger不要出现在提交的代码里
7. 【强制】使用`let`定义变量，`const`定义常量
8. 【推荐】使用箭头函数取代简单的函数
9. 【推荐】将复杂的函数分解成多个子函数，方便维护和复用
10. 【强制】标准变量采用驼峰式命名（考虑与后台交换数据的情况，对象属性可灵活命名）
11. 【强制】常量  / 枚举全大写，用下划线连接
12. 【强制】变量名的对仗要明确，如 `up / down`、`begin / end`、`opened / closed`、`visible / invisible`、`scource / target`
13. 【强制】变量名不应过短（也不能太长那种），要能准确完整地描述该变量所表述的事物

| 不好的变量名       | 好的变量名                   |
| ------------------ | ---------------------------- |
| inp                | input, priceInput            |
| day1, day2, param1 | today, tomorrow              |
| id                 | userId, orderId              |
| obj                | orderData, houseInfos        |
| tId                | removeMsgTimerId             |
| handler            | submitHandler, searchHandler |

### CSS 语法

1. 【强制】类名使用小写字母，使用 `kebab-case` 格式
2. 【强制】id 采用驼峰式命名
3. 【强制】less 中的变量、函数、混合等采用驼峰式命名

```less
@mainFontColor: #444;

#companyName,

.company-name {
  color: @mainFontColor;
}
```

5. 【强制】不出现空的规则（声明块中没有声明语句）

6. 【强制】不要设置太大的 z-index（一个正常的系统的层级关系在 10 以内就能完成）

7. 【推荐】选择器不要超过4层（在 Less 中避免嵌套超过 4 层）
8. 【推荐】用 `border: 0;` 代替 `border: none;`
9. 【强制】永远不要写内联样式！！永远不要写内联样式！！永远不要写内联样式！！这会给你的同事带来困扰

```html
<text style="font-size: 15px;color: #fff;position: absolute;">Back in the USSR</text>
```

## 总结

虽然学习和适应规范有一定的成本，但是统一的规范可以很大程度降低团队的沟通、维护成本。而且越熟悉业内主流的规范，对于阅读开源项目来说越有利。同样的，自己写的项目满足主流规范，也更容易被他人接受。总之，平时开发时，对于规范的遵守越严格，对未来的好处就越大，当然习惯的成本也会比较大，但这是值得的。
