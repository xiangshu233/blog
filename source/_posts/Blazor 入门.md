---
date: 2023-10-19
title: Blazor 入门笔记
categories: [笔记]
tags: [C#, Blazor]
references:
  - title: 'Blazor 文档'
    url: https://learn.microsoft.com/zh-cn/aspnet/core/blazor/?view=aspnetcore-7.0&WT.mc_id=dotnet-35129-website
---

## Blazor 介绍

Blazor 框架是一个用于构建单页应用程序的开源框架。它由 Microsoft 创建，将传统的 razor 框架与现代的 .Net 和 WebAssembly 框架相结合。更重要的是？它有助于构建服务器端和客户端应用程序。Blazor 一般有两种托管模型，一种用于客户端，另一种用于服务器端

### Blazor  WebAssembly（指客户端）

![Blazor WebAssembly runs .NET code in the browser with WebAssembly.](https://learn.microsoft.com/zh-cn/aspnet/core/blazor/index/_static/blazor-webassembly.png?view=aspnetcore-7.0)

Blazor WebAssembly 使用 WebAssembly 技术将 C# 代码编译成可在浏览器中运行的二进制文件，然后在客户端执行，这种模型可以提供更接近原生应用程序的性能和用户体验。这样，前端和后端的代码都可以使用 C# 来编写，实现了一种统一的开发语言和技术栈。这意味着应用程序的逻辑和 UI 都在客户端执行，而不需要与服务器进行实时通信，但需要考虑到文件大小和加载时间的影响。

Blazor WebAssembly 可以理解为一种单页应用程序（SPA）的实现方式，通过在客户端进行渲染和执行来提供交互性和响应性的用户体验，类似于使用前端框架（如Vue.js、React、Angular）实现客户端渲染的现代 Web 开发趋势。

优点：

• 这允许客户端站点在浏览器中运行应用程序。下载应用程序后，您可以断开服务器连接。但是，应用程序恢复工作，但无法与服务器交互以提取新数据。

• 这种托管模式的最佳之处在于它可以充分利用客户的能力和资源。

• 它不需要ASP.NET core Web 服务器来托管应用程序。

• 它通过缩短加载时间显着降低了服务器负载，因为它只需要在 DOM 中进行修改。

缺点：

• 此托管模型仅限于浏览器功能。
• .NET 标准兼容性及其调试存在限制。
• 需要兼容Wasm 的客户端软件和硬件。这意味着它仅支持最新的浏览器。

### Blazor  Server（指服务器端）

![Blazor Server runs .NET code on the server and interacts with the Document Object Model on the client over a SignalR connection](https://learn.microsoft.com/zh-cn/aspnet/core/blazor/index/_static/blazor-server.png?view=aspnetcore-7.0)

Blazor Server 使用 SignalR 技术在客户端和服务器之间建立实时的双向通信。在 Blazor Server 中，应用程序的 UI 是在服务器上渲染的，然后通过 SignalR 将更新的 UI 推送到客户端。这种模型可以减少客户端的资源消耗，但需要保持与服务器的实时连接，无法脱机使用。

Blazor  Server 是服务端渲染的一种实现方式。在现代 Web 开发中 NuxtJS 也是一种服务端渲染框架，

Nuxt.js 是一个基于 Vue.js 的框架，它使用 Node.js 在服务器上进行渲染，并生成静态的 HTML 文件。这种模型可以提供更好的性能和 SEO，但需要在服务器上进行渲染，并且不支持实时的双向通信。

JSP（JavaServer Pages）和传统的 Java 后端项目也是服务端渲染，在这种架构中，前端和后端的代码是紧密耦合的，前端页面（JSP）和后端逻辑（Java）在服务器端进行渲染和执行。

在传统的 JSP 和 Java 后端项目中，前端页面包含了动态的 Java 代码片段，这些代码片段会在服务器端被执行，并生成最终的 HTML 页面。然后，服务器将生成的 HTML 页面发送给客户端浏览器进行显示。

它们都是 **服务端渲染** 只是实现方式不同

优点：

• Blazor Web 应用程序可以更快地加载，因为服务器端会预先呈现 HTML 内容。
• 它可以有效地利用服务器的功能。
• 服务器端应用程序没有任何浏览器版本限制。因此，它甚至可以使用旧的浏览器。
• 服务器端托管模型的优点是它增强了安全性，因为它不会将应用程序代码传输到客户端。

缺点：

• 它要求与服务器的连接必须处于活动状态。如果没有互联网连接，Web 应用程序无法运行。
• 此托管模型需要ASP.NET core 服务器。
• 由于数据不断地往返于客户端服务器，因此它具有显着的延迟。

## 项目结构

本项目使用的 Blazor  Server 模式，所以只讲 Blazor  Server 目录结构

![image-20231020144058556](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets//2023/10/202312061738424.png)


`Program.cs`：应用的入口点，用于设置 ASP.NET Core [主机](https://learn.microsoft.com/zh-cn/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-7.0) 并包含应用的启动逻辑，其中包括服务注册和请求处理管道配置：

- 配置应用的请求处理管道：
  - 调用 [MapBlazorHub](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.builder.componentendpointroutebuilderextensions.mapblazorhub) 可以为与浏览器的实时连接设置终结点。 使用 [SignalR](https://learn.microsoft.com/zh-cn/aspnet/core/signalr/introduction?view=aspnetcore-7.0) 创建连接，该框架用于向应用添加实时 Web 功能。
  - 调用 [`MapFallbackToPage("/_Host")`](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.builder.razorpagesendpointroutebuilderextensions.mapfallbacktopage) 以设置应用的根页面 (`Pages/_Host.cshtml`) 并启用导航。

- `appsettings.json` 和环境应用设置文件：提供应用的[配置设置](https://learn.microsoft.com/zh-cn/aspnet/core/blazor/fundamentals/configuration?view=aspnetcore-7.0)。
- `_Imports.razor`：包括要包含在应用组件 (`.razor`) 中的常见 Razor 指令，如用于命名空间的 [`@using`](https://learn.microsoft.com/zh-cn/aspnet/core/mvc/views/razor?view=aspnetcore-7.0#using) 指令。
- `App.razor`：应用的根组件，用于使用 [Router](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.components.routing.router) 组件来设置客户端路由。 [Router](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.components.routing.router) 组件会截获浏览器导航并呈现与请求的地址匹配的页面。

- `wwwroot` 文件夹：应用的 [Web 根目录](https://learn.microsoft.com/zh-cn/aspnet/core/fundamentals/?view=aspnetcore-7.0#web-root)文件夹，其中包含应用的公共静态资产。

![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets//2023/10/202312061738352.png)

- `Shared` 文件夹：可以理解为前端的项目目录的 Layout 文件夹，里面放的是布局组件。

![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets//2023/10/202312061738223.png)

- `Tools` 文件夹：工具类写在这里以供 组件 随时调用。

- `Locales` 文件夹：存放国际化映射文件。

- `Properties` 文件夹：在 `launchSettings.json` 文件中保存[开发环境配置](https://learn.microsoft.com/zh-cn/aspnet/core/fundamentals/environments?view=aspnetcore-7.0#development-and-launchsettingsjson)。

- `Pages` 文件夹：包含 Blazor 应用的可路由 Razor 组件 (`.razor`) 和 Blazor Server 应用的根 Razor 页。 每个页面的路由都是使用 [`@page`](https://learn.microsoft.com/zh-cn/aspnet/core/mvc/views/razor?view=aspnetcore-7.0#page) 指令指定的。

![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets//2023/10/202312061738761.png)

  - `_Host.cshtml`：实现为 Razor 页面的应用根页面：
    - 最初请求应用的任何页面时，都会呈现此页面并在响应中返回。
    - 此主机页面指定根 `App` 组件 (`App.razor`) 的呈现位置。

- `Data` 文件夹：包含 `WeatherForecast` 类和 `WeatherForecastService` 的实现，它们向应用的 `FetchData` 组件提供示例天气数据，calss 和 service 放到这里面给 pages 组件提供数据，其中 class 的类型要和后端的返回 `Model Class` 数据类型一致才能正确接收数据

![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets//2023/10/202312061738212.png)

## 组件结构

Blazor应用基于组件。 Blazor 中的组件是指 UI 元素，例如页面、对话框或数据输入窗体。

组件类通常以 [Razor](https://learn.microsoft.com/zh-cn/aspnet/core/mvc/views/razor?view=aspnetcore-7.0) 标记页（文件扩展名为 `.razor`）的形式编写。 Blazor 中的组件正式称为 Razor 组件，非正式地称为 Blazor 组件。 Razor 是一种语法，用于将 HTML 标记与专为提高开发人员工作效率而设计的 C# 代码结合在一起。 借助 Razor，可使用 Visual Studio 中的 [IntelliSense](https://learn.microsoft.com/zh-cn/visualstudio/ide/using-intellisense) 编程支持在同一文件中的 HTML 标记与 C# 之间切换。

lazor 使用 UI 构成的自然 HTML 标记。 以下 Razor 标记演示了当用户选择按钮时递增计数器的组件。

写过前端的很容易理解下面的结构，上面写`template`，`@code{}`内写 C# 代码逻辑

```razor
<PageTitle>Counter</PageTitle>

<h1>Counter</h1>

<p role="status">Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

## 组件样式隔离

将 CSS 样式隔离到各个页面、视图和组件以减少或避免：

- 依赖难以维护的全局样式。
- 嵌套内容中的样式冲突。

若要定义组件特定的样式，请在相同文件夹中创建一个 `.razor.css` 文件，该文件与组件的 `.razor` 文件的名称相匹配。 `.razor.css` 文件是限定范围的 CSS 文件，和 vue 组件中的 `scope` 一个意思。

详情可看：
{% link https://learn.microsoft.com/zh-cn/aspnet/core/blazor/components/css-isolation?view=aspnetcore-7.0&WT.mc_id=DT-MVP-5005108#enable-css-isolation desc:true %}

```css
<style scoped>

</style>
```

![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets//2023/10/202312061738405.png)

## 样式穿透

一般用于对子组件应用样式更改

> 默认情况下，CSS 隔离仅应用于与 `{COMPONENT NAME}.razor.css` 格式关联的组件，其中占位符 `{COMPONENT NAME}` 通常是组件名称。 若要对子组件应用更改，请对父组件的 `.razor.css` 文件中的任何后代元素使用 `::deep`[pseudo-element](https://developer.mozilla.org/docs/Web/CSS/Pseudo-elements)。 `::deep` pseudo-element 会选择属于元素生成范围标识符后代的元素。

示例：更改 **Bootstrap Blazor** 的 `Card` 组件 默认 padding 值

![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets//2023/10/202312061738549.png)

![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets//2023/10/202312061738589.png)



![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets//2023/10/202312061739835.png)

可以看到 **card** 的 默认 **padding** 被覆盖成了我们自定义的 **padding**

## 组件的父子传值

写过 Vue 的应该很熟悉了，除了语法上的区别，其他的基本一样。默认情况下，组件的属性是私有的，这里需要注意子组件的 Value 属性要声明为 **public**，以便父组件能够传递数据给子组件

`Parent.razor`：

```razor
<div class="grayscale">
    @* 灰度图组件 *@
    <GrayscaleImages Value="@DefectTypeList" />
</div>

@code {
    [NotNull]
    private List<DefectTypeModel>? DefectTypeList { get; set; } = new()
    {
        new DefectTypeModel { Id = "1", ClassNumber = 1, NameCn = "缺陷类型1", NameEn = "Defect Type 1" },
        new DefectTypeModel { Id = "2", ClassNumber = 2, NameCn = "缺陷类型2", NameEn = "Defect Type 2" },
        new DefectTypeModel { Id = "3", ClassNumber = 3, NameCn = "缺陷类型3", NameEn = "Defect Type 3" }
	};
}
```

`Child.razor`：

```razor
@foreach (var item in Value)
{
    <div class="grayscale-item">
        <div class="grayscale-info">
            <div class="text">@item.NameCn</div>
        </div>
    </div>
}

@code {
    // props
    [Parameter]
    public List<DefectTypeModel> Value { get; set; } = new();
}
```

## 组件调用

只要父组件和子组件在同一级目录中就能自动识别，直接在父组件中调用子组件即可无需像 JS 那样手动 `import` 后再注册使用

## 字符串插值

和 es6 模板字符串不能说毫不相干简直一模一样

```c#
string apiUrl = $"api/RejudgeRecord/rejudgeRecord/list?startTime={startTime}&endTime={endTime}&timeGranularity={timeGranularity}";

string LogError = $"注册失败！错误信息：{errorMessage}"
```

## 动态样式

动态 class

```html
<div class="@GetClassByJudgeCode()"></div>
```

```c#
// 动态样式
private string GetClassByJudgeCode()
{
    switch (RejudgeCode)
    {
        case "1":
            return "color-ok";
        case "0":
            return "color-ng";
        default:
            return "";
    }
}
```

动态 style

```html
<div style="@SetFilterActiveButton("-3")"> -3 </div>
```

```c#
private string SetFilterActiveButton(string buttonValue)
{
    return ActiveButton == buttonValue ? "background-color: #2e96ff;" : "background-color: #1b6ec2;";
}
```

## HTML 表达式

blazor 组件文档：
{% link  https://learn.microsoft.com/zh-cn/dotnet/architecture/blazor-for-web-forms-developers/components desc:true %}


在Blazor中，`@` 符号用于表示 C# 代码的起始点。当您在Blazor组件中使用 `@` 符号时，它会告诉 Blazor 编译器将其后面的内容视为 C# 代码而不是普通的文本。Razor 通常很聪明，可猜出你何时切换回 HTML。 例如，以下组件使用当前时间呈现 `<p>` 标记：

隐式：`@`

```html
<p>@DateTime.Now</p>
```

显式：`@()`

```html
<p>@(DateTime.Now)</p>
```

对于复杂的表达式，涉及到了多个操作和方法调用。在这种情况下，为了明确告诉 Razor 引擎将其作为 C# 代码进行求值，您需要使用 `@()` 将整个表达式括起来。

将 value.Value 强转成 DateTime 类型再调用 ToString 格式化为 "yyyy-MM-dd HH:mm:ss" 格式

```html
<div>@(((DateTime)value.Value).ToString("yyyy-MM-dd HH:mm:ss"))</div>
```

## @if  ? else / @foreach 条件渲染

```html
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else
{
       <p>empty.</p>
}
```

```razor
<ul>
@foreach (var item in items)
{
    <li>@item.Text</li>
}
</ul>
```

## @bind 数据双向绑定

官方文档的双向绑定：
{% link https://learn.microsoft.com/zh-cn/dotnet/architecture/blazor-for-web-forms-developers/components#data-binding desc:true %}

看看官方文档示例：

![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets//2023/10/202312061739122.png)

再看看 Vue 的 v-model 官方文档示例：

![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets//2023/10/202312061739633.png)

不能说一模一样只能说 99 像了，所以无缝入门

### 自定义组件的双向绑定

可以先看看官网的示例：

子组件：

*PasswordBox.razor*

这是接收父组件传过来的值，和 vue 里 props 一个意思

```c#
[Parameter]
public string Password { get; set; }
```

将 `Password` 变量绑定在 input value 属性上，通过 `OnPasswordChanged` 事件每次输入都会把当前最新的值赋值给 `Password`

要想正确通知父组件更新需要声明一个 `EventCallback<T>`，用于组件触发事件并在值发生变化时通知父组件，在这种情况下，`PasswordChanged` 是一个事件回调，它提供了 `InvokeAsync()` 方法，用于调用事件回调并将更新后的 `Password` 值传递给父组件

```c#
[Parameter]
public EventCallback<string> PasswordChanged { get; set; }
```

注意：`EventCallback` 的命名一定要 `{Parameter}Change`，这是一种约定，否则不生效

```razor
Password: <input
    value="@Password"
    @oninput="OnPasswordChanged"
    type="@(showPassword ? "text" : "password")" />

<label><input type="checkbox" @bind="showPassword" />Show password</label>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();
        return PasswordChanged.InvokeAsync(Password);
    }
}
```

父组件：

*parent.razor*

通过使用 `@bind` 指令，它将 `PasswordBox` 组件内部 `Password` 属性 与父组件 `password` 变量进行双向绑定

这意味着当密码框的值发生变化时，`password` 变量的值也会更新，反之亦然

```razor
<PasswordBox @bind-Password="password" />

@code {
    string password;
}
```

在日常开发中封装组件一般会基于 UI 库的组件进行二次封装，这个时候该如何双向绑定，可以参考以下示例

子组件：

*CustomInput.razor* 组件基于 **Bootstrap Blazor** 的 `Select` 组件二次封装

代码的关键是 `_RejudgeCode` 字段，他重写了自己的 get set 方法，获取值执行 get 返回 `RejudgeCode` 字段值，set `_RejudgeCode` 字段值时，会将新的值赋给 `RejudgeCode` 字段，并通过 `RejudgeCodeChanged.InvokeAsync(RejudgeCode)` 来异步地触发 `RejudgeCodeChanged` 事件回调，通知父组件，`RejudgeCode` 的值已经发生了变化

```html
<div>
    <Select
            @bind-Value="@_RejudgeCode"
            Items="@SelectList">
    </Select>
</div>
```

```c#
/// <summary>
/// select Items
/// </summary>
[Parameter]
public IEnumerable<SelectedItem> SelectList { get; set; } = new[]
{
    new SelectedItem ("1", "OK"),
    new SelectedItem ("0", "NG") ,
};

/// <summary>
/// RejudgeCode 的值 是 1 或 0
/// </summary>
[Parameter]
public string RejudgeCode { get; set; } = "0";

/// <summary>
/// 定义一个 _RejudgeCode 私有字段
/// 这里必须定义一个私有字段，直接用 RejudgeCode get set 的话，set 方法就会变成 `RejudgeCode = value;`，
/// 这实际上是在调用 set 方法本身，因此，每次尝试设置属性值时都会再次调用 set 方法，导致无限递归调用，最终导致栈溢出错误。
/// 在 C# 中，当你使用属性的 get 和 set 方法时，你应该使用一个私有字段来存储属性的值，而不是直接访问属性本身。这样可以避免循环调用。
/// </summary>
private string _RejudgeCode
{
    get
    {
        return RejudgeCode;
    }
    set
    {
        RejudgeCode = value;
        RejudgeCodeChanged.InvokeAsync(RejudgeCode);
    }
}

/// <summary>
/// 定义与可绑定参数同名的事件，名称会添加“Changed”后缀。
/// </summary>
[Parameter]
public EventCallback<string> RejudgeCodeChanged { get; set; }
```

父组件：

*parent.razor*

父组件把表格 `Row.RejudgeCode` 字段传给 `CustomInput` 组件后，由于子组件内部的 select 组件绑定的是 `ComputedRejudgeCode`，它就会执行我们自定义的 `get` 方法并返回 `RejudgeCode` 给 select 组件，当执行 select change 事件的时候 `ComputedRejudgeCode` 值改变了就会执行自身的 set 方法，把最新的值赋值给 `RejudgeCode`，并把最新的 `RejudgeCode` 值传递给父组件

```html
<TableColumns>
    <TableColumn @bind-Field="@context.RejudgeCode">
        <EditTemplate Context="Row">
            <CustomInput @bind-RejudgeCode="@Row.RejudgeCode" />
        </EditTemplate>
    </TableColumn>
</TableColumns>
```

## @Ref 指令

{% link https://learn.microsoft.com/zh-cn/dotnet/architecture/blazor-for-web-forms-developers/components#capture-component-references desc:true %}

捕获对组件或 HTML 元素的引用 `<MyDialog @ref="myDialog" />`

```html
<MethodEdit @ref="@MethodEditRef"
            IsEdit="@IsMergeTableEdit"
            InitialEditMergeModel="@InitialEditMergeModel" />
```

```C#
/// <summary>
/// 表格编辑组件 的 ref
/// </summary>
[NotNull]
private MethodEdit MethodEditRef = default!;

void OnSomething()
{
    var EditMergeModel = MethodEditRef.EditMergeModel;
    JSRuntime.InvokeVoidAsync("console.log", "提交的表单数据", EditMergeModel);
}
```

子组件：

*MethodEdit.razor*

这样就可以调用 `MethodEdit` 组件内部的变量或者方法，注意：它们一定是 `public` 声明的否则父组件将无法调用

```c#
/// <summary>
/// 初始化新增、编辑弹窗的 EditMerge Model
/// 同时也是保存提交的数据
/// </summary>
public EditMergeModel EditMergeModel { get; set; } = new();
```

## 组件生命周期

{% link https://learn.microsoft.com/zh-cn/dotnet/architecture/blazor-for-web-forms-developers/components#component-lifecycle desc:true %}

Razor 组件也具有定义完善的生命周期。 组件的生命周期可用于初始化组件状态及实现高级组件行为

### OnInitialized

`OnInitialized` 和 `OnInitializedAsync` 方法用于初始化组件。 组件通常在首次呈现后初始化。 组件初始化后，可能会在最终释放前呈现多次。 `OnInitialized` 方法类似于 ASP.NET Web Forms 页和控件中的 `Page_Load` 事件。

```csharp
protected override void OnInitialized() { ... }
protected override async Task OnInitializedAsync() { await ... }
```

### OnParametersSet

当组件已从其父级接收参数并将值分配给属性时，将调用 `OnParametersSet` 和 `OnParametersSetAsync` 方法。 这些方法在组件初始化后以及每次呈现组件时执行。

```csharp
protected override void OnParametersSet() { ... }
protected override async Task OnParametersSetAsync() { await ... }
```

### OnAfterRender

`OnAfterRender` 和 `OnAfterRenderAsync` 方法在组件完成呈现后调用。 此时，将填充元素和组件引用（在下文中详细介绍这些概念）。 此时已启用与浏览器的交互性功能。 与 DOM 和 JavaScript 执行的交互可以安全地进行。

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

在服务器上进行预呈现时，不调用 `OnAfterRender` 和 `OnAfterRenderAsync`。

`firstRender` 参数在首次呈现组件时为 `true`；否则，其值为 `false`。

### IDisposable

从 UI 中移除 Razor 组件时，该组件可以实现 `IDisposable` 来释放资源。 Razor 组件可以通过使用 `@implements` 指令来实现 `IDispose`：

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

## Blazor 中调用 Js

### 方法一

注入:

```c#
@inject IJSRuntime JSRuntime
```

使用之前要在 `_Layout.cshtml` body 内引入 js 文件，或者在  `<script> </script> ` 内定义好所需的 js 函数

使用：

`InvokeVoidAsync` 第一个参数是 `func name`，第二个参数是 `js func` 的参数

```c#
private void PositionWaferTableRow(string Id)
{
    JSRuntime.InvokeVoidAsync("setGrayscaleItemScrollView", Id);
    // 也可以用来调试打印
    JSRuntime.InvokeVoidAsync("console.log", "value is default", RejudgeCode);
}
```

### 方法二

参考以下文章：

{% link https://www.cnblogs.com/codecopier/p/17609183.html desc:true %}


## 读取配置文件

{% link https://learn.microsoft.com/zh-cn/dotnet/architecture/blazor-for-web-forms-developers/config#read-configuration-in-the-app desc:true %}

有时候我们需要将有些变量放置到 `appsettings.json` 配置文件中，我们该如何在组件里访问配置文件里的变量

注入`IConfiguration` 对象：

```c#
@inject IConfiguration Configuration
```

上面的语句使 `IConfiguration` 对象在 Razor 模板的其余部分中可作为 `Configuration` 变量提供。

### `string` 类型

```html
<div class="system-version">版本：@(version)</div>
```

```c#
protected override void OnInitialized()
{
    base.OnInitialized();
    version = Configuration["SystemVersion"];
}
```

```json
// appsettings.json
{
  // 版本号
  "SystemVersion": "1.0.0",
}
```

### `bool` 类型

```c#
var IsCheckLicense = Configuration.GetSection("IsCheckLicense").Get<bool>();
if (IsCheckLicense)
{
    // something...
}
```

```json
// appsettings.json
{
  // 是否检查 License
  "IsCheckLicense": false,
}
```

### 数组对象类型

需要注意 `List<SelectedItem>` 类型问题

```c#
private IEnumerable<SelectedItem> MethodItems { get; set; } = Enumerable.Empty<SelectedItem>();

protected override void OnInitialized()
{
    base.OnInitialized();

    MethodItems = Configuration.GetSection("MethodItems").Get<List<SelectedItem>>();
}
```

```json
// appsettings.json
{
    // 算法名称
    "MethodItems": [
        {
            "value": "0",
            "text": "FindMerge"
        },
        {
            "value": "1",
            "text": "Elliptic"
        },
    ],
}
```

## 总结

没有在写 vue，可处处都在写 vue
