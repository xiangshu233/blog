---
date: 2020-10-14
updated: 2022-10-23 20:25
title: hexo 博客部署到云服务器
categories: [博客]
tags: [博客部署]
cover: https://cdn.xiangshu233.cn/post/banner/wallhaven-gjp17e.jpg
---

{% note color:green 🎉🎉🎉 2022-10-23参照本文重新部署测试后，成功访问，本文仍具有参考价值。 %}

## 前提条件

你已经在本地搭建好了 Hexo 所需要的环境 `Git、Node.js、hexo` 且已经在本地可以运行 `Hexo` 静态网站，`Git` 推荐 [gitbash](https://gitforwindows.org/) 代替 `cmd`

## 本地机器配置

在本地机器任意目录右键 `git bash` 打开命令窗口使用 `ssh` 生成公钥

```Bash
# 生成密钥命令
ssh-keygen -t rsa
```

然后连按三次回车出现如下界面即代表生成成功，默认生成位置在你的用户文件夹里的 `.ssh` 文件夹里

![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@36d08c6d93c884d832493ff3c41a952cd9473b5d/2020/10/14/d0ebae7f9fadfb31df7c3b064f99f503.png)

![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@a993916f21a254da5024c7c5297a11b19dc6dc87/2020/10/14/c211105afd0d502921ebc6dcc1dbca7f.png)

我的是部署在 `github` 上时生成的密钥，这里就不再重复生成，就用以前的公钥。

在本地服务器中配置好了公钥，接下来我们需要把这个公钥交给服务器，相当于本地机器有了一把能访问服务器的 `钥匙`，所以接下来需要配置服务器

## 服务器选择

阿里云学生机有两个配置，我买的是 `轻量应用服务器` 具体啥区别我也不知道

![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@92a964a7462fd2e4e1b04bfc9335ae122d98415d/2020/10/14/45979cbef4af7c60508007da012ce6a4.png)

{%note color:yellow 轻量应用服务器是不要添加安全组规则的，默认开启状态，ECS则需要手动添加安全组规则授权`80`端口，否则无法访问服务器 %}

域名这块就略过了，网上一堆，有时间单独写域名

## 服务器重置及宝塔面板配置、Nginx配置

### 宝塔面板配置

服务器重置为 `CentOS7.3`，`SSH` 连接到服务器输入以下命令切换 `root` 用户并修改 `root` 密码
```Shell
sudo su root
passwd
```
输入以下命令安装宝塔（大约两分钟）
```Shell
yum install -y wget && wget -O install.sh http://download.bt.cn/install/install_6.0.sh && sh install.sh
```
安装完毕会提示你 `临时访问地址以及账户密码`，在浏览器地址栏输入 `http://你的ip:8888` 登录后找到 `面板设置` 里面进行自定义设置，由于涉及服务器安全这里就不放图了

![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@70ea2196dcec567711ff580c8a8fb20f2d7ff19a/2020/10/14/79cd8118f2049c56a04d0000f39c193c.png)

`进入面板` --> `网站`  --> `添加站点` --> `提交`

如下图操作这样你的站点就创建好了

![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@67245b4c92ccf464c5cb9cbf2cf342bfc09bfc82/2020/10/14/a7a14b77ccc8eaa1c458435ab0c4eebf.png)

### Nginx配置
找到宝塔 `软件商店`  --> 安装 `Nginx`，安装完毕后修改 `配置文件`
```Nginx
server
    {
        listen 80;						# 监听端口号（服务器请开启此端口）
        server_name xiangshu233.cn;				# 域名
        index index.html index.htm index.php;			# 入口文件
        root  /www/wwwroot/hexo_blog;				# 博客存放路径

        #error_page   404   /404.html;
        include enable-php.conf;

        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
        {
            expires      30d;
        }

        location ~ .*\.(js|css)?$
        {
            expires      12h;
        }

        location ~ /\.
        {
            deny all;
        }

        access_log  /www/wwwlogs/access.log;
    }
```

在你的博客目录下新建 `index.html` 文件添加如下代码测试 `Nginx`
```html
<!DOCTYPE html>
<html>
  <head>
    <title></title>
    <meta charset="UTF-8">
  </head>
  <body>
    <p>Hello world!</p>
  </body>
</html>
```
访问你的 `域名或者ip` 如果出现以下界面则表示 `Nginx` 配置成功

![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@516b4fe11ba1b6283af874bea0fca4d8f1dec6c7/2020/10/14/bfcf68c4ecaf28ad228545cf72211fe1.png)

## 服务器Git配置

安装 Git
```Bash
yum install -y git
```

创建 `Git` 用户，修改 `sudoers` 文件为 `740`（修改文件内部权限）
```Bash
adduser git
chmod 740 /etc/sudoers
vim /etc/sudoers
```

找到  `root ALL=(ALL) ALL` 在下面添加一行  `wq!` 保存
```Bash
git  ALL=(ALL)  ALL
```
 修改后保存再改回 `400`
```Bash
chmod 400 /etc/sudoers
```
设置 `git` 用户密码

```Bash
sudo passwd git
```

## 配置SSH

在本地机器找到你的公钥`（id_rsa.pub）`，复制公钥内容

切换 `git` 用户，创建  `~/.ssh` 文件夹和  `~/.ssh/authorized_keys`  文件，将公钥内容复制到 `authorized_keys` ，然后 `wq!`  保存

```Bash
su git
mkdir ~/.ssh
vim ~/.ssh/authorized_keys
```

之后修改 `authorized_keys`  文件权限和 `.ssh`  文件夹的权限

```Bash
chmod 600 ~/.ssh/authorized_keys
chmod 700 ~/.ssh
```

## 测试 Git 连接
在本地机器打开  `git bash`  ,测试能不能连接到你的服务器上，`@后面是你的服务器ip地址`
```Bash
ssh -v git@xx.xx.xxx.xx
```

出现如下图则测试连接成功
![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@7fc01caa2a160c029d7768b49163ffc5194070b2/2020/10/14/036334ef892784e398b96731b69134b7.png)


>可能遇到的问题
>@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
>@ WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED! @
>@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
>如果测试连接遇到以上问题在客户机输入以下命令即可解决
>```Bash
>ssh-keygen -R 输入服务器的IP
>```
>重新连接，出现 `Are you sure you want to continue connecting (yes/no)?`  选择 `yes`即可


## Git仓库设置

初始化 git 裸库
切换 `git` 用户，进入 `git` 用户目录下创建，在 `git` 用户目录下初始化一个`git` 仓库 `(hexo.git)`

```Bash
su git
cd /home/git
git init --bare hexo.git
```
此时 `git` 用户目录下就会存在一个 `hexo.git` 仓库 ，并将 `hexo.git` 目录下所有文件拥有者设置为 `git` 用户

```Bash
chown git:git -R hexo.git
```

![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@ed09e6e879efd887a7e9c0792f503264fd223dc9/2020/10/14/31f3df62fdaccb7d0bc9afac8b595b0d.png)
{% note color:yellow 注意 这里必须在 git 用户下用指令创建，不能用其他用户或宝塔面板创建 %}
使用 git-hooks
使用 `post-receive` 钩子，当 `git` 有收发的时候就会调用这个钩子


```Bash
vim ~/hexo.git/hooks/post-receive
```

以下内容复制到 `post-receive` 中，`wq!` 保存

```Bash
#!/bin/sh
git --work-tree=/www/wwwroot/hexo_blog --git-dir=/home/git/hexo.git checkout -f
```

保存退出后再赋予该文件 `执行` 权限

```Bash
cd /home/git/hexo.git/hooks
chmod +x post-receive
```
{%note color:yellow 注意：  确保 `hexo.git .ssh hexo_blog` 目录的用户组权限为 `git:git`，若不是，执行下列命令：
```Bash
chown -R git.git /home/git/hexo.git/
chown -R git.git /home/git/.ssh/
chown -R git.git /www/wwwroot/hexo_blog/
```
%}


这样 `git`自动部署就完成 了

# 部署至服务器

修改 Hexo
在你的博客根目录中找到  `_config.yml`  文件，打开找到 `deploy` 作如下修改
```yml
deploy:
    type: git
    repo: git@xx.xx.xxx.xx:/home/git/hexo.git    # git@ip:/服务器仓库路径
    branch: master      # 分支
```
修改完毕后，每次 `hexo  d` 他就会自动发布到你的远程仓库上
之后就是正常的部署流程：
```Bash
hexo clean			# 清除以前的缓存文件
hexo g				# 生成静态文件
hexo d				# 发布到远程仓库
```

## 自动部署测试
出现图下图所示即表示部署成功

![img](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@68f90aae9839926fdd8a6dd797fb2b8a5a9f5864/2020/10/14/e5b7928a6ec6e83d5f88797e175da7ae.png)

# 结语
> 回首前尘，尽是可耻的过往
