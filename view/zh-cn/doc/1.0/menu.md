### 介绍
菜单类型请参考 [微信官方文档](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421141013)。

### 添加默认菜单

接口地址：`/menu/addDefault`

请求类型：`POST`

参数示例：
```javascript
 {
    "button":[
      {	
        "type":"click",
        "name":"今日歌曲",
        "key":"V1001_TODAY_MUSIC"
      },
      {
        "name":"菜单",
        "sub_button":[
          {	
            "type":"view",
            "name":"搜索",
            "url":"http://www.soso.com/"
          },
          {
            "type":"miniprogram",
            "name":"wxa",
            "url":"http://mp.weixin.qq.com",
            "appid":"wx286b93c14bbf93aa",
            "pagepath":"pages/lunar/index"
          },
          {
            "type":"click",
            "name":"赞一下我们",
            "key":"V1001_GOOD"
          }
        ]
      }
  ]
 }
```

### 菜单列表

接口地址：`/menu/get`

请求类型：`GET`

### 删除所有菜单

接口地址：`/menu/delete`

请求类型：`GET`

### 添加个性化菜单

个性化菜单：让公众号的不同用户群体看到不一样的自定义菜单。注意，添加个性化菜单之前一定要有默认菜单。

接口地址：`/menu/addConditional`

请求类型：`POST`

请求参数：

```
{
  "button": [
    {
      "type": "click",
      "name": "今日歌曲",
      "key": "V1001_TODAY_MUSIC"
    },
    {
      "name": "菜单",
      "sub_button": [
        {
          "type": "view",
          "name": "搜索",
          "url": "http://www.soso.com/"
        },
        {
          "type": "miniprogram",
          "name": "wxa",
          "url": "http://mp.weixin.qq.com",
          "appid": "wx286b93c14bbf93aa",
          "pagepath": "pages/lunar/index"
        },
        {
          "type": "click",
          "name": "赞一下我们",
          "key": "V1001_GOOD"
        }]
    }],
  "matchrule": {
    "tag_id": "2",
    "sex": "1",
    "country": "中国",
    "province": "广东",
    "city": "广州",
    "client_platform_type": "2",
    "language": "zh_CN"
  }
}
```