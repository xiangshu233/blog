---
robots: noindex,nofollow
menu_id: more
seo_title	: 友链
toc_title: 友链索引
comment_title: 和我交换友链
comment_id: '朋友'
sidebar: [welcome, recent]
comments: true
breadcrumb: true
---

## 开源大佬
{% friends 开源大佬 %}

## 海内存知己 天涯若比邻
感谢人生旅途中的每一份真挚的友谊：{% friends repo:xiangshu233/blog-friends %}

## 关于友链

**我可以交换友链吗？**

先友后链，在我们有一定了解了之后才可以交换友链，除此之外，您的网站还应满足以下条件：

- 合法的、非营利性、无商业广告
- 有实质性原创内容的 `HTTPS` 站点



{% timeline %}

<!-- node 第一步：新建 Issue -->

新建 [GitHub Issue](https://github.com/xiangshu233/blog-friends/issues/) 按照模板格式填写并提交。

为了提高图片加载速度，建议优化头像：
1. 打开 [压缩图](https://www.yasuotu.com/) 上传自己的头像，将图片尺寸调整到 `96px` 后下载。
2. 将压缩后的图片上传到 [去不图床](https://7bu.top/) 并使用此图片链接作为头像。
3. 网站缩略图可以去 [thum](https://www.thum.io/) 输入自己的站点地址即可返回外链。

<!-- node 第二步：添加友链并等待管理员审核 -->

请添加本站到您的友链中，如果您也使用 issue 作为友链源，只需要告知您的友链源仓库即可。


{% codeblock lang:json %}
{
  "title": "傲慢或香橙",
  "url": "https://xiangshu233.cn",
  "avatar": "https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@0.2/assets/images/avatar-1.jpg",
  "screenshot": "https://image.thum.io/get/https://www.xiangshu233.cn",
  "description": "随知修行乃当务之急，然怠惰度日至今"
}
{% endcodeblock %}


待管理员审核通过，添加了 `active` 标签后，回来刷新即可生效。
{% endtimeline %}

如果您需要更新自己的友链，请直接修改 issue 内容，大约 3 分钟内生效，无需等待博客更新。如果无法修改，可以重新创建一个。
