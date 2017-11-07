## 介绍
如果用户在微信客户端中访问第三方网页，公众号可以通过微信网页授权机制，来获取用户基本信息，进而实现业务逻辑。

如果你想了解技术细节，请前往 [微信公众平台](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421140842)，这里我们只介绍需要做什么。

### 前期工作

#### 到公众平台配置回调域名
前往微信公众平台，找到 **开发 - 接口权限 - 网页服务 - 网页帐号 - 网页授权获取用户基本信息 - 修改** 将授权域名配置为项目部署的域名，注意：是域名不是网址，不带 `http://`。如图：

![https://p0.ssl.qhimg.com/t01b1c3501f4be02dfa.png](https://p0.ssl.qhimg.com/t01b1c3501f4be02dfa.png)

#### 复制 .txt 文件到 www 目录下
如上图，将 `MP_verify_PwmQKE4JhwRd6nQw.txt` 文件下载下来，放在项目目录 `www` 下。

#### 到项目配置页面配置

* 授权成功回调接口地址：授权成功后会将获取到的用户信息以 `POST` 方式通知该接口
* 授权成功回跳页面地址：授权成功后要重定向的页面地址

### 授权

首先简单介绍几个概念：
* `openId` 即为用户在当前公众号中的唯一标识。所有针对用户的操作，都需要它。
* 授权类型分为两种：`snsapi_base` 和 `snsapi_userinfo`,二者的区别是获取用户信息不同，前者只能获取用户 `openId`，静默授权。后者可以获得更多信息（头像，地址等），需要用户同意后授权。
* 因为我们前期配置了授权接口地址以及会跳页面，授权成功后会做两件事情：
  - 将获取到的信息以 `POST` 方式传给响应的接口
  - 跳转到指定页面

#### 引导用户打开授权地址
引导用户打开如下页面：
```
https://open.weixin.qq.com/connect/oauth2/authorize?appid=APPID&redirect_uri=REDIRECT_URI&response_type=code&scope=SCOPE&state=STATE#wechat_redirect
```
里面几个参数：
* APPID：这个无需解释了。
* REDIRECT_URI：微信回调地址，注意这里需要是 url encode 之后的地址。这里如果用`snsapi_base`方式，地址为`/auth/getUserId`；`snsapi_userinfo`方式为`/auth/getUserInfo`。
* SCOPE：`snsapi_base` 和 `snsapi_userinfo`两种方式
* STATE：重定向后会带上 state 参数


举个例子：
```
https://open.weixin.qq.com/connect/oauth2/authorize?appid=wx6d38c89f63a11a42&redirect_uri=https%3A%2F%2Fapi.xxx.com%2Fauth%2FgetUserInfo&response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect
```
#### snsapi_base授权
授权成功后，你配置的接口地址会收到类似数据：
```javascript
{ 
  access_token: 'L577_2VyxzS3cQuiSfkWPWBHysjC_VoSUfv-HFkto6Mq4h-hCfKBvKo0FKWwRDZMN4UkXpetx-i_VlWzH3JWAYjr4TdGjQ87TAsDwskVwaY',
  expires_in: 7200,
  refresh_token: 'hpDrSj76ABXiQhqHN_p-vQSnGb8q7pHUwsdVwa8OTEotjpDuEGpjrFWkxGR276Vr1TgXqXe_NqOhw290K8yrZMNtyW7pj8srW8FCjh74PzI',
  openid: 'o-GHl046UPRVFoifvggilFGdGVX4',
  scope: 'snsapi_base' 
}
```
#### snsapi_userinfo授权
授权成功后，你配置的接口地址会收到类似数据：
```javascript
{ 
  openid: 'o-GHl046UPRVFoifvggilFGdGVX4',
  nickname: '郑farmer🐾',
  sex: 1,
  language: 'zh_CN',
  city: '',
  province: '北京',
  country: '中国',
  headimgurl: 'http://wx.qlogo.cn/mmopen/vi_32/DYAIOgq83eq69BUSktQUY2u12nF4pmI68WibU2WAFSviauOjRMWeM1uaY18ZqYJWduic03pRCtSwBrGRibKENR3rWw/0',
  privilege: [] 
}
```
