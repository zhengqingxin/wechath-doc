### 介绍
微信JS-SDK是微信公众平台面向网页开发者提供的基于微信内的网页开发工具包。
通过使用微信JS-SDK，网页开发者可借助微信高效地使用拍照、选图、语音、位置等手机系统的能力，同时可以直接使用微信分享、扫一扫、卡券、支付等微信特有的能力，为微信用户提供更优质的网页体验。JS-SDK提供的功能以及配置项见 [微信官方文档](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421141115)。

这里我们介绍在 Wechath 中使用 JS-SDK 需要做如下几步：

### 绑定安全域名
先登录微信公众平台进入“公众号设置”的“功能设置”里填写“JS接口安全域名”。

![https://p1.ssl.qhimg.com/t0142ad75fe61ed3db9.png](https://p1.ssl.qhimg.com/t0142ad75fe61ed3db9.png)

如上图，先将 `MP_verify_PwmQKE4JhwRd6nQw.txt` 文件下载下来，放在项目目录 `www` 下。确定http://your_domain/MP_verify_PwmQKE4JhwRd6nQw.txt 能访问后，再点击确定。

### 获取 sdk 配置
Wechath 提供了通用的获取配置的接口：

### 页面中引入微信脚本

在需要调用JS接口的页面引入如下JS文件，（支持https）：`http://res.wx.qq.com/open/js/jweixin-1.2.0.js`

### 获取配置参数
Wechath 提供了获取 js-sdk 配置参数的方法，用户通过调用此接口可以拿到需要的配置参数，并重定向到指定页面。

接口地址：`/auth/getJsApi`

请求类型：`GET`

请求参数：
```javascript
{
  "url":"https://wechath.zhengqingxin.com"
}
```
### 页面中初始化js-sdk

通过上述接口，可以在 url 中取到相关配置，我们就可以初始化了。例如：

```
// 获取配置，包括 appId,timestamp,nonceStr,signature
var conf = queryUrlParam();
conf.debug = true;
conf.jsApiList = ['chooseImage'];
wx.config(conf);
```