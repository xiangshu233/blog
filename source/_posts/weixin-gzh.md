---
date:  2021-3-24
title: 微信公众号获取用户 openid 及用户信息
categories: [小程序]
tags: [微信公众号, java, vue]
---


>本次开发主要是在公众号中访问服务器一个表单页面提交用户信息到后台。以下内容均来自于微信公众号官方文档，文档写的已经很详细了这里稍作记录，便于日后查阅微信公众号开发文档：https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/Wechat_webpage_authorization.html#2


大致流程如下

第一步：用户同意授权，获取 `code`

第二步：通过code换取网页授权 `access_token`

第三步：拉取用户信息(需 `scope` 为 `snsapi_userinfo`）

{% note color:yellow 该接口权限需要开启微信公众号认证才可调用。 %}


>在微信公众号请求用户网页授权之前，开发者需要先到公众平台官网中的“`开发 - 接口权限 - 网页服务 - 网页帐号 - 网页授权获取用户基本信息`”的配置选项中，修改授权回调域名。请注意，这里填写的是`域名`（是一个字符串），而不是`URL`，因此请勿加 `http://` 等协议头


跳转页面需要获取用户的基本信息（openId、头像、昵称、性别等）

菜单配置链接：

`https://open.weixin.qq.com/connect/oauth2/authorize`

`?appid=xxxxxxxxx`

`&redirect_uri=xxxxxxx`

`&response_type=code`

`&scope=snsapi_userinfo`

`&state=state#wechat_redirect`


{% note color:yellow 特殊场景下的静默授权： 如果用户关注了该公众号且从公众号回话或者自定义菜单进入网页授权页，即使是 scope 为 snsapi_userinfo 88也是静默授权，用户无感知 %}



| 参数             | 是否必须 | 说明                                                         |
| :--------------- | :--------------------------- | :--------------- |
| appid            | 是       | 公众号的唯一标识                                             |
| redirect_uri     | 是       | 授权后重定向的回调链接地址， 请使用 urlEncode 对链接进行处理 |
| response_type    | 是       | 返回类型，请填写code                                         |
| **scope**        | **是**   | **应用授权作用域，snsapi_base （不弹出授权页面，直接跳转，只能获取用户openid），snsapi_userinfo （弹出授权页面，可通过openid拿到昵称、性别、所在地。并且， 即使在未关注的情况下，只要用户授权，也能获取其信息 ）** |
| state            | 否       | 重定向后会带上state参数，开发者可以填写a-zA-Z0-9的参数值，最多128字节 |
| #wechat_redirect | 是       | 无论直接打开还是做页面302重定向时候，必须带此参数            |

根据上述配置菜单后以下是获取`openId`及`网页授权access_token`的步骤



## 第一步：用户访问页面同意授权后，获取url上的code

如果用户同意授权，页面将跳转至 redirect_uri/?code=CODE&state=STATE。


{% note color:green code说明： code 作为换取 access_token 的票据，每次用户授权带上的 code 将不一样，code 只能使用一次，5分钟未被使用自动过期。%}



js前端代码示例：

1、api.js

```js
export function login(params) {
    return Vue.axios({
        url: '/yqfk-wx/wechatHandler/login',
        method: 'post',
        data: params
    });
}
```

2、截取路径code并请求后台

```js
import { login } from '@/api/index';

created() {
    const code = this.getUrlParam('code')  // 截取路径中的code
    this.getOpenId(code) //把code传给后台获取用户信息
}

methods: {
    getOpenId(code) {
        const params = { code: code };
        login(params).then((res) => {
            console.log('接口数据', res)
        }).catch(() => {});
    },

    getUrlParam(name) {
        var reg = new RegExp('(^|&)' + name + '=([^&]*)(&|$)');
        let url = window.location.href.split('#')[0];
        let search = url.split('?')[1];
        if (search) {
            var r = search.substr(0).match(reg);
            if (r !== null) {
                return unescape(r[2]);
            }
            return null;
        } else {
            return null;
        }
    },
}
```

## 第二步：通过code换取网页授权access_token

> 在确保微信公众账号拥有授权作用域（scope参数）的权限的前提下（服务号获得高级接口后，默认拥有scope参数中的`snsapi_base`和`snsapi_userinfo`），则通过`code`换取`网页授权access_token`同时也获取了`openid`。通过`code`换取的是一个特殊的`网页授权access_token`，与基础支持中的`access_token`（该access_token用于调用其他接口）不同。


获取code后，请求以下链接获取access_token：

`https://api.weixin.qq.com/sns/oauth2/access_token`

`?appid=APPID`

`&secret=SECRET`

`&code=CODE`

`&grant_type=authorization_code`

| 参数       | 是否必须 | 说明                     |
| :--------- | :------- | :----------------------- |
| appid      | 是       | 公众号的唯一标识         |
| secret     | 是       | 公众号的appsecret        |
| code       | 是       | 填写第一步获取的code参数 |
| grant_type | 是       | 填写为authorization_code |


>尤其注意：由于公众号的 `secret` 和获取到的 `access_token` 安全级别都非常高，必须只保存在 `服务器` ，不允许传给 `客户端` 。后续刷新`access_token`、通过 `access_token` 获取 `用户信息` 等步骤，也必须从 `服务器` 发起。


java后端代码示例：

1、controller

```java
@RequestMapping("/login")
@ResponseBody
public Map<String, Object> wechatOauth(String code) throws Exception {
    Map<String, Object> map = new HashMap<>();
    //输入校验
    if(StringUtil.isEmpty(code) ){
        throw new ServiceException("code不能为空！");
    }

    // 获取网页授权access_token
    Map<String, String> accessToken = weChatCoreService.getAccessToken(code);

    String openId = accessToken.get("openid");
    map.put("openId", openId);
    return getSucessMap(map);
}
```

2、service

```java
//通过code获取access_token和openid的微信请求前缀
public static final String GET_BY_CODE = "https://api.weixin.qq.com/sns/oauth2/access_token?";
//必填，固定值
public static final String GRANT_TYPE = "authorization_code";

@Override
public Map<String,String> getAccessToken(String code) throws Exception {
    // AppId、Secret存放在 setting.properties 中，SettingUtil工具类可以访问到
    String AppId = SettingUtil.getString("AppId");
    String Secret = SettingUtil.getString("Secret");
    // 请求连接
    String requestUrl = GET_BY_CODE + "appid=" + AppId + "&secret=" + Secret + "&code=" + code + "&grant_type=" + GRANT_TYPE;
    JSONObject reponseJson = null;
    try {
        // HttpHelper请求框架
        reponseJson = HttpHelper.httpGet(requestUrl);
    } catch (Exception e) {
        e.printStackTrace();
    }

    String access_token = reponseJson.getString("access_token");
    String openId = reponseJson.getString("openid");

    Map<String,String> map = new HashMap<String,String>();
    map.put("access_token", access_token);
    map.put("openid", openId);
    return map;
}
```

正确返回结果如下：

```json
{
  "access_token":"ACCESS_TOKEN",
  "expires_in":7200,
  "refresh_token":"REFRESH_TOKEN",
  "openid":"OPENID",
  "scope":"SCOPE"
}
```

## 第三步：拉取用户信息(需scope为 snsapi_userinfo)

如果网页授权作用域为 `snsapi_userinfo`，则此时开发者可以通过 `access_token` 和 `openid` 拉取用户信息了。

请求接口：

 `https://api.weixin.qq.com/sns/userinfo`

`?access_token=ACCESS_TOKEN`

`&openid=OPENID`

`&lang=zh_CN`

| 参数         | 描述                                                         |
| :----------- | :----------------------------------------------------------- |
| access_token | 网页授权接口调用凭证,注意：此access_token与基础支持的access_token不同 |
| openid       | 用户的唯一标识                                               |
| lang         | 返回国家地区语言版本，zh_CN 简体，zh_TW 繁体，en 英语        |

java后端代码示例：

1、controller

{% note color:yellow 注意： 该controller方法与第二步的controller为同一个，此处为补充部分代码 %}

```java
@RequestMapping("/login")
@ResponseBody
public Map<String, Object> wechatOauth(String code) throws Exception {
    // Map 对象用于存放最后结果
    Map<String, Object> map = new HashMap<>();

    //输入校验
    if(StringUtil.isEmpty(code) ){
        throw new ServiceException("code不能为空！");
    }

    // 获取网页授权access_token
    Map<String, String> accessToken = weChatCoreService.getAccessToken(code);

    String openId = accessToken.get("openid");
    // 去数据库查询是否存在该用户
    SysUser userDB = sysUserService.findByOpenId(openId);

    // 如果用户信息不存在则认定为新用户
    if (userDB == null) {
        // 调用getWechatUserInfo获取微信用户信息
        Map<String, String> WechatUserMap = weChatCoreService.getWechatUserInfo(accessToken);
        // new SysUser 实体类
        SysUser wechatUser = new SysUser();
        wechatUser.setNickName(WechatUserMap.get("nickname"));
        wechatUser.setAvatarUrl(WechatUserMap.get("headimgurl"));
        wechatUser.setCountry(WechatUserMap.get("country"));
        wechatUser.setOpenId(openId);
        // 保存用户信息wechatSave
        userDB = sysUserService.wechatSave(wechatUser);
    }

    // 以下为业务逻辑代码
    SysStaffInfo queryStaffInfo = new SysStaffInfo();
    queryStaffInfo.setUserId(userDB.getUserId());
    // 根据用户 id 查询员工信息
    List<SysStaffInfo> list = sysStaffInfoService.findList(queryStaffInfo);
    if (list.size() > 0) {
        map.put("sysStaffInfo", list.get(0));
    }
    // 存入map
    map.put("openId", openId);
    map.put("sysUser", userDB);
    return getSucessMap(map);
}
```

2、service代码

```java
//从微信公众平台接口获取用户基本信息（网页授权）前缀
public static final String GET_USERINFO_WEB = "https://api.weixin.qq.com/sns/userinfo?";

@Override
public Map<String,String> getWechatUserInfo(Map<String, String> map) {
    String openid = map.get("openid");
    String access_token = map.get("access_token");

    String requestUrl = GET_USERINFO_WEB + "access_token=" + access_token + "&openid=" + openid + "&lang=zh_CN";
    JSONObject reponseJson = null;
    try {
        reponseJson = HttpHelper.httpGet(requestUrl);
    } catch (Exception e) {
        e.printStackTrace();
    }

    String nickname = reponseJson.getString("nickname");
    String sex = reponseJson.getString("sex");
    String province = reponseJson.getString("province");
    String city = reponseJson.getString("city");
    String country = reponseJson.getString("country");
    String headimgurl = reponseJson.getString("headimgurl");

    map.put("sex", sex);
    map.put("province", province);
    map.put("city", city);
    map.put("country", country);
    map.put("headimgurl", headimgurl);
    map.put("nickname", nickname);
    return map;
}
```

返回说明

正确时返回的JSON数据包如下：

```json
{
  "openid": "OPENID",
  "nickname": NICKNAME,
  "sex": 1,
  "province":"PROVINCE",
  "city":"CITY",
  "country":"COUNTRY",
  "headimgurl":"https://thirdwx.qlogo.cn/mmopen/g3MonUZtNHkdmzicIlibx6iaFqAc56vxLSUfpb6n5WKSYVY0ChQKkiaJSgQ1dZuTOgvLLrhJbERQQ4eMsv84eavHiaiceqxibJxCfHe/46",
  "privilege":[ "PRIVILEGE1" "PRIVILEGE2"     ],
  "unionid": "o6_bmasdasdsad6_2sgVt7hMZOPfL"
}
```
以上

## 结语
> 天生百种愁，挂在斜阳树。
> 绿叶阴阴占得春，草满莺啼处。
> 不见生尘步。空忆如簧语。
> 柳外重重叠叠山，遮不断、愁来路。
> ———— 《卜算子·天生百种愁》宋代 徐俯
