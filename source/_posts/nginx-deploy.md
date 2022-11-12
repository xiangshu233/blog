---
date: 2020-7-20
title: 本机模拟前后端分离部署
categories: [笔记]
tags: [nginx, 反向代理, 模拟部署]
---

## 后端接口代码打包
- maven clean
- maven compile
- maven package

打包完成后将 `target` 下的 `war` 包或者 `edts` 文件包放在 `tomcat webapps` 下或者任意目录下

## 编辑 tomcat 的 server.xml文件

| 元素-属性   | 解释                                                         |
| :---------- | :----------------------------------------------------------- |
| **host**    | **(表示一个虚拟主机)**                                       |
| name        | 指定主机名                                                   |
| appBase     | 应用程序基本目录，即存放应用程序的目录                       |
| unpackWARs  | 如果为true，则tomcat会自动将WAR文件解压，否则不解压，直接从WAR文件中运行应用程序 |
| **Context** | **(表示一个web应用程序，通常为WAR文件，关于WAR的具体信息见servlet规范)** |
| docBase     | 应用程序的路径或者是WAR文件存放的路径                        |
| path        | 表示此web应用程序的url的前缀，这样请求的url为http://localhost:8080/path/（虚拟路径） |
| reloadable  | 这个属性非常重要，如果为true，则tomcat会自动检测应用程序的/WEB-INF/lib 和/WEB-INF/classes目录的变化，自动装载新的应用程序，我们可以在不重启tomcat的情况下改变应用程序 |

```xml
<Context path="/edts" reloadable="true" docBase="F:\testProject\edts"></Context>
```

**保存后重启 tomcat**


## 前端 vue 代码
- yarn build 打包前端vue代码,打包好后的文件为 dist
- 将 dist 文件复制到 nginx 安装目录下的 html 文件里并编辑以下文件
- maven package

## 编辑 nginx.conf 文件

默认 `80` 端口

`location /` 为前端部署的vue包直接输入 ip 就可访问

`root` 为前端代码位置

 `location /edts` 为后端接口（nginx 拦截浏览器发起的后缀为 /edts 的接口请求并转发至 tomcat 8080 端口下，与tomcat 的 server.xml 配置呼应）

```
server {
    listen       80;
    server_name  192.168.1.111;
    charset utf-8;

    location / {
        root 'D:/Program Files/nginx-1.18.0/html/dist';
        index index.html index.htm;
    }

    location /edts{
        add_header Access-Control-Allow-Origin *;
        add_header Access-Control-Allow-Headers X-Requested-With;
        add_header Access-Control-Allow-Methods GET,POST,OPTIONS;

        proxy_pass http://192.168.1.111:8080/edts;
    }
}
```

**保存后重启 tomcat**


访问 http://192.168.1.111



## Nginx 相关概念

### Nginx基本命令

```Bash
nginx -s reload     # 优雅重启，并重新载入配置文件nginx.conf
nginx -s quit       # 优雅停止nginx，有连接时会等连接请求完成再杀死worker进程
nginx -t            # 检查nginx的配置文件
```

### 正向代理就是代理客户端

> 场景举例：类似于VPN代理客户端，Google 并不知道真正访问它的客户端是谁，它只知道这个中间服务器（vpn）在访问它。因此，这里的代理，实际上是中间服务器代理了客户端，这种代理叫做正向代理。
>
> 1. 正向代理服务时由客户端设立
> 2. 客户端了解代理服务器和目标服务器都是谁
> 3. 帮助客户端突破访问权限，提高访问速度，对目标服务器隐藏客户端的IP

![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@17d0f9c57cce7dfc4c821bd5d8bcd5106b263640/2020/10/13/af2db8e252ef7897467abbcd617ec717.png)
### 反向代理就是代理服务器

> 场景举例：10086客服 10086 这个号码相当于是一个代理，真正提供服务的，是话务员，但是对于客户来说，他不关心到底是哪一个话务员提供的服务，他只需要记得 10086 这个号码就行了。
>
> 所有的请求打到 10086 上，再由 10086 将请求转发给某一个话务员去处理。因此，在这里，10086 就相当于是一个代理，只不过它代理的是话务员而不是客户端，这种代理称之为反向代理。
>
> 1. 反向代理服务器是配置在服务端的
> 2. 客户端是不知道访问的到底是哪一台主机
> 3. 达到负载均衡，并且可以隐藏服务器真正的 ip 地址

![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@f17eec300f32465f7ac555a6781fba111b0df7cf/2020/10/13/698d89d3c085959d6a9cce6e9514d1e4.png)


### Nginx 负载均衡默认三种策略

![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@87acc222d6fd40156c85a5e99fd21cf2f6ecfc04/2020/10/13/e633539d7ae0265b59f7dfd1290e133d.png)

> 1. 轮询：
>
>    根据客户端发起的请求，平均的分配给每一台服务器
>
> 2. 权重：
>
>    会将 客户端的请求根据服务器的权重值不同分配 不同的数量
>
> 3. ip_hash：
>
>    ip_hash机制能够让某一客户机在相当长的一段时间内只访问固定的后端的某台真实的web服务器,这样会话就会得以保持,在网站页面进行login的时候就不会在后面的web服务器之间跳来跳去了,也不会出现登录一次的网站又提醒重新登录的情况.



## Nginx 动静分离概念

第一种访问动态资源：

客户端发送请求到Nginx（或者Nginx拦截）再由Nginx帮客户端访问服务器然后服务器返回结果给Nginx,Nginx再把结果交给客户端，这就是动态资源经历的过程最少需要四次连接数

![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@1a10695de855dd2327a5249eceda9b9bbec30218/2020/10/13/8ff6f4333da61a1ca3a97c6da19dd6e2.png)

动态资源代理

```yml
# 一个server下可以有多个location
# 配置如下
location /edts{
    add_header Access-Control-Allow-Origin *;
    add_header Access-Control-Allow-Headers X-Requested-With;
    add_header Access-Control-Allow-Methods GET,POST,OPTIONS;
	# 转发到Tomcat
    proxy_pass http://192.168.1.111:8080/edts;
}
```

第二种访问静态资源：

假如客户端请求的是一个css/图片，客户端还是发送连接到Nginx，而Nginx不再请求服务器而是在Nginx本地直接找css/图片，如果有直接返回，只需要两个连接数

![](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@186b967c4d78dcd847cb39351334fb2054617373/2020/10/13/2a741f33c298a0b7cae770f85ce55872.png)

静态资源代理

```nginx
# 配置如下
location / {
    root 'D:/Program Files/nginx-1.18.0/html/dist';   #静态资源路径
    index index.html index.htm;
}
```
