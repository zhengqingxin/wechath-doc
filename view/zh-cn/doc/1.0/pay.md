## 介绍
这里介绍如何在微信浏览器里面打开 H5 网页中执行 JS 调起微信支付。

详细流程图查看 [这里](https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=7_4)。想必很多人第一次看流程图都懵了吧，没关系，Wechath 中大部分工作都已经做好了，下面介绍如何使用 Wechath 来轻松接入微信支付。

### 前期工作

#### 到商户平台配置安全域名

因为微信支付需要得到用户的`openId`,所以需要网页授权，如果你的公众号还没有实现网页授权，请查看 [这里](/zh-cn/doc/1.0/wechat-auth.html) 先进行网页授权配置。配置好网页授权之后，在 [微信商户平台](pay.weixin.qq.com) 设置您的公众号支付支付目录，具体查看 [这里](https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=7_3)。

### 开发
开发之前我们先来梳理一下流程，了解流程我们不妨从后往前推，如 [微信官方文档](https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=7_7&index=6) 所介绍的，简化点来说我们在页面里只需执行下面代码就可以调起微信支付：
```js
  WeixinJSBridge.invoke(
    'getBrandWCPayRequest', {
        "appId":"wx2421b1c4370ec43b",     //公众号名称，由商户传入     
        "timeStamp":"1395712654",         //时间戳，自1970年以来的秒数     
        "nonceStr":"e61463f8efa94090b1f366cccfbbb444", //随机串     
        "package":"prepay_id=u802345jgfjsdfgsdg888",     
        "signType":"MD5",         //微信签名方式：     
        "paySign":"70EA570631E4BB79628FBCA90534C63FF7FADD89" //微信签名 
    },
    function(res){     
        if(res.err_msg == "get_brand_wcpay_request:ok" ) {}     // 使用以上方式判断前端返回,微信团队郑重提示：res.err_msg将在用户支付成功后返回    ok，但并不保证它绝对可靠。 
    }
  ); 
```
看到如上代码，如果你是前端同学，看到这些一定很开心，坐等服务端给你吐这几参数就好了。那么服务端可能就要头疼了，这些参数从哪里取？都是啥意思？不用担心，Wechath 来提供。

#### 获取微信支付所需参数
接口地址：`/order/pay`

请求类型：`POST`

参数示例：
```javascript
{
  "device_info":"WEB",
  "body": "测试商品",       //商品描述
  "out_trade_no": Date.now(),
  "total_fee": 1,         //金额，单位：分
  "spbill_create_ip": "218.43.221.23",
  "notify_url": "https://yourdomain.com/order/payResult",//支付结果通知接口
  "openid": "o-GHl046USRVooisvggilFGd5VX4"
}
```
此接口会返回所有客户端需要的参数，原样返回即可。

#### 支付结果通知
支付后，微信会通知到 `notify_url` 接口。需要注意几点：
* 微信会传递一个 xml 的参数，参数例如：
```
<xml>
    <appid><![CDATA[wx2421b1c4370ec43b]]></appid>
    <attach><![CDATA[支付测试]]></attach>
    <bank_type><![CDATA[CFT]]></bank_type>
    <fee_type><![CDATA[CNY]]></fee_type>
    <is_subscribe><![CDATA[Y]]></is_subscribe>
    <mch_id><![CDATA[10000100]]></mch_id>
    <nonce_str><![CDATA[5d2b6c2a8db53831f7eda20af46e531c]]></nonce_str>
    <openid><![CDATA[oUpF8uMEb4qRXf22hE3X68TekukE]]></openid>
    <out_trade_no><![CDATA[1409811653]]></out_trade_no>
    <result_code><![CDATA[SUCCESS]]></result_code>
    <return_code><![CDATA[SUCCESS]]></return_code>
    <sign><![CDATA[B552ED6B279343CB493C5DD0D78AB241]]></sign>
    <sub_mch_id><![CDATA[10000100]]></sub_mch_id>
    <time_end><![CDATA[20140903131540]]></time_end>
    <total_fee>1</total_fee>
    <trade_type><![CDATA[JSAPI]]></trade_type>
    <transaction_id><![CDATA[1004400740201409030005092168]]></transaction_id>
</xml>
```

* 对后台通知交互时，如果微信收到商户的应答不是成功或超时，微信认为通知失败，微信会通过一定的策略定期重新发起通知，尽可能提高通知的成功率，但微信不保证通知最终能成功。**所以同样的通知可能会多次发送给商户系统。商户系统必须能够正确处理重复的通知。**参见[微信文档](https://pay.weixin.qq.com/wiki/doc/api/native.php?chapter=97)。
* 为了防止微信不断发起通知，需要作出成功相应，返回也是xml，例如：
```
<xml>
    <return_code><![CDATA[SUCCESS]]></return_code>
    <return_msg><![CDATA[OK]]></return_msg>
</xml>
```
* 一定要做**签名验证,并校验返回的订单金额是否与商户侧的订单金额一致**，防止数据泄漏导致出现“假通知”。Wechth 提供了 [生成签名](/zh-cn/doc/1.0/pay.html#toc-e0c) 的接口，你需要将获取到的签名和微信返回的对比，如果签名一致，再进行后续业务操作，代码示例：
```js
 async payResultAction() {
    // 微信 post 的数据
    const data = this.post();
    // 转化为json
    const json = global.parseXmlToJson(data);
    // 从 Wechath 获取 sign
    const sign = await getSignFromWechath(json)
    if(sign === json.sign){
      // do something
    }
  }
```

### 生成签名接口
接口地址：`/order/getPaySign`

请求类型：`POST`

参数示例：
```javascript
{
  //将微信支付通知的结果转化成json后原样传递即可  
  "appid":"WEB",
  "attach": "测试商品",       //商品描述
  "bank_type": Date.now(),
  "total_fee": 1,         //金额，单位：分
  "is_subscribe": "218.43.221.23",
  "openid": "https://yourdomain.com/order/payResult",//支付结果通知接口
  "out_trade_no": "o-GHl046USRVooisvggilFGd5VX4",
  ...
}
```