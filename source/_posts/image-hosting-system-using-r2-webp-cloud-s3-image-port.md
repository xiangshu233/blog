---
title: 我的博客最新免费图床方案（CLoudflare R2 + WebP Cloud + S3 Image Port）记录
tags: [图床, CLoudflareR2]
categories: [博客]
cover: https://03c068f.webp.li/i/2025/02/07/18ygwk-5j.jpg
banner: https://03c068f.webp.li/i/2025/02/07/18ygwk-5j.jpg
date: 2025-2-21 0:59:10
references:
  - title: '从零开始搭建你的免费图床系统（Cloudflare R2 + WebP Cloud + PicGo）'
    url: https://www.pseudoyu.com/zh/2024/06/30/free_image_hosting_system_using_r2_webp_cloud_and_picgo/
  - title: 'Cloudflare 解析 CNAME'
    url: https://docs.tangly1024.com/article/vercel-domain#6bbd8a77b83344cf8cadf3af7ae12096
  - title: 'Hexo 博客使用 WebP Cloud Service'
    url: https://keshane.moe/2023/06/14/using-webp-cloud-service-on-hexo/
  - title: 'Cloudflare对象存储R2配置'
    url: https://blog.tanglu.me/cloudflare-R2-configure/
---

## 我的博客图床变迁方案历史

### GitHub + jsDelivr CDN + PicGo

一开始的方案是新建一个 github 仓库 [[blogAssets](https://github.com/xiangshu233/blogAssets)]，通过 PicGo 上传到仓库，PicGo 返回的图片路径为 [jsDelivr](https://www.jsdelivr.com/) CDN 加速后的链接，配合 Typora 的偏好设置与 PicGo 关联，在写作的时候可以实现复制粘贴图片后自动上传至仓库，整个操作还是很丝滑的，但好景不长， jsDelivr 遭到了 DNS 污染，被大陆封锁。

### 又拍云 + PicGo

后来域名备案下来了，于是开始使用国内的对象存储，记得当时又拍云是比较火的且有 10G 的免费空间，尝试使用毕竟这样国内访问速度会好很多。配置不算复杂，按照文档来即可，这个方案应该持续了一两年，直到有一次我的流量被刷爆，欠费100 多（当场气晕），怒发一封工单，最后只给了我P用没有的优惠券，一气之下不玩了。

### 多吉云 + 腾讯云 EdgeOne 边缘安全加速平台（CDN）

最后阿里云服务器到期了又搬到了腾讯云，那个时候腾讯云推出 EdgeOne 一款安全加速的 CDN，号称边缘函数、四层代理吹的挺厉害的，计费规则是以防护后的干净流量进行计费，且个人版一个月只要 9.9，配合多吉云免费 10G 的存储，还是很香的（信了腾讯云鬼话）。正当这套方案打算一直用下去的时候，不出意外的情况下还是出意外了，2024 年 7 月 23 日晚上 20:30 ~ 22:30 这段时间一连收到四五条腾讯云的警告，赶紧打开控制台去看记录，发现已经刷了我 284G 的流量（当场气晕），只能先暂时停用 EdgeOne。在群里询问得知这次攻击是山西 IP 段的无差别 PCDN 攻击，PCDN 团伙为了平衡上下流量，在搞恶意刷量的行为，防止被运行商检测到。

![](https://03c068f.webp.li/i/2025/02/20/12pixq-ng.png)

> 他只需略微出手，就能让你 CDN 里的一个小文件几个小时之内被访问破万次。
> 再给他一个神仙管理端，他能让你的 CDN 配置下发延迟近一倍，享受字(liu)节(liang)跳动的快感。
>
> 不是代理 IP 买不起，而是伪造 Header 更有性价比。

隔天账单出来了，一共 100 多块，事后封锁 IP 也属于亡羊补牢，总会有新的 IP，只能先把山西 IP 段禁了，后来发工单找腾讯云扯了半天就是不给补偿只给优惠券，最后还是自己掏了这 100 块，通过这次教训让我知道了EdgeOne 并没有想象中的那么牛逼，也可能它根本就不牛逼。

### Cloudflare R2 + WebP Cloud + S3 Image Port

介于腾讯云服务器马上到期，看了下续费价格下一年 600 多😑，况且我这博客本来就是自己写着玩的，也没必要拘泥于国内，于是干脆把服务器停了直接部署到 Vercel 上去了。过了一段时间收到短信说我域名解析没有指向国内要给我取消备案停止解析，查了下得知域名备案必须要有国内服务器才给备案，那我干脆直接取消备案算了，接着又出现多吉云那边域名由于没有备案也停止解析了，整无语了，现在的情况是我要想继续使用 9.9 的 多吉云 + 腾讯云 EdgeOne 图床方案，我还必须要买个服务器，为了一个 CDN 买一个服务器，这也太搞了。

所以就瞄准了海外 CDN + Vercel 免费白嫖方案，一分钱不要美汁汁。

说到海外 CDN 那第一个想到的就是赛博菩萨 Cloudflare 提供的 R2 对象存储这一服务，免费计划中有每月 10 GB 的存储容量，对于个人使用来说完全够用，大厂的服务与数据安全也有保障。

### **Cloudflare R2 vs 国内 CDN 厂商存储**

| **对比维度**       | **Cloudflare R2**                            | **国内 CDN 厂商（阿里云 OSS、腾讯 COS、多吉云等）** |
| ------------------ | -------------------------------------------- | --------------------------------------------------- |
| **出口流量费用**   | ✅ **免费**（无 Egress 费用）                 | ❌ **按流量收费**（通常按 GB 计费，成本较高）        |
| **存储费用**       | ✅ **低成本**（按存储量收费）                 | ❌ **按存储量收费**（价格因厂商而异）                |
| **CDN 加速**       | ✅ **可与 Cloudflare CDN 无缝结合**           | ✅ **厂商自带 CDN，但需额外配置**                    |
| **全球可用性**     | ✅ **全球分布，无需备案**                     | ❌ **国内服务需 ICP 备案**                           |
| **回源优化**       | ✅ **智能回源，减少访问延迟**                 | ✅ **国内厂商优化国内回源**                          |
| **与边缘计算结合** | ✅ **Cloudflare Workers + R2 可直接处理请求** | ❌ **部分厂商提供 FaaS，但与存储整合度低**           |
| **国内访问速度**   | ❌ **国内无官方节点，速度可能受限**           | ✅ **国内厂商在国内优化较好，速度快**                |
| **数据迁移**       | ✅ **支持 S3 兼容 API，方便迁移**             | ❌ **部分厂商采用自有 API，迁移难度大**              |

为了优化用户的访问，又使用了一个「[WebP Cloud](https://webp.se/)」服务对 R2 的图片进行代理，在代理层面进一步减小图片体积，虽然对于国内用户来说速度肯定还是比不上阿里云 OSS 这种线路，但是在不用备案、稳定且免费的综合条件下，这是我能想到的最好的方案了。

这里使用 [S3 Image Port](https://docs.iport.yfi.moe/zh/)，它是一个网页端的图片管理面板，至于为什么不用 PicGo 看个人喜好吧，我用它单纯觉得界面好看，它可以展示所有存储桶里的图片，而 PicGo 只能展示它上传的图片。

## 图床搭建教程

Cloudflare R2 + WebP Cloud + S3 Image Port 就是目前最强的免费方案。

Cloudflare R2 Storage 的免费计划包含每月 10 GB 的存储空间、每月 100 万次 A 类操作请求和每月 1,000 万次 B 类操作请求，加之其无出口费用的特点，使之成为了个人小型图床云存储的良好选择。

首先你需要免费注册一个 [Cloudflare 账号](https://www.cloudflare.com/zh-cn/)才能使用，如果你是首次使用注册后它会让你添加一个域名，添加域名后个人建议把域名解析交给 **Cloudflare** 管理。这样就跟国内域名厂商没有任何关系了（你只是在它那里买了一个域名而已）。

- 如何将阿里云购买的域名放在CloudFlare中解析与托管？ 具体配置方法可以参考《[阿里云域名用CloudFlare解析域名](https://bbs.maozhishi.com/d/56-cloudflare)》。

点击右上角的 **添加** 选择 **R2 Storage 存储桶**

#### 创建存储桶

![](https://03c068f.webp.li/i/2025/02/20/12tppb-bi.png)

选择后如果你没绑信用卡（我这里选择绑 **PayPal**）它会先让你绑定信用卡，但并不会扣费，主要是为了验证用户身份使用。至于地址信息什么的直接上网搜一下**美国地址生成器**就好了

![](https://03c068f.webp.li/i/2025/02/20/12vo66-yh.png)

输入**存储桶名字**，位置选择**北美洲西部**，因为后续还将使用 [WebP Cloud](https://webp.se/) 服务的美西机房进行图片代理优化，所以在此处选择的是 **北美洲西部（WNAM）**，根据需求选其他区域也可以，但 Cloudflare 并不保证一定会分配到所指定的区域，最后点击创建存储桶。

这里贴个 WebP Cloud 博客中关于如何选择区域的文章：

[如何选择 WebP Cloud 的区域？WebP Cloud Services 区域一览](https://blog.webp.se/webp-cloud-services-regions-zh/)

> 对于中国大陆的用户而言，由于 Cloudflare 在中国大陆没有边缘节点，截止本文写作时大部分的请求都会访问到 Cloudflare 美国的边缘，所以我们建议如果面向中国大陆用户的话，在美国区域创建 WebP Cloud Proxy 可以获得最好的访客体验。

![](https://03c068f.webp.li/i/2025/02/20/15mzlm-q5.webp)

此时我们已经可以向我们的 **r2-test** 存储桶上传文件了，可以选择在网页直接上传文件或文件夹。

![](https://03c068f.webp.li/i/2025/02/20/163yma-a7.webp)

也可以使用 **S3 API** 进行上传，后续使用 **S3 Image Port** 进行上传就依赖这种方式，但需要进行一些额外配置，点击导航栏 **设置** 选项进行配置。

![](https://03c068f.webp.li/i/2025/02/20/17lfc0-c1.webp)

首先我们需要打开 **R2.dev 子域**，这是为了后续访问图片时需要的公网地址，点击 **允许访问**，并按照提示输入 **allow** 即可开启。

![274_2x_shots_so.png](https://03c068f.webp.li/i/2025/02/20/17wyc4-49.webp)

完成后会显示一个以 `r2.dev` 结尾的公网网址，就是我们后续访问图片的公网网址。

#### 自定义存储桶域名（可选）

默认分配的地址比较长不便于记忆，我们可以通过 **添加自定义域** 来配置自己的专属域名，任意输入一个二级域名，如 `r2-test.xiangshu233.cn`。

![77_2x_shots_so.png](https://03c068f.webp.li/i/2025/02/20/18glzw-8s.webp)

点击继续后出现如下界面继续点击 **连接域**。

![733_2x_shots_so.png](https://03c068f.webp.li/i/2025/02/20/18pfdo-x5.webp)

接下来等待 **DNS** 解析生效即可。

![527_2x_shots_so.png](https://03c068f.webp.li/i/2025/02/20/18svwz-1b.webp)

回到顶部 我们查看存储桶状态 **公共 URL 访问** 显示 “**已允许**”，且 **域** 显示为我们刚自定义的域名即配置成功。

![411_2x_shots_so.png](https://03c068f.webp.li/i/2025/02/20/1924pk-8l.webp)

在 **DNS** 解析记录这里也能看到我们的自定义域名 `r2-test.xiangshu233.cn` 域名 **成功代理** 了 `r2-test` 存储桶。

![110_2x_shots_so.png](https://03c068f.webp.li/i/2025/02/20/18zh29-te.webp)

当我们完成上述配置后，可以回到存储桶 **对象** 界面，上传一张示例图片，点开详情则会显示该图片的访问地址，可以看到我们的自定义域已经配置成功了，此时我们就拥有了一个可访问的图床服务了。

![644_2x_shots_so.png](https://03c068f.webp.li/i/2025/02/20/1bcdk6-xc.webp)

#### 配置存储桶访问 API

回到 R2 首页，可以看到存储桶列表，点击右边的 **API** 下拉框，选择 **管理 API 令牌** 。

![494_2x_shots_so.png](https://03c068f.webp.li/i/2025/02/20/1a0x0j-uy.webp)

点击右上角 **创建 API 令牌**。

![139_2x_shots_so.png](https://03c068f.webp.li/i/2025/02/20/1a9wtm-jd.webp)

输入令牌名称，权限选择 **对象读和写**，指定存储库选择我们刚才创建的 `r2-test` 存储桶，TTL 永久，确认无误后点击右下角 **创建 API 令牌**。

![121_2x_shots_so.png](https://03c068f.webp.li/i/2025/02/20/1aetsr-n8.webp)

如下图所示则创建成功，这些秘钥只会显示 **一次** ，请妥善保管。

至此，我们需要在 Cloudflare R2 上配置的部分就完成了，接下来我们需要配置 [S3 Image Port](https://iport.yfi.moe/zh)。

![984_2x_shots_so.png](https://03c068f.webp.li/i/2025/02/20/1bf732-ma.webp)

### S3 Image Port

> [S3 Image Port](https://docs.iport.yfi.moe/zh/guide/what-is-sip) 是一个网页端的图片管理面板，用于管理 AWS S3 存储桶或 S3 兼容服务 （如 Cloudflare R2、DigitalOcean Spaces、腾讯 COS、阿里云 OSS 等）中的图片。
>
> 传统上这些存储服务没有专门的图片管理面板，该解决方案为图片的上传、管理和集成提供了一个简单而强大的界面。
>
> 本面板本身不存储任何数据，所有数据都存储在您的 S3 存储桶中。因此，您可以随时迁移或删除本面板，而不会丢失任何数据。

特性和功能

- ☁️ **上传图片**：轻松上传您的图片，支持上传前压缩及格式转换。
- 🖼️ **图库**：在图库中浏览和查找所有您已经上传的图片，支持丰富的过滤选项。
- 🔗 **复制图片地址**：只需一次点击，就可以复制图片的纯链接或 Markdown 格式链接。
- 🗑️ **删除图片**：在管理面板中快速删除您已上传的图片。

该管理面板是完全响应式的，在移动设备上也能无缝运行。

![314_2x_shots_so.png](https://03c068f.webp.li/i/2025/02/20/1bsp5v-ld.webp)

#### 配置 CORS

> 由于我们是一个网页项目，配置 CORS 是必须的。目前用户反馈“连不上”的大多数原因都是没有配置 CORS。

- 前往 [Cloudflare dashboard](https://dash.cloudflare.com/) 并在左侧选择 R2；
- 选择你刚刚创建的存储桶；
- 点击 `设置`，在 `CORS 策略` 板块右侧点击 `添加 CORS 策略`。

```json
[
  {
    "AllowedOrigins": ["https://iport.yfi.moe"],
    "AllowedMethods": ["GET", "PUT", "DELETE", "HEAD", "POST"],
    "AllowedHeaders": ["*"]
  }
]
```

把上面的整个 JSON 复制粘贴过去，点击保存。

![789_2x_shots_so.png](https://03c068f.webp.li/i/2025/02/20/1caejp-qn.webp)

#### 配置秘钥

现在你只需将之前获得的 Cloudflare API 信息填入 [S3 Image Port 的设置页](https://iport.yfi.moe/zh/settings/s3)，就可以开始使用 S3 Image Port 了。

现在让我们看下这些字段都是什么意思

- `Endpoint`: Cloudflare  API S3 客户端使用管辖权地特定的终结点
- `Bucket Name`: 你刚才创建的存储桶名字
- `Region`: 这是存储桶的区域一点要填写 `auto`
- `Access Key ID`: Cloudflare  API 访问密钥 ID
- `Secret Access Key`: Cloudflare  API 机密访问密钥
- `Public URL`: 你刚才配置的自定义域名

按照上面的字段配置后点击测试 **测试连接** 出现三个绿点表示配置成功

![92_2x_shots_so.png](https://03c068f.webp.li/i/2025/02/20/1e5ct4-fr.webp)

#### 上传测试

现在你可以点击顶部的 **上传** 选择任意一张图片测试一下是否可以正常上传，如下图所示可以看到我们的配置是正确的 **包括先前我们自行在  Cloudflare R2 上传的那张图片也展示出来了**。

![125_2x_shots_so.png](https://03c068f.webp.li/i/2025/02/20/1e2b9c-7e.webp)

#### 其他个性化配置

另有其他个性化配置可移步 S3 Image Port 的 [Cloudflare R2 逐步指南](https://docs.iport.yfi.moe/zh/guide/for-cloudflare-r2) 自行配置，如上传压缩、自部署等。

### [WebP Cloud](https://webp.se/)

简单来说这是一个类 CDN 的图片代理 SaaS 服务，可以在几乎不改变画质的情况下大幅缩小图片体积，加快整体站点加载速度。发展到现在除了图片体积减少外，还提供了缓存、添加水印、图片滤镜等更多实用的功能，并提供了自定义 Header 等配置选项。

![556_2x_shots_so.png](https://03c068f.webp.li/i/2025/02/20/1eshe2-7r.webp)

#### 配置 WebP Cloud

通过 Github 登录 [**WebP Cloud Services**](https://dashboard.webp.se/login) 服务后可以看到当前 Plan 下的 Free Quota 和额外 Quota 的数据，以及一些用量统计，点击 **创建代理** 。

![36_2x_shots_so.png](https://03c068f.webp.li/i/2025/02/20/1f492y-tg.webp)

- 为了优化国内访问，我们「Proxy Region」选择的是美西「Hillsboro, OR」区域，Cloudflare R2 创建的存储桶也是美西区域
- 「Proxy Name」填写一个自定义名称即可
- 「Proxy Origin URL」，比较重要，需要填写上文我们配置好的 R2 自定义域名，如我填写的是 `https://r2-test.xiangshu233.cn`，如果没配置自定义域名则填写 R2 提供的 `xxx.r2.dev` 格式的域名

![543_2x_shots_so.png](https://03c068f.webp.li/i/2025/02/20/1f9qi3-le.webp)

图中显示的以 `xxx.webp.li` 格式即为我们的代理地址。

#### 代理流程示例

用户 ---> WebP Cloud 代理服务器 ---> 原始图片服务器 ---> WebP Cloud 代理服务器 ---> 优化后的图片 (xxx.webp.li) ---> 用户

1. **用户** 请求图片。
2. 请求经过 **WebP Cloud 代理服务器**，代理服务器向 **原始图片服务器** 请求图片。
3. **原始图片服务器** 返回图片后，**WebP Cloud 代理服务器** 对图片进行优化（格式转换、压缩等）。
4. 优化后的图片会生成一个 **以 `.webp.li` 为后缀的代理地址**，如：`dc84642.webp.li/new_mbp_setup.jpg`。
5. 最终，**优化后的图片** 通过代理地址返回给 **用户**，完成整个访问流程。

![417_2x_shots_so.png](https://03c068f.webp.li/i/2025/02/21/7c25-kz.webp)

例如我们之前通过 [S3 Image Port](https://docs.iport.yfi.moe/zh/guide/what-is-sip)  上传到 R2 的文件 `https://r2-test.xiangshu233.cn/i/2025/02/20/1daxmg-z7.webp` 则可以用 `https://80b4469.webp.li/i/2025/02/20/1daxmg-z7.webp ` 这一链接进行访问。

#### 设置 Public URL

让我们回到 [S3 Image Port 存储桶设置页](https://iport.yfi.moe/zh/settings/s3)，找到 **Public URL** 项，把 `https://r2-test.xiangshu233.cn` 替换为 `https://80b4469.webp.li`

![549_2x_shots_so.png](https://03c068f.webp.li/i/2025/02/21/z2zg-gs.webp)

#### WebP Cloud 测试

可以看到图片 URL 已经被成功代理且得到了很大的压缩，这张图片在 Cloudflare R2 那边大小是 3.85 MB，在网站上它被压缩到了 485KB。

| 对象                                      | 类型       | 存储类 | 大小    | 已修改时间               |
| ----------------------------------------- | ---------- | ------ | ------- | ------------------------ |
| 📁 i/                                      | 目录       | --     | --      | --                       |
| [343DC3B787318EA6AA57956848AA346D.jpg](#) | image/jpeg | 标准   | 3.85 MB | 2025年2月20日晚上9点07分 |

![1_2x_shots_so.png](https://03c068f.webp.li/i/2025/02/21/158lo-6p.webp)

另外 WebP Cloud 同样支持自定义域名，我这里不在赘述了，有兴趣可以自行配置，非常简单。

#### WebP Cloud 用量

免费用户每天有 3000 Free Quota，即能够代理 2000 次图片访问请求，并提供 200M 的图片缓存，对于一般用户来说完全够用，如有一些流量较大的特定时期也可以购买额外 Quota，价格很便宜。

如超过了 Quota，访问则会被 301 转发到源站图片地址，不经过 WebP Cloud 服务压缩，但依然可用；超过 200M 的缓存则会按照 LRU 算法清理，所以依然能够保障一些高频请求的图片能够有较好的访问体验。

![679_2x_shots_so.png](https://03c068f.webp.li/i/2025/02/21/1lwqh-am.webp)

如果觉得不够用也可以升级 Lite 订阅，一个月也才 3 刀，你将获得：

- 每日 8,000 个请求额度

- 最多 6 个代理

- 1024 MiB 缓存

- 无限流量

- 还算好用的 API 服务

## 结语

以上就是我基于 CLoudflare R2 + WebP Cloud + S3 Image Port 搭建的最新的图床系统方案，其实这个图床系统过年的时候就已经配好了，当时懒得写了，但后来想想还是写一下吧，算是一个比较重大的改动。今天相当于从头到尾把所有过程重新走了一遍，整个过程很顺畅，没有在任何一个环节卡住，所以只要你按照我的流程来是 100% 成功的。

另外整个图床系统完全参考 **pseudoyu** 作者的文章：[从零开始搭建你的免费图床系统（Cloudflare R2 + WebP Cloud + PicGo）](https://www.pseudoyu.com/zh/2024/06/30/free_image_hosting_system_using_r2_webp_cloud_and_picgo/)来的，再次感谢。


PS: 上面的代理和存储桶都是测试用的，在本文写完后我会**删除**所以不要填我的一些秘钥或者地址（填了也没用）。<img src="https://cdn.xiangshu233.cn/i/2025/02/21/2vbwt-rb.gif" alt="https://03c068f.webp.li/i/2025/02/21/2vbwt-rb.gif" style="zoom: 50%;" />



