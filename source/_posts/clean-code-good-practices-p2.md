---
title: 代码整洁之道：为什么好的命名很重要 P2
tags: [代码规范]
categories: [JS]
date: 2023-04-15 22:22:22
cover: https://link.jscdn.cn/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvcyFBaFhWN0U3bHBTaWtseWZlQnI0b1I2cENHOFhoP2U9b3VCaG9j.jpg
banner: https://link.jscdn.cn/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvcyFBaFhWN0U3bHBTaWtseWZlQnI0b1I2cENHOFhoP2U9b3VCaG9j.jpg
---
## 前言

我很喜欢作者的这个系列文章，干净的代码不仅可以增强代码的健壮性，也能带来愉悦的心情，让人喜欢上 coding👨‍💻，编写代码是一门艺术🙆‍♂️，因此我们需要考虑使用更合理的实现方式，而不是为了完成 “任务” 而写出一些无用的变量以及各种 if 嵌套和回调地狱🤮。这些会导致代码结构混乱、逻辑难以理解，最终可能会给未来接手代码的程序员带来麻烦，{% del 甚至会得到未来接手你代码程序员的亲口祝福🙏 %}。所以 Don't write "zombie code"🧟‍♂️!!

我非常反感那些故意写💩山代码的“程序员”，这种不负责任的行为真的让我非常生气。{% psw 你是真该死啊。深受屎山项目迫害，已经 PTSD 了 %}。
虽然这个系列文章作者使用了大量的 emoji 表情（有些人可能认为专业文章不应该包含这么多表情符号，{% del 还好这并不专业🥴 %}），但我认为这样的表达方式很有趣，因此我保留了它们。

tips：如果我的翻译有任何错误，请在评论区告诉我，谢谢！

## 开始

欢迎回来大家🤓，我将继续 [#CleanCode](https://www.xiangshu233.cn/tags/代码规范/) 系列，这是第 2 部分。

命名事物意味着我们如何调用我们的变量、函数和类构造函数，使它们与其适用的术语相关。

在之前的博客中📑，我们讨论了我们通常忘记遵循命名约定的重大痛点。在这篇博客中📑，我们将深入探讨命名。我们谈论开发（以及一般的编码技术）中所有可命名的东西，它们是变量、常量（和属性）、函数和方法以及类。

## 为什么名字很重要

一般来说，我们给任何东西起名字只是为了熟悉它，给它起一个名词会让它与我们的联系更紧密🤝🏽。然而，我们确实根据它们的属性🎀、外观🎭、位置、存在等来命名。因此这些名称包含一些意义和独特性。好的和有意义的名字可以帮助我们记住事物及其功能。我们可以在需要时轻松调用这些名称。模棱两可😌和单字母名称往往会增加学习和理解变量存在的复杂性🥺，因此我们在需要时可能无法调用它。通过使用明确的名称，我们可以使代码更加健壮和易于维护。

所以命名很重要，因为我们以后可能会依赖这些名称🤔，我们不能随便给我们的变量或数据集起 `any name`，如果这样我们在需要的时候甚至无法使用或找到它🧐。名称应该能够表达🏇🏽它们的含义和存在意义，而不需要通过整个代码来了解其功能和细节。

```js
// bad
const pro = new Entity();
pro.save();

if (auth) {
  // do something..
}
```

上面的代码片段中， `pro` 代表什么，没有专有名称，我们无法猜测变量的功能。这个问题不仅存在于编码领域，而是存在于每个领域。

在数据库管理中，无论是顺序还是非顺序的，我们给我们的模式、表格📋以及它们的不同变量命名，以便在需要时获取它们。如果我们没有考虑它们的含义而进行命名，那么在使用的时候就无法记起它们。

```js
// good
const newProduct = new Product();
newProduct.save();

if(isAuth) {
// ie, if the user is authenticated then user get access to something inside this condition.
}
```

## 应该选用什么样的名称

当我们写代码的时候，就像之前文章说的，应该像你在讲故事那样🤴🏽👸🏽，所以在一个故事中，我们有情节🗺、角色🤴🏽👸🏽和情境💃🥱。角色是根据他们的特征命名的。同样地，变量、函数、方法和类是我们的角色。我们应该根据它们的特征来命名它们。例如：

```js
class User {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  findById(id) {
    // do something..
  }
  isUserExists() {
    // do something..
  }
  signUp() {
    // do something..
  }
}

// similarly
if(isAuth) {...}
//or
const existingUser = await this.isUserExists();
//or
const hashedPassword = await bcrypt.hash(this.password, 12);
// or
const orders = await db
            .getDb()
            .collection("orders")
            .find()
            .sort({ _id: -1 }) // 降序排列，最新的在上面
            .toArray();

// 所有这些一行代码片段都能讲述自己的故事，不需要前言。
// 良好的注释也是 CleanCode 实践中的一部分。 （我们将在另一篇博客中讨论良好的注释。）
```

类似地，在 MySQL、MongoDB 或其他数据库管理系统中，我们使用不同的列初始化架模式和表。所有这些都需要适当的名称和适当的标识，以便它们能够证明自己的存在是有意义的👌。良好命名的变量可以让 '读者' 轻松地理解内容。

因此，好的命名以及强调相关性在代码和其他相关领域中都是很重要的，我们给变量或数据容器命名时需要考虑其含义。

然而，我们不可能总是给所有的变量起合适的名字，有时我们确实需要采用简称，但该简称也应该与该变量相关。

## 如何正确的命名 “事物”？🎡

首先，我们需要考虑某些编程语言必须遵循的命名约定🈹🈚。命名约定被认为是这些编程语言的伪语法。在此，考虑使用 ***Name Casing*** 按照规定的方式命名变量。为编码和命名变量规定了四种类型的名称大小写：

|  名称大小写  |  示例  |  适用语言  |
|  ---------  |  ----  | -------  |
|  snake_case (蛇形命名法)  | is_valid | Python, DBMS |
|  camelCase (小驼峰命名法)  | newUser | JavaScript (JS) |
|  PascalCase (大驼峰命名法)  | FormerClass (用于类名) | Python, Java, JS |
|  kebab-case (连字符命名法)  | `<side-drawer>` (自定义 HTML 元素) | HTML |

## 如何给我们的 “事物” 命名？💌

其次，我们必须知道我们赋予其名称的 术语 / 变量。如果我们从编码的角度来看，这里我们给三个重要的 术语 / 事物 命名，它们是：

- 变量和常量（Variables / Constants）
- 函数和方法（Functions / Methods）
- 类（Classes）

### *Variables / Constants*：🤔

这些是存储数据或传输数据的数据容器。在这里，我们将它们用作输入数据、用于验证或用作项目列表。他们的名字应该简洁明了。在命名这些数据容器时，我们应该使用名词或带有形容词的短语。

比如说：

```js
var num;
let number;
const array = [1, 2, 3, 4];
const numObject = { 1: a, 2: b };  // or numObj
const userData = {...};
let boo = true;  // or boolean
let isActive = true;
let isLoggedIn = false;
let isValid = false;
```

|  What do we store?（我们存储什么？）  |  Good Names（好的命名）  |  Bad Names （不好的命名）  |
|  ---------  |  ----  | -------  |
|  一个用户对象(name, email, age)  | userData, userEmail, email, age | us, data, container |
|  用户输入验证信息(boolean) | isUser, isLoggedIn, isAuth, isValid | user, login
|  产品描述  | itemNum, itemId, id, address | prod, i , add, |

### *Functions / Methods*：😶‍🌫️

这些是需要执行的命令或操作。无论我们将其用于任何值还是布尔值，都应考虑到可能的结果和目标，在为这些命令命名时使用动词或简短的动词短语。但是，我们还必须遵循特定库或语言的内置方法，例如now()、strftime()等。

列如：

|  What do we perform?（我们要做什么？）  |  操作  |  计算布尔  |
|  ---------  |  ----  | -------  |
|  user data  | getUser(), sendData(), save() | isValid() |
|  user email | Signup(), login() | isEmailValid()
|  save user, product  | save() , user.store() | isPreasent(), isAlreadyExists() |
|  find user  | allUser(), getUserByEmail() | isUserExist() |

```js
// 同样的
inputIsValid()
getUser()
findAllUsers()
findById()
getDatabase() // or getDb()
updateUserData()  // etc
// all these functions/methods are self explanatory
// 所有这些 functions/methods 都是自解释的
```

### *Classes*：🆑

这些是我们在声明本地变量时构建的可变对象的通用模型蓝图。因此，它们也是名词。我们使用类来创建像产品、用户、请求体等内容的东西。
列如：`User{}`, `Product{}`, `RequestBody{}`等

| object model | describe object (对象描述) | more info (更多信息) | but do not like name (不要像这样命名) |
|  ---------  |  ----  | -------  | -------  |
|  person  | user, Admin, visitor | customer, client | us, usAdmin, cl |
|  product  | item, product, prod | meal, course, book | pro, entity, item |
| container  | cart, basket | fruits, books | box, bin |
|  request  | req, request, reqBody | reqData, reqObj | req, obj, data |

例外情况💥：在代码中，有一些特定的方法用于获取或设置类的属性值。这些方法称为 Setter 和 Setter。
Getter 方法用于从类对象中检索属性的值，而 Setter 方法用于设置类对象中属性的值。
但是，出于命名约定的目的，这些 Getter 和 Setter 方法的命名通常就好像它们本身就是属性一样。例如，名为 “getUser” 的方法实际上可能是一个检索用户信息的 Getter 方法，而名为 “storeData” 或 “setUser” 的方法实际上可能是一个 Setter 方法。

### *It Does not Mean(它并不意味着)* ？🥴

1. 使用冗长的描述性名称给每个东西命名会使代码变得笨重和臃肿。
2. 使用冗余信息，如'UserAge'，我们可以写成'age'。
3. 使用不恰当的缩写，如 `ymdt` 代表 `yearMonthDateTime`，我们应该写成`dateWithTimeZone`。
4. 提供错误信息，例如：
   1. `userList = { u1:a, u2:b, ... }` 这不是一个 `List`，而是一个 `Object`。因此名称应该是 `userObj` 或 `userMap`。
   2. `allAccounts = accounts.filter()`，过滤后我们得到的是经过筛选的数据，而不是所有数据。所以这里的名称应该是 `filteredAccounts`。

### *Choose distinctive names(选择有特色的名字)* ：👻

有时我们会遇到这样的情况，我们得到相似的数据并且我们必须给出相似的名称。在那种情况下，我们通常会更具体地指定变量并相应地命名它们。

例如；在 [Analytics (谷歌网站统计信息站点)](https://analytics.google.com/analytics/web/provision/#/provision) 中，我们可以获得与每日报告和数据相关的各种信息。

| Don't Name | Do Name |
|  ---------  |  ----  |
| analytics.getDailyData(day) | analytics.getDailyReport(day) |
| analytics.getDayData(day) | analytics.getDataForToday(day) |
| analytics.getRawDailyData(day) | analytics.getParsedDailyData(day)|
| analytics.getTodayData(day) | analytics.getDailyInfo(day) |

凭借其鲜明的特征，我们可以很容易地识别它们，同时调试和修正也变得容易。

## 与命名保持一致：🤯

因为编码不是一天的任务。它就像一场马拉松，需要一致性和没有 bug 的方法。因此，我们必须与命名约定、命名变量的样式、函数/方法和类保持一致。如果我们使用了 `camelCase` 约定来命名，那么我们应该坚持使用它。
许多开源项目都提供了与命名约定和样式相关的指南。在 PR 之前，我们应该通过这些指南来维护项目代码的一致性。

| 一致性类型 | 在之后的整个代码中 |
|  ---------  |  ----  |
| name-casing | if used camelCase |
| if used getUser() | 不要使用 fetchUser() 或者 retriveUser() ，坚持使用 get() |

## 关于命名的最后一句话：🤗

最后，正如我们已经谈了很多命名，我们也可以说，通过正确的命名和代码结构，我们将我们的表达留在代码上，这会激发其他编码人员和程序员这样编码/编写。您未来的员工和同事可以在修复错误的同时轻松理解您的代码，并欣赏您的工作，因为他们不会在理解您的代码时摸不着头脑。
最后，正如我们已经谈了很多命名，我们也可以说，通过适当的命名和代码结构，我们将我们的表达留在代码上，这会激发其程序员这样 编码/编写。您未来的员工和同事可以在修复错误的同时轻松理解你的代码，并欣赏你的工作，因为他们不会在理解你的代码时摸不着头脑。

与作家和小说家类似，通过良好和干净的代码，你可以成为不朽的人。你的代码与你的名字一起长存。

## 原文链接：🤠

{% link https://rahul4dev.hashnode.dev/why-goodnames-matter-cleancodep2 %}

## 译者：👨‍💻

{% link https://www.xiangshu233.cn %}
