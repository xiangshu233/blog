---
title: git 修改上次 commit 描述
date: 2022-10-23
categories: [Git]
tags: [git]
cover: https://link.jscdn.cn/1drv/aHR0cHM6Ly8xZHJ2Lm1zL3UvcyFBaFhWN0U3bHBTaWtsbnZhUHhHWGExX1VLTTN3P2U9clB0eWtZ.jpg
banner: https://link.jscdn.cn/1drv/aHR0cHM6Ly8xZHJ2Lm1zL3UvcyFBaFhWN0U3bHBTaWtsbnZhUHhHWGExX1VLTTN3P2U9clB0eWtZ.jpg
references:
  - title: '修改旧提交'
    url: https://stackoverflow.com/questions/17338792/amending-old-commit
  - title: '详解git commit --amend 用法'
    url: https://www.jb51.net/article/192486.htm
---

今天在提交代码的时候 commit 描述里多了一个空格，强迫症患者表示不能忍受

![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2022/10/commit.png)

> 有时你提交过代码之后，发现一个地方改错了，你下次提交时不想保留上一次的记录；或者你上一次的commit message的描述有误，这时候你可以使用这个命令：
>
> ```bash
> git commit --amend
> ```



但是，如果你想修改上上次，甚至上上上次

你需要先使用这个命令

```bash
git rebase -i HEAD~2
```

接上图，其实我这个提交本来是上次的，结果我在提交 `本次提交` 之前，在 `github` 上直接修改过 `README.md`，然后我在 `git push` 后顺便 `git pull`（同步更改）了，就导致我的这个提交变成上上次了。

所以我需要先使用这个命令 `git rebase -i HEAD~2`

如果是上上上次 后面数字以此类推

之后你会看到这个画面

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2022/10/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20221022225145.png %}

上面是显示你最近的两次提交记录，找到你需要的那个 `commit`，进入编辑模式 `i` 把前面的 `pick` 改成 `edit`，最后 `wq` 保存

现在你再执行 `git commit --amend` 修改，同样是进入 `vim`，进入编辑模式 `i`  改好后 `wq` 保存

然后执行 `git rebase --continue` 你会得到如下提示，表示你操作成功

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2022/10/edit-commit-successfully.png %}

然后现在 `git log` 一下，会发现你已经修改成功

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2022/10/gitlog.png %}

最后一步，强制推送到远程库 `git push -f`

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2022/10/QQ%E6%88%AA%E5%9B%BE20221022231225.png %}

推送成功！


然后进入github远程库查看：

已经修改了提交描述信息，且原来的 `git` 版本没有了

但是有个地方要注意，就是该操作会改变你原来的 `commit id`

![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets/2022/10/QsdfsdsfdsJQXW.png)

{% note color:yellow 警告 这个变基操作在多人开发下一定要谨慎使用 %}
