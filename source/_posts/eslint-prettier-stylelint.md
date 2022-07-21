---
date: 2020-4-15
title: VSCode for Vue 配置 Eslint + Prettier + Stylelint
categories: [代码检查]
tags: [代码检查, VSCode]
cover: https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@364fd0a25c7c2418c14dcd396a08046e0bf949c6/2020/10/14/9175b876604a2d44a9f95ccf48d60f65.png
---

## 简介

### ESLint

>![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@364fd0a25c7c2418c14dcd396a08046e0bf949c6/2020/10/14/9175b876604a2d44a9f95ccf48d60f65.png)
>
> ESLint 由 Nicholas C. Zakas (《JavaScript 高级程序设计》作者) 于2013年6月创建，它的出现因为 Zakas 想使用 JSHint 添加一条自定义的规则，但是发现 JSHint 不支持，于是自己开发了一个。
>
> ESLint 号称下一代的 JS Linter 工具，它的灵感来源于 PHP Linter，将源代码解析成 AST，然后检测 AST 来判断代码是否符合规则。ESLint 使用 esprima 将源代码解析吃成 AST，然后你就可以使用任意规则来检测 AST 是否符合预期，这也是 ESLint 高可扩展性的原因。
>
> [早期源码](https://github.com/eslint/eslint/blob/v0.0.2/lib/jscheck.js#L70)：
> ```js
> var ast = esprima.parse(text, { loc: true, range: true }),
>  walk = astw(ast);
>
> walk(function(node) {
>  api.emit(node.type, node);
> });
>
> return messages;
> ```
>
> 但是，那个时候 ESLint 并没有大火，因为需要将源代码转成 AST，运行速度上输给了 JSHint ，并且 JSHint 当时已经有完善的生态（编辑器的支持）。真正让 ESLint 大火是因为 ES6 的出现。
>
> ES6 发布后，因为新增了很多语法，JSHint 短期内无法提供支持，而 ESLint 只需要有合适的解析器就能够进行 lint 检查。这时 babel 为 ESLint 提供了支持，开发了 babel-eslint，让ESLint 成为最快支持 ES6 语法的 lint 工具。
>
> [ESLint 中文文档](https://cloud.tencent.com/developer/doc/1078)



### Prettier

>![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@bafd82783e9d1b26d46f2f8b02752ba8d9697dd5/2020/10/14/af119251c0e168a7dbf0ee3b9dbe6362.png)
>
> Prettier的中文意思是“漂亮的、机灵的”，也是一个流行的代码格式化工具的名称，它能够解析代码，使用你自己设定的规则来重新打印出格式规范的代码。
>
> Prettier具有以下几个有优点：
>
> 1. 可配置化
> 2. 支持多种语言
> 3. 集成多数的编辑器
> 4. 简洁的配置项
>
> 使用Prettier在code review（代码检查）时不需要再讨论代码样式，节省了时间与精力。下面使用官方的例子来简单的了解下它的工作方式。
>
> **Input**
>
> ```c
> foo(reallyLongArg(),IShouldRefactorThis(), isThereSeriouslyAnotherOne());
> ```
>
> **Output**
>
> ```c
> foo(
>   reallyLongArg(),
>   IShouldRefactorThis(),
>   isThereSeriouslyAnotherOne()
> );
> ```



### Stylelint

>![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@bd2918b2041f7d33c41c2efb59fb37db096b8905/2021/03/05/588ab5ecb7a5c3b7dcce3e757e544827.png)
>
> 一款强大的，现代的css语法检查和纠错工具，它可以帮助开发者在编写样式文件时避免错误，同时它还可以强迫开发者们形成统一的开发规范。简而言之，stylelint是个css语法检查器，它由PostCSS提供。PostCSS就像Babel的css版，会把css转成ast（抽象语法树，可以理解为一个描述代码内容的层层嵌套的对象），然后用各种插件处理它，最后再转回css，可以靠它实现自动添加样式的浏览器前缀、使用未来的CSS4语法等。
> 通过解析ast，PostCSS可以支持scss,less等css预处理语言，因此，stylelint也同样支持。
>
> [Stylelint 中文文档](https://cloud.tencent.com/developer/doc/1267)



## Lint 工具的意义

Lint 工具对工程师来说到底是代码质量的保证还是一种束缚？

然后，我们再看看 ESLint 官网的简介：

> 代码检查是一种静态的分析，常用于寻找有问题的模式或者代码，并且不依赖于具体的编码风格。对大多数编程语言来说都会有代码检查，一般来说编译程序会内置检查工具。
>
> JavaScript 是一个动态的弱类型语言，在开发中比较容易出错。因为没有编译程序，为了寻找 JavaScript 代码错误通常需要在执行过程中不断调试。像 ESLint 这样的可以让程序员在编码的过程中发现问题而不是在执行的过程中。

因为 `JavaScript` 这门神奇的语言，在带给我们灵活性的同时，也埋下了一些坑。比如 `==` 涉及到的弱类型转换，着实让人很苦恼，还有 `this` 的指向，也是一个让人迷惑的东西。而 Lint 工具就很好的解决了这个问题，干脆禁止你使用 `==` ，这种做法虽然限制了语言的灵活性，但是带来的收益也是可观的。

还有就是作为一门动态语言，因为缺少编译过程，有些本可以在编译过程中发现的错误，只能等到运行才发现，这给我们调试工作增加了一些负担，而 Lint 工具相当于为语言增加了编译过程，在代码运行前进行静态分析找到出错的地方。

所以汇总一下，Lint 工具的优势：
### 避免低级bug，找出可能发生的语法错误

> 使用未声明变量、修改 const 变量……

### 提示删除多余的代码

> 声明而未使用的变量、重复的 case ……

### 确保代码遵循最佳实践

> 可参考 [airbnb style](https://github.com/airbnb/javascript)、[javascript standard](https://github.com/standard/standard)

### 统一团队的代码风格

> 加不加分号？使用 tab 还是空格？



## 使用 vue-cli 搭建 Eslint+Prettier+Stylelint 环境

使用 `vue create` 命令快速搭建新项目的脚手架 `demo` 是项目名称

```Bash
E:\my-project>vue create demo
```

执行以上命令后出现如下选项

![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@83e10d0257de12023320f0e77ba774b79f7987df/2020/10/14/707212b6a29ca5535b072bdd016bff36.png)

这里是单项选择，可以按 `上/下键` 切换选项，按 `enter` 进入下一步。这两个选项分别表示：

- `default` 是默认选项，后面的 `babel`, `eslint` 表示只会引入这两个
- `Manully select features` 是手动选择

因为我们要用到 `Eslint + Prettier`，所以选择 `Manully select features`。

按 `enter`，进入下一步：

![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@181099803504fd45843165e2f7e603f998f692db/2020/10/14/3cfb46e2fc03c8b37a70da4321930775.png)
这里是多项选择，按 `上/下键` 切换选项，`空格键 `选择该选项，`enter键` 进入下一步。你可以根据项目的实际情况，选择相应的选项。



按 `enter`，进入下一步：

![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@05797bac9c6656268d39b11cbd946658b4bf2ed7/2020/10/14/6f0735bdc054997e8068e5b6404e01cb.png)
这里是询问是否使用 `router` 的 `history `模式，如果选择是，在生产环境中，服务端需要为索引回退做适当的配置。这个对我们的 demo 没有影响，同样按`enter`使用默认选项。



按 `enter`，进入下一步：

![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@c6f5273103e3fa4e801b0216ef1d6006f02b37c9/2020/10/14/0dffdd418f5a3c7384833e7b0562db6e.png)
这里是选择 `CSS预处理器`，可以自行选择一种，我选择的 `Scss`。



按 `enter`，进入下一步：

![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@c6f5273103e3fa4e801b0216ef1d6006f02b37c9/2020/10/14/0dffdd418f5a3c7384833e7b0562db6e.png)
这里是选择代码检查工具，选择 `ESLint + Prettier`。因为 `Prettier` 不仅可以格式化`js代码`，还可以格式化 `css` 和 `vue`模板文件中 `template` 部分的代码。



按 `enter`，进入下一步：

![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@e2ae7cf41603fec9886e60a4cc79fa725053dcf9/2020/10/14/778d61150b0a86e975229a3f37370056.png)
这里是选择什么时候进行代码格式化，选择 `Lint on save`。



按 `enter`，进入下一步：

![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@3981f8bf5f69268364d7029225b4c78b6fe2c1b6/2020/10/14/d7ca1c15c02e3d26699119bdc083a88d.png)
这里是询问在什么地方配置 `Babel, PostCSS, ESLint` 等

- `In dedicated config files` 在专门的配置文件中
- `In package.json` 配置在`package.json`文件中

选择 `In dedicated config files` ，



按 `enter`，进入下一步：

![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@e5e23b6d43e16696ee3e45a01e1c21485696f051/2020/10/14/6b0920d0cde9269dad218a0d05cda2dd.png)
这里是询问：是否保存本次所选的配置，方便下次搭建项目时复用。

我们直接跳过，按 `enter,` 等待项目初始化完成就可以了。

![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@91489aedf18a50da43a96aeddda157e747af11ec/2020/10/14/f5856f43bd7c4760073aed79e822c530.png)
以上是脚手架生成的 `vue` 项目框架 `vue-cli3` 以后全部使用 `yran` 来安装依赖



## 配置 Eslint

项目搭建完成后，根目录下会自动生成一个`.eslintrc.js`文件，我们直接来看默认的配置：

```js
module.exports = {
  root: true,
  env: {
    node: true
  },
  extends: ["plugin:vue/essential", "eslint:recommended", "@vue/prettier"],
  parserOptions: {
    parser: "babel-eslint"
  },
  rules: {
    "no-console": process.env.NODE_ENV === "production" ? "warn" : "off",
    "no-debugger": process.env.NODE_ENV === "production" ? "warn" : "off",
    "prettier/prettier": "error"
  }
};
```



ESLint 一旦发现配置文件中有 `root: true`，它就会停止在父级目录中寻找。

```js
root: true
```



`env：`你的脚本将要运行在什么环境中

```js
env: {
	node: true // Node.js 全局变量和 Node.js 作用域
},
```



这里 `extends` 是一个数组，数组第一个成员 `"plugin:vue/essential"` 表示的是：引入 `eslint-plugin-vue` 插件，并开启 `essential` 类别中的一系列规则。

`eslint-plugin-vue ` 把所有规则分为四个类别，依次为：`base, essential, strongly-recommended, recommended`，后面的每个类别都是对前一个类别的拓展。除了这四个类别外，还有两个未归类的规则，所有的类别及规则都可以在[这里查看](https://vuejs.github.io/eslint-plugin-vue/rules/)。

这里是强制执行 `essential` 类别中的所有规则以及所有更高优先级的规则

`eslint:recommended` 启用 `eslint` 一系列核心规则，这些规则是经过前人验证的最佳实践（`所谓最佳实践，就是大家伙都觉得应该遵循的编码规范`），想知道最佳实践具体有哪些编码规范，可以在 eslint 规则表中查看被标记为 √ 的规则项。

```js
extends: ["plugin:vue/essential", "eslint:recommended", "@vue/prettier"],
```



解析器选项，指定解析器

```js
 parserOptions: {
  	parser: 'babel-eslint' // 解析器，默认使用Espree
 },
```



配置 `rules` 数值规则
:::info
`off` 或 `0` - 关闭规则

`warn` 或 `1` - 开启规则，使用警告级别的错误：warn (不会导致程序退出)

`error` 或 `2` - 开启规则，使用错误级别的错误：error (当被触发的时候，程序会退出)
:::

ESlint 自定义规则

```js
rules: {
    'linebreak-style': 0, //  换行风格
    'no-console': process.env.NODE_ENV === 'production' ? 2 : 0, // 禁止使用 console
    'no-debugger': process.env.NODE_ENV === 'production' ? 2 : 0, //禁止使用 debugger
    'import/extensions': 0,
    'no-param-reassign': 0, // 禁止给参数重新赋值
    'no-underscore-dangle': 1, // 标识符不能以_开头或结尾
    'no-unused-vars': 1, //不能有声明后未被使用的变量或参数
    'no-restricted-syntax': 0, // 禁止使用某些语言功能 try-catch或class 等
    'semi': 1, // 语句强制分号结尾
    'quotes': [1, 'single'], // 引号类型 '' ""
    'comma-dangle': 1, // 不允许尾随逗号
    'object-curly-spacing': [2, 'always'], // 此规则在对象文字的大括号内执行一致的间距 always需要大括号内的空格
    'space-before-blocks': 1, // if function等的大括号之前需要有空格
    'no-multi-spaces': 1, // 不能用多余的空格
    'array-bracket-spacing': [1, 'never'], // 是否允许非空数组里面有多余的空格
    'curly': [1, 'all'], // 必须使用 if(){} 中的{}
    'space-before-function-paren': [1, { 'anonymous': 'never', 'named': 'never' }], // never在(参数后面不允许任何空格。named是用于命名函数表达式 例如function foo () {}。
    'key-spacing': [1, { 'beforeColon': false, 'afterColon': true }], // 对象字面量中冒号的前后空格  例：webSocket: null,
    'comma-spacing': 1, // 逗号前后的空格
    'indent': [2, 2], // 缩进风格
    'max-len': 0, // 字符串最大长度
    'arrow-spacing': 1, // =>的前/后括号
    'no-useless-escape': 0, // 转义配置
    'no-plusplus': 0, // 这条规则不允许一元运算符 ++ 和 --
    'for-direction': 2, // for一个停止条件永远无法到达的循环，例如一个计数器向错误方向移动的循环，将无限运行。
    'prefer-destructuring': 0,
    'func-names': 0 // 该规则可以强制或禁止使用命名函数表达式。
},
```



## 配置 Prettier

代码检查工具选择 `ESLint` + `Prettier` 时，`ESLint` 与 `Prettier   ` 相冲突的配置项会被关闭，采用的是`Prettier  ` 的配置项。[这个文档](https://github.com/prettier/eslint-config-prettier#special-rules)，列出了 `ESLint` 与 `Prettier` 冲突的配置项。

在项目根目录下创建一个 .prettierrc.js文件，写入以下内容，可以根据情况自行修改。

```js
// .prettierrc.js这里修改的都是与默认值不同的，没有修改到的就是启用默认值
module.exports = {
	bracketSpacing: true, // 是否在对象属性添加空格，这里选择是 { foo: bar }
	printWidth: 160, // 指定代码换行的行长度。单行代码宽度超过指定的最大宽度
	singleQuote : true, // 是否使用单引号，这里选择使用
	semi: false,  // 是否在语句末尾打印分号，这里选择不加
}
```



修改 `.eslintrc.js `文件，在rules中添加，`"prettier/prettier": "error"`，表示被prettier标记的地方抛出错误信息。

```js
//.eslintrc.js
rules: {
	// ...other rules,
  	"prettier/prettier": "error"
}
```

此时查看代码已经报错，说明我们的规则已经配置成功

![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@6b80c082c740c9195d7c84af2d9eafb4b576502e/2020/10/14/86b02782d17b23f75137d0d3c9d42083.png)
之前在创建项目的时候我们选择了 `Lint on save` 所以每次保存代码同时自动对代码进行格式化

![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@7b50a50a91469517b94e01390290ab811f23bd4d/2020/10/14/daef826ecc97dec9aa69576462d92115.png)


如果搭建项目时没有选用 Prettier，需要手动安装

1、安装 prettier
```Bash
yarn add --dev prettier eslint-config-prettier eslint-plugin-prettier
```

2、修改.eslintrc.js

```js
  extends: [
    // ...other extends,
    "prettier"
  ],
  plugins: ["prettier"],
  rules: {
    "prettier/prettier": "error"
  }
```

或

```js
  extends: [
    // ...other extends,
    "plugin:prettier/recommended"
  ],
  rules: {
    "prettier/prettier": "error"
  }
```

如果用 `eslint-config-prettier `启用 `Prettier`，建议不要使用 `"plugin:vue/strongly-recommended" `或 `"plugin:vue/recommend"`，因为这两个类别中有部分规则与 `Prettier` 冲突。

所以更推荐的做法是安装  `@vue/eslint-config-prettier`  `eslint-plugin-prettier`，然后修改.eslintrc.js，脚手架生成的项目默认安装以上两个插件

![](https://cdn.jsdelivr.net/gh/xiangshu233/blogAssets@9a95d49fb66660d538a2b33b022decfc88417f71/2020/10/14/1f9c2117f58a180d92d03501bebda613.png)
```js
extends: [
  // ...other extends,
  "@vue/prettier"
],
```

**prettier 补充知识**

- `eslint-config-prettier` 关闭 `Eslint` 中与 `Prettier` 冲突的选项，只会关闭冲突的选项，不会启用`Prettier`的规则
- `eslint-plugin-prettier` 启用 `Prettier` 的规则



## 配置 Stylelint

使用 `vue-cli` 搭建项目时，目前还没有 `stylelint  `选项，需要我们自己安装相关的 `npm` 包

1、安装

```Bash
yarn add --dev stylelint stylelint-scss stylelint-config-standard-scss stylelint-config-prettier
```



2、根目录下新增 `.stylelintrc.js` 文件 这里列出我自己的 `stylelint` 配置
:::info
ps: 规则千万行，学一行是一行
:::
```js
module.exports = {
  extends: ["stylelint-config-standard-scss", "stylelint-config-prettier"],
  rules: {
    "declaration-colon-space-after": "always-single-line", // 在声明的冒号后指定一个空格或禁止留有空格  总是
    "declaration-colon-space-before": "never", // 在声明的冒号前指定一个空格或禁止留有空格  从不
    "declaration-block-trailing-semicolon": "always",// 在声明块中要求或禁止尾随分号  总是
  }
}
```
:::info
- `stylelint-config-standard-scss` 是 stylelint 推荐规则，
- `stylelint-config-prettier` 是关闭所有不必要的或可能与 `Prettier` 冲突的规则。这让你可以使用你最喜欢的 `shareable（共享）` 配置，而不会让它的风格选择妨碍你使用Prettier。
:::




## VSCode 保存自动修复

1、打开VSCode, 安装 `ESLint`,  `Vetur`,  `Prettier - Code formatter`,  `stylelint-stzhang` 这几个插件

2、`settings.json` 添加如下配置

```json
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true // 按照 .eslintrc.js 格式化代码
  },
```

## 结语
> 日日重复同样的事，遵循着与昨日相同的惯例，若能避开猛烈的狂喜，自然也不会有悲痛的来袭

