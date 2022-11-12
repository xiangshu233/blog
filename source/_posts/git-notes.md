---
date: 2021-05-13
title: git 命令笔记
categories: [Git]
tags: [git]
cover: https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@a187fb9a519bdde5d5452e86e135049489413ec4/2022/02/15/66ff7049390b4d75e7a67af4a00f3ae7.png
references:
  - title: 廖雪峰的官方网站 Git 教程
    url: https://www.liaoxuefeng.com/wiki/896043488029600
---

## 本地提交到服务器

查看本地状态

```Bash
git status
```

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@fb2ed21a2d24ffabbbe845cc8197afa4b47eeb2d/2021/05/10/c14ef3043c3642c3e712846df8e07e0f.png %}

`git add`把文件添加进去，实际上就是把文件修改添加到暂存区

```Bash
git add .		# 提交所有
git add src/	# 提交src下的文件
```

`git commit` 提交更改，实际上就是把暂存区的所有内容提交到当前分支

```Bash
git commit -m "测试提交"
```

`git push`把文件推送到远程库

```Bash
git push
```



## 版本回退 git reset

Git 中HEAD表示当前版本，上一个版本就是 `HEAD^`，上上一个版本就是 `HEAD^^`，当然往上100个版本写100个 `^` 比较容易数不过来，所以写成 `HEAD~100`。

```Bash
git reset --hard HEAD^
```

- `HEAD` 指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令 `git reset --hard commit_id`。

- 穿梭前，用 `git log` 可以查看提交历史，以便确定要回退到哪个版本。

- 要重返未来，用 `git reflog` 查看命令历史，以便确定要回到未来的哪个版本。


{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@0a451f5dc702015d3c8a03fdd7e8b569b2d02277/2021/05/10/6034c87cc7489bf58d5f3ee29af15b6c.png %}

## 查看提交日志 git log

git log命令显示从最近到最远的提交日志

退出 ：q

```Bash
git log
```

如果嫌输出信息太多，看得眼花缭乱的，可以试试加上 `--pretty=oneline` 参数

```Bash
git log --pretty=oneline
```

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@18ce65fb996b4067faeaeac6d8408981053efd7f/2021/05/10/55c8e0207b14dbe93343136518d93043.png %}

## 工作区和暂存区的概念

### 工作区

电脑里能看到的目录为工作区

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@4bebaf48efceeff39ccefe81003845736a631a70/2021/05/11/9e67cb4772432efe0f34ed593ceb1449.png %}


### 暂存区

工作区里有一个隐藏目录 `.git`，这个就是Git的版本库。

其中最重要的就是称为 `stage`（或者叫做 `index` ）的暂存区，还有Git为我们自动创建的第一个 `master`，以及指向 `master` 的一个指针叫 `HEAD`。

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@daa44989205340c615bd6264efc2ddaef6dc7755/2021/05/11/cb15dac9cc725368fb3f375b7eff5914.png %}

往版本库修改（或添加）文件的时候分两步执行

- `git add` 把文件添加进去，实际上就是把文件修改添加到暂存区。
- `git commit` 提交更改，实际上就是把暂存区的所有内容提交到当前分支。

一旦提交后，如果你又没有对工作区做任何修改，那么工作区就是“干净”的：

`nothing to commit, working tree clean`


{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@eabefd5c8fcc87c32645bce17c7eed1e79081a52/2021/05/11/4cac5264eaee092b42ccc86761b33490.png %}

## 工作区与版本库比较 git diff

`git diff` 是工作区跟仓库（仓库也叫版本库）的比较，这个时候可以看你开发过程中修改了那些内容

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@35479912f446cd459d7908daf73cd9c33123d177/2021/05/11/ce742eb4e83aa170ad698fd6f3f0fdbe.png %}

 `git diff --cached` 是看你 `stage` （暂存）区和仓库（版本库）分支上的比较，你 `add` 后但是没有 `commit`， 这个时候只是在 `stage` 中，可以确认下修改是否正确，如果正确无误可以 `commit` 合并到分支

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@032a35c9ba1162fb3987a72837cbd9f532397379/2021/05/11/c2cb893cd79a8334b343bfda493a28d1.png %}

> 为什么Git比其他版本控制系统设计得优秀，因为Git跟踪并管理的是修改，而非文件。
>
> 你会问，什么是修改？比如你新增了一行，这就是一个修改，删除了一行，也是一个修改，更改了某些字符，也是一个修改，删了一些又加了一些，也是一个修改，甚至创建一个新文件，也算一个修改。

## 撤销更改 git restore

每次修改（新增）文件后都要 `git add` 到暂存区，否则就不会加入到 `commit` 中

如果文件已经 `git add` 到暂存区了，但是没有 `commit` **提交**：

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@b56948e64c95c694e225035d58bd115780333079/2021/05/11/2a0e288643c9ccb36f3a58e68f11e243.png %}

可以使用 `git restore --staged<file>` 将文件从**暂存区**撤出，**但是不会撤销工作区文件的更改**

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@e964fc74a0f4768b83f653eeaeacdbfaa27c6dbc/2021/05/11/005afe3b438562bff9d8957c9825a21f.png %}

使用 `git restore --staged config/dev.env.js` 命令后，测试提交可以看到只提交了两个文件，因为 `commit` 只会提交暂存区里的文件。

`config/dev.env.js` 虽然撤出了暂存区，但是工作区文件的更改并没有撤销，可以 `git status` 查看


{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@750e231f9e123c05471959a265f2e8f6292a5853/2021/05/11/6863b5839b0bbf61f1c4b60b27759b58.png %}

同样Git给出了提示可以使用 `git restore <file>` 将不在暂存区的文件撤销更改（即：`git status` 提示的被修改但未被加入暂存区的内容，会被撤销）。



{% note color:yellow 这里有个区别，工作区修改了但是未加入暂存区的使用 `git status` 查看文件状态是红色的，加入暂存区未提交的状态是绿色的 %}

```Bash
git restore config/dev.env.js
```

工作区的修改被撤销，`working tree clean`

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@092074c3a971ab29543630561d207bf2c754fd4f/2021/05/11/c495e47d0740d1713d14c1aa980e9a42.png %}


提示：如果修改的文件已经提交到版本库（但是没有 `push` 到远程库可以参考**版本回退**）

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@bc795a75a352c75bd87a3169442b11876d5af23e/2021/05/11/484454eafb8f2a079310f2e9d86a1b0f.png %}


版本已经回退至上个版本

## 删除文件 git rm

在Git中，删除也是一个修改操作，新工作区中新建一个 `test.txt` 添加到暂存区并且提交：

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@baab273371be757e009b5d13ebe2d30f0ac60192/2021/05/11/f4fe525fb959ac9c7f8ae890cba50955.png %}

一般情况下在工作区你直接把没用的文件删除了或者使用`rm`命令删了：

```Bash
rm test.txt
```

这个时候，Git知道你删除了文件，因此，工作区和版本库就不一致了，`git status` 命令会立刻告诉你哪些文件被删除了：

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@8a7caa2eebab91126b2e5078c7ae329716857583/2021/05/11/803a0bb34c214f7779431916a07ad56f.png %}

同样Git给出提示，你有两个选择，一确实要从版本库中删除该文件，使用 `git rm` 删除，并且 `git commit`

或者使用 `git restore <file>` 撤销更改

```Bash
git rm test.txt

git commit -m "删除测试文件"
```

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@6cab583427f623e4e99231007f55addaf8f77a93/2021/05/11/27bce3b62695baaeacf03e57d141f56b.png %}

现在文件就从版本库中删除了 `git diff` 没有返回信息则表示工作区和版本库一致。

## 分支管理

### 查看分支

```Bash
git branch
```

### 创建分支

创建 `dev` 分支，并切换到 `dev` 分支上

```Bash
git checkout -b dev
# 新版本使用 switch 切换

# 创建并切换分支
git switch -b dev
```

`git checkout` 命令加上 `-b` 参数表示创建并切换，相当于以下两条命令：

```Bash
$ git branch dev		# 创建分支

$ git checkout dev		# 切换分支

Switched to branch 'dev'
```

### 合并分支

例：将 `dev` 分支的内容合并到 `master` 分支

首先切换到 `master` 分支

```Bash
git checkout master
```

使用 `git merge` 将 `dev` 分支的内容合并到当前（master）分支

如果多人开发的话需要 `git pull` 一下

```Bash
git merge dev
```

### 删除分支

删除本地分支

```Bash
git branch -d dev
```

删除远程分支

```bahs
git branch -r -d origin/branch-name
```

### 拉取远程新分支

如果远程新建了一个分支，本地没有该分支。

可以利用 `git checkout --track origin/branch_name` ，这时本地会新建一个分支名叫 `branch_name` ，会自动跟踪远程的同名分支 `branch_name`。

```Bash
git checkout --track origin/branch_name
```

### 本地新分支推送到远程库

如果本地新建了一个分支 `branch_name`，但是在远程没有。

这时候 `push` 和 `pull` 指令就无法确定该跟踪谁，一般来说我们都会使其跟踪远程同名分支，所以可以利用 `git push --set-upstream origin branch_name`，这样就可以自动在远程创建一个 `branch_name` 分支，然后本地分支会（跟踪）`track` 该分支。后面再对该分支使用 `push` 和 `pull` 就自动同步。

```Bash
git push --set-upstream origin branch_name
```

## 场景1：新建本地仓库，需要关联远程仓库

```Bash
git init
```

关联远程远程仓库 `git remote add origin XXXX`

```Bash
git remote add origin http://192.168.1.66:8088/robot_admin_system/browser_server/uo_vue_frontend.git
```

关联后使用 `git remote -v` 来查看是否关联成功

```Bash
 git remote -v
```

同步远程仓库到本地

此时 `pull` 下来的是远程库的所有分支

```Bash
git pull
```

使用 `git branch` 并不会显示分支，其实已经下载下来了，需要手动 `checkout` 到分支

```Bash
git checkout master
```

再 `git branch` 就显示本地分支了

```Bash
git branch
```

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@73765caaaf298350788dd11c5319ef9f2a2b8994/2021/05/11/b8e907c81fbd26f7e4f80177c73db338.png %}

显示远程分支

```Bash
git branch -a
```

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@4e76c29d2426ebabad19a9e4677c2694e7c0701a/2021/05/12/0726eda976c29fa1a7993efe958ce7d1.png %}

## 场景2：切换关联的远程仓库

删除远程关联

```Bash
git remote rm origin
```

一旦你使用这种方式删除了一个远程仓库，那么所有和这个远程仓库相关的远程跟踪分支以及配置信息也会一起被删除。

再查看远程分支没有则删除关联成功

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@1b87c2de62736f34a67016c4a6b998186d3d807b/2021/05/12/cb1f66d7ac27f1cc96ba310c13625f47.png %}


继续场景一第一步即可

```Bash
git remote add origin xxxxx
```


后续补充中。。。


