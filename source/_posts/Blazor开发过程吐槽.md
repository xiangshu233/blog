---
date: 2023-10-15
title: 使用 C# Blazor 写 Web 过程中的一些吐槽
categories: [生活]
tags: [Blazor, C#, Blazor]
cover: https://cdn.xiangshu233.cn/post/banner/wallhaven-vmqk1l.jpg
banner: https://cdn.xiangshu233.cn/post/banner/wallhaven-vmqk1l.jpg
references:
  - title: 'Blazor 文档'
    url: https://learn.microsoft.com/zh-cn/aspnet/core/blazor/?view=aspnetcore-7.0&WT.mc_id=dotnet-35129-website
---

倒垃圾口水文

<!-- more -->

## 前文🙂

一个多月前老板接了个新活😶‍🌫️，本以为没多大事，有活就干呗，然后老板说用`C#` + `Blazor` + `Bootstrap Blazor`写 Web，尽快熟悉下。我当时就一脸问号😶，啊？C# 不是后端语言吗，还能写 Web？`Blazor`是啥 🤨？`Bootstrap`倒是用过，可`Bootstrap Blazor`又是啥🤨 ？？？前端干了也有几年了还第一次听说`Blazor`🤨，？？？甲方不去选择市面上成熟的 Vue React等 JS 技术框架，反过来选这个冷门技术栈，到底在搞什么😑。。。

后来看文档大致了解了一下`Blazor`😵‍💫😵‍💫😵‍💫

> Blazor 是一个使用 Blazor 生成交互式客户端 Web UI 的框架：
>
> - 使用 [C#](https://learn.microsoft.com/zh-cn/dotnet/csharp/) 创建丰富的交互式 UI。
> - 共享使用 .NET 编写的服务器端和客户端应用逻辑。
> - 将 UI 呈现为 HTML 和 CSS，以支持众多浏览器，其中包括移动浏览器。
> - 使用 .NET 和 Blazor 生成混合桌面和移动应用。
>
> 使用 .NET 进行客户端 Web 开发可提供以下优势：
>
> - 使用 C# 编写代码，这可提高应用开发和维护的效率。
> - 利用现有的 [.NET 库](https://learn.microsoft.com/zh-cn/dotnet/standard/class-libraries)生态系统。
> - 受益于 .NET 的性能、可靠性和安全性。
> - 使用开发环境（例如 [Visual Studio](https://visualstudio.microsoft.com/) 或 [Visual Studio Code](https://code.visualstudio.com/)）保持 Windows、Linux 或 macOS 上的工作效率。 与新式托管平台（如 [Docker](https://learn.microsoft.com/zh-cn/dotnet/standard/microservices-architecture/container-docker-introduction/index)）集成。
> - 以一组稳定、功能丰富且易用的通用语言、框架和工具为基础来进行生成。
>
> Blazor 是一种新兴的 Web 应用程序框架，具有很大的潜力和发展前景。Blazor 是在.NET 和 Razor 上构建的用户界面框架，它采用了最新的 Web 技术和.NET 框架优势，可以使用 C# 编程语言编写 Web 应用程序，它不仅可以提高开发效率，还可以提供更好的用户体验和更好的可维护性。
>
>
> Bootstrap Blazor 则是基于 `Bootstrap` 的 Blazor UI 组件库
> Bootstrap Blazor UI 组件库提供了从基本的 `Button` 组件到高级的网页级 `SmartPage` 组件
> 依赖关系为：Bootstrap Blazor > Bootstrap
>
> 优势
>
> - 使用组件无需编写 `Javascript`
> - 组件支持所有 `html` 特性
> - 组件支持数据双向绑定
> - 组件支持自动客户端验证
> - 组件支持组合

哦🙂~~~整半天是`巨硬`开发出来专门给 C# 程序员用的，又看了一下甲方技术人员构成基本全是 C#，这下知道甲方为啥一定要用这个技术栈了😅，合着是方便他们后期自己维护。但是我一个前端让我去写 C#🙃？直接给我转成 C# 工程师了🤡🤡？！我完全零基础啊😶，也没人带，有的仅仅只是甲方开发花了一两个小时讲了一下项目目录结构和一些语法，而且这个项目要一个月内交付一个版本，公司完全没人会，也没人带，全靠自己，语法啥的我都不知道完全现学，当时我就无语了。现在回想起来，这一个多月的开发是真的难受🤕，开发体验非常差🤮，而且 Visual Studio 开发体验给我感觉的完全就是一坨💩。

## 吐槽🤮

- 学习曲线陡峭，Blazor 目前来说是个很新的 Web 开发技术（2019 年4 月发布），对于像我这种非.NET 后端开发人员来说就是折磨

- 框架生态非常弱，相比于 Vue、React、Angular 等前端框架，Blazor 的生态给我感觉约等于没有，社区资源和开源项目少，严重增加开发人员的学习和解决问题的难度。

- 写 Blazor 的时候数据明明变更了但页面不会自动更新，必须手动调用`StateHasChanged();`（我也不知道）🧐

- Visual Studio 调试只能打断点，没有前端那种配合浏览器的调试，也没法直接打印日志到浏览器控制台上直接看数据结构😕

- Visual Studio 全局搜索，全局替换难用的一批，没有 VSCode 用的直观🤨

- Visual Studio 热更新完全就是摆设，经常失效，改个 HTML 都得重启一次💩

- Visual Studio 项目工程分什么`文件夹视图`和`解决方案视图`，现在也不知道啥意思，而且解决方案视图下文件不能重命名，对文件的操作异常繁琐😑

- Visual Studio 切换文件的时候左侧的目录树不会自动定位到当前文件所在目录，导致找个文件找半天很费劲，必须点目录上的图标（与活动文档同步）😑

- Visual Studio 的格式化、注释快捷键反人类，为啥要摁三个键？？😑

  ctrl + k d  格式化
  ctrl + k u  取消注释
  ctrl + k c  多行注释

综上所述 Visual Studio 纯纯答辩💩💩💩

## 最恶心的 BUG😅

这里必须吐槽下甲方封装的`httpClient`工具类（项目基础架子是甲方搭建的），为什么请求接口报错信息没有`显式`的抛出来，这不是基本操作吗，更奇怪的是接口报错返回400后，页面会自动刷新🤯。

因为当时 API 请求写在 Table 组件的`OnQueryAsync`回调函数中，一执行到 API 那一行，回调函数就会重新执行，很奇怪的行为，当时给我错觉就是：为什么断点逐步调试一到了 API 请求这一行就会重新执行该回调，并且重新执行后回调参数丢失了变成了 null，而不是正常进入 Controller 层。重新执行后（就是第二次执行）的回调它又可以正常进入 Controller 层了，当时甚至被折磨到怀疑人生😵‍💫。

后来觉得可能是界面自动刷新导致重复执行，因为 Table 组件的`OnQueryAsync`回调函数初始化会自动执行一次。

当时还以为回调里不能写请求，写请求就会导致页面刷新，通过打断点和打开浏览器控制台看 DOM 变化，果然，到了 API 哪一行 DOM 刷新了，这也就说的通了我什么第二次执行的时候回调参数为 null 了，（页面都刷新了，回调参数可不得是 null）。

至于第二次执行的时候为何会正常进入 Controller。

我的理解是 如果回调参数是 null，在调用 API 的时候，他是无法判断我传的参数 是否和 API 接收 Model 参数类型一致的，而 Model 参数又是可选的`?`，自然就会正常进入 Controller，而接口参数又是不传的情况下返回全部数据，最后因为种种原因叠加在一起一直没能定位到问题，实在是太坑了。最后一步一步打断点，断点打到`httpClient`工具类内部才知道接口报错 400 了，报错信息提示某个字段类型问题，就因为接口报错后没有`显式`抛出这个错误信息，导致一个简单的类型 Bug 调试了整整一天，我真服了🤡🤡🤡

## 让人欣慰的地方🥱

虽然 Blazor 的 code 部分还是用 C# 编写，但好在模版那里还是与前端大部分框架有几分相同的(互相抄了属于是)，比如也可以父子传值、双向绑定、if/foreach、也可以通过 ref 去获取组件内部对外暴露的方法或者变量，甚至还有样式穿透、空值合并运算符 `??`，可选符 `!?`，毕竟 TS 也是巨硬的会有这个也不奇怪🥴

## 最后😫

我还是想说就算不用 Vue，使用其他 JavaScript 框架也没有问题，我可以学习它们，因为它们都属于前端。然而，让我感到抗拒的是😕，我只是一个菜鸡前端开发，我缺乏 .NET 基础😣，而公司只给我一个月时间让我用 C# Blazor 去开发并上线一个版本。这搞得我非常烦躁😡，最关键的问题是公司里没有其他人会，只能自己来，这让我感到非常痛苦。这一个多月来基本天天加班😩，好在国庆前成功发了一个版本😥，后面我要狠狠地摸鱼补回来😤😤😤。
