---
title: 代码整洁之道：实践指南 P1
tags: [代码规范]
categories: [JS]
date: 2023-03-25 19:20:00
cover: https://link.jscdn.cn/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvcyFBaFhWN0U3bHBTaWtsd2N0YURBRFNXd3Zqd0RsP2U9RGlvMVkx.jpg
banner: https://link.jscdn.cn/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvcyFBaFhWN0U3bHBTaWtsd2N0YURBRFNXd3Zqd0RsP2U9RGlvMVkx.jpg
---

## 前言

> “阅读这本书有两种原因：第一，你是个程序员；第二，你想成为更好的程序员。很好，IT行业需要更好的程序员！”——罗伯特·C. 马丁（Robert C. Martin）

冲浪时看到的一篇文章，觉得写的很不错。翻译下放到自己博客上，顺便学习一下这本书的思想😁

以下是《代码整洁之道》中文翻译版的一个在线仓库，可以直接看
{% link https://github.com/xiangshu233/Clean-Code-zh %}

## 什么是干净的代码？👁‍🗨

作为开发人员，我们的工作主要是编写代码💻、review、重构和 writing again。大多数情况下，当我们开始一个新项目时，我们往往会在脑海中构思其特性、布局、功能以及实现方法。随后，我们会详细设计并开始编写代码来实现这些想法。由于每个项目都需要反复修改和完善，我们必须不断检查代码，并根据需求和情况进行调整。但是，如果我们花费大量时间寻找正确的位置来插入新代码或进行修改，那么可能是因为我们对代码不够熟悉，或者代码编写不正确。

如果编写的代码需要花费很长时间才能展示出其功能和特性，那么这些代码通常不是干净的代码🤢🤮。这些代码可能没有得到正确的缩进格式，变量、函数、方法、类的命名也可能不够规范，导致代码难以理解和维护。因此，我们应该编写易于阅读和理解的代码，使用清晰的变量、函数、方法和类名称，并保证正确的缩进格式，以便使代码更加易于维护和修改。

另外，干净的代码指的是如何编写代码，而不是如何放置或维护代码。这是由代码架构来处理整个项目及其功能。干净的代码只涉及编写易于阅读的代码，它最终有助于代码的维护。它专注于解决手头的单一问题。因此，编写干净的代码，需要着重保证代码易于阅读、理解和维护，以便让代码更加具备可读性、可维护性和可扩展性。

## 代码应该如何编写？🙄

代码应该易于阅读、易于理解和易于维护🥱。干净的代码通常具备以下特点：

1. 代码应该有适当的缩进。那些逐行书写的代码不仅让人感觉乏味，而且很难集中精力关注它们的功能🧐。
2. 干净的代码并不意味着过长的名称和冗余的占位符和变量。这会使代码更加笨重和难以维护。干净的代码意味着适当的命名，能够传达它们的功能和存在。
3. 代码应该遵循所使用语言的***命名约定、最佳实践和模式***。这显示出对项目的一致性和适当的理解。
4. 它应该***降低认知负担***😫。它不需要花费时间来表达它所在的位置和用途。
5. 它应该***简洁明了***，直击要害🏹🎯。
6. 代码应该***避免使用不直观的名称***，复杂的嵌套和大代码块。
7. ***编写***代码🤓和维护代码应该是很有趣的。

最后，代码应该像一个故事，你是它的作者😍。

## 我们在哪些方面最容易搞砸？🤮

大多数程序员在遵循干净的代码规范时容易出现以下问题：

1. ***变量、函数***和类的命名；
2. 代码结构和注释：***代码格式***、糟糕的注释等；
3. 在声明***函数***时，涉及到其范围和参数的问题；
4. ***条件语句和错误处理***：深度嵌套，缺少适当的错误处理；
5. 在使用***类和数据结构时***。

然而，干净的代码并不需要强类型分配，因为这些对于了解我们使用的变量类型来说是微不足道的事情。有些语言在声明变量之前需要进行强类型化，如 ***Java***。在某些地方，我们可以注释输出-响应类型。我们还可以通过为它们指定适当的名称将这些类型存储在变量中，例如，如果它是一个布尔值，我们可以为它命名为 ***isShow***、***response***🆗等。好的，我们以后再深入讨论这个问题，现在我们主要关注干净的代码。

## 干净代码的助手🤝

由于代码不是死的，而是一直在运行中，因此没有开发人员可以编写最优和最干净的代码。我们必须维护它，并根据需求不断进行更改。已经多次阅读代码的资深开发者应该或必须知道如何使这些代码变得干净。

编写干净的代码是一种艺术💃，任何人都可以通过实践💪和信念掌握它。通过保持代码格式的一致性和编写干净的代码，这将成为你的创造性工作，并且会一直伴随着你。初学者们会追随你的脚步，而你的同事们会赞赏👏你编写干净代码的技巧。

由于实习生👦在编写代码时没有遵循干净代码的良好实践，因此应由资深开发者👨‍💼对他们进行指导。如果资深开发者不知道这些干净代码的良好实践，那么他们应该开始通过阅读相关博客来接受这些实践。😎

## 为什么干净的代码很重要？👩🏻‍⚖️

编程不是一次性的操作，而更像是反复进行的网球回击⛹🏻‍♂️。因此，如果你想打出一个成功的回击，就必须确保它是最佳的回击🏌🏻‍♀️。为此，你必须使代码易于阅读、易于维护和易于理解。

干净的代码实践需要时间，但一旦在编程生涯中接受了这种实践，它将成为未来的助手。你将不会被难以辨认和无法理解的代码🥶困扰，在修复错误或甚至破碎的生产代码时，那时候比开发过程中花费的时间更加关键。

因此，我们应该专注于编写干净的代码，并通过实践，可以在规定时间内快速交付项目。干净代码与快速代码之间的重要性也源自以下图表。

![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/3/clean-code.avif)

> "Dry Code" 是 “Don't Repeat Yourself” 的缩写，意思是 “不要重复自己”，它表示在编写代码时尽量避免冗余，将逻辑和功能分离，使代码更加简洁、可读性更高、易于维护。
>
> “Dry Code”  主要关注代码是否能够实现功能，而 “Clean Code” 则注重代码的可读性和可维护性。编写易于维护且更易于修改的代码有助于提高生产效率并使代码更具弹性。

这张图展示了通过采用 ”Dry Code” 方式编写代码并且只是为了快速交付生产环境，最终会因为高昂的维护成本在未来消亡😥（换句话说，如果编写代码时只考虑能否运行，而不考虑代码的可读性和可维护性，最终代码将难以维护并且很可能被淘汰）。相比之下，易于维护且易于交付的 “Clean Code” 将是更具生产力和开放性变化的选择。如果我们将生产力作为X轴，新的图表📈📉也显示出了 “Clean Code” 和 “Quick Code” 的同样行为。

“Clean Code” 并不主张延迟项目交付，而是鼓励编写易于维护且更易于修改的代码🧞‍♂️。当你给上级汇报时，不要说你正在编写一份需要很长时间⌛才能完成的 “Clean Code”，而是应该保证代码整洁并及时交付，这会让上级感到满意💋。那些在创作过程中令人愉悦的和具有吸引力的事物并不需要太长时间。当你像讲故事一样编写代码时，你就可以按时交付了。只要提高你的叙述能力即可。

## 原文链接

{% link https://rahul4dev.hashnode.dev/clean-code-good-practices-p1 %}