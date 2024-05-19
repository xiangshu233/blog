---
date: 2023-12-07
title: 美化你的 PowerShell
categories: [笔记]
tags: [PowerShell]
cover: https://cdn.xiangshu233.cn/post/banner/wallhaven-4d7lj4.jpg
references:
  - title: 'Oh My Posh 文档'
    url: https://ohmyposh.dev/docs/installation/windows
---


{% note color:blue 提示

本文针对 Win11 操作系统的 PowerShell。其实文档已经写的很清楚了，本文是为了方便萌新快速上手，如果你对文档已经很熟悉了，那么这篇文章可能对你来说没什么用。

%}

## 安装

打开 [Oh My Posh](https://ohmyposh.dev/)，找到 Docs 页，选择 Windows 菜单

![image-20231206205518857](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/12/202312062055932.png)

打开 PowerShell 终端并运行以下命令：

```shell
winget install JanDeDobbeleer.OhMyPosh -s winget
```

这会安装一些东西：

- `oh-my-posh.exe` - Windows 可执行文件
- `themes` - 最新的 Oh My Posh 主题

安装完成后重启终端

## 安装字体

打开 Fonts 菜单。

![image-20231206213235874](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/12/202312062132908.png)

为什么需要 Nerd 字体？因为 Oh My Posh 的主题里用的 Icons 都是需要 Nerd 字体支持，否则会显示乱码

访问 https://www.nerdfonts.com/ 可以看到Nerd 字体支持众多 Icons

![image-20231206211148519](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/12/202312062111594.png)



在 PowerShell 运行以下命令：

```shell
oh-my-posh font install --user
```

{% note color:yellow 注意

这里不加 --user 的话就用管理员身份打开 PowerShell 执行这条命令，这个命令是一个 Nerd 字体的 CLI

%}

![image-20231206213752944](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/12/202312062137973.png)

这里我选择安装 FiraCode 字体，想安装其他字体请自行选择

如果觉得上面命令 CLI 没有体现字体具体样式可以进入 [Downloads](https://www.nerdfonts.com/font-downloads) 页面自行安装各种 Nerd Font，看自己喜好，下载好后解压安装即可

![image-20231206214054076](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/12/202312062140131.png)

{% note color:yellow 注意：

安装完字体后要在终端中启用选项 `Use the new text renderer ("AtlasEngine")`

%}

![image-20231206214151380](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/12/202312062141410.png)

打开终端的设置- 呈现 - 打开 - 保存

![image-20231206214315946](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/12/202312062143981.png)

字体安装完要配置后才能使用：在终端上按住（快捷方式： `CTRL + SHIFT + ,` ）会自动打开 `settings.json` 文件，在 `profiles` 中的 `defaults` 属性下添加 `font.face` 属性：这里指定你安装的字体即可

![image-20231206214453443](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/12/202312062144473.png)

具体可看：https://ohmyposh.dev/docs/installation/fonts#configuration

## 设置默认的 Prompt

https://ohmyposh.dev/docs/installation/prompt

> 如果您不知道当前使用的是哪个 shell，Oh My Posh 有一个实用程序开关可以告诉您这一点。
>
> 终端执行这个命令：
>
> ```shell
> oh-my-posh get shell
> ```

![image-20231207000132031](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/12/202312070001046.png)

我这里是 PowerShell 我就按照 PowerShell 的文档来

编辑 PowerShell 配置文件脚本

```shell
notepad $PROFILE
```

当上述命令出现错误时，请确保首先创建配置文件

```shell
New-Item -Path $PROFILE -Type File -Force
```

{% note color:yellow 警告

在这种情况下，PowerShell 也可能会阻止运行本地脚本。要解决此问题，请将 PowerShell 设置为仅要求使用 :

```shell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope LocalMachine
```

签署远程脚本，或签署配置文件。

以管理员身份执行 PowerShell  终端 输入上述命令，选择 Y 即可

![image-20231207000505184](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/12/202312070005207.png)

%}

然后再执行

```shell
notepad $PROFILE
```

会打开一个 `PowerShell_profile.ps1` 一个文本文件

添加以下行

```shell
oh-my-posh init pwsh | Invoke-Expression
```

添加后，重新加载以使更改生效。

```shell
. $PROFILE
```

再次打开 PowerShell 终端就会显示默认的主题终端了

![image-20231207000752929](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/12/202312070007951.png)

## 更换 PowerShell  Theme

https://ohmyposh.dev/docs/themes

在 PowerShell，中可以使用以下 PowerShell cmdlet 显示每个可用主题。

```shell
Get-PoshThemes
```

也可以直接在 [Theme](https://ohmyposh.dev/docs/themes) 找到自己想要的主题后，复制主题名字，这里我选择的是 `neko` 主题

![image-20231207001003920](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/12/202312070010950.png)

打开文档的 [Customize](https://ohmyposh.dev/docs/installation/customize) 菜单项，这里是设置配置/主题 脚本文件

![image-20231207001458467](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/12/202312070014506.png)

在终端中执行以下命令，打开的就是刚才的 `PowerShell_profile.ps1` 脚本文件

```shell
notepad $PROFILE
```

里面默认内容是 `oh-my-posh init pwsh | Invoke-Expression`

![image-20231207001642867](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/12/202312070016893.png)

根据文档说明要设置新的配置/主题，您需要更改 `profile` 或 `.<shell>rc` 脚本中 `oh-my-posh init <shell>` 行的 `--config` 选项，并将其指向预定义主题或自定义配置的位置。他处理两种可能得值一个是本地配置文件路径和远程配置URL，这里选择远程的配置即可，以下内容覆盖到配置文件中去

```shell
oh-my-posh init pwsh --config 'https://raw.githubusercontent.com/JanDeDobbeleer/oh-my-posh/main/themes/neko.omp.json' | Invoke-Expression
```

如果要更换其他主题直接替换圈中的 `Json` 名即可

![image-20231207001750057](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/12/202312070017081.png)

更改后，重新加载你的个人资料以使更改生效。

```shell
. $PROFILE
```

再次打开终端可以看到主题更新了

![image-20231207001822231](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2023/12/202312070018251.png)
