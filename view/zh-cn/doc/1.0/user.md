### 简介
以下接口提供了用户相关的操作。唯一可能需要解释的是 `OpenId`：加密后的微信号，每个用户对每个公众号的OpenID是唯一的。

### 获取用户列表
接口地址：`/user/getAll`

请求类型：`GET`

参数示例：
```javascript
// next_openid：第一个拉取的OPENID，不填默认从头开始拉取
{
  "next_openid":"o-GHl046UPRVoaifvgsilDGd5VX4" 
}
```

### 获取用户详情
接口地址：`/user/getDetail`

请求类型：`GET`

参数示例：
```javascript
{
  "opneid":"o-GHl046UPRVoaifvgsilDGd5VX4" 
}
```

### 备注用户
接口地址：`/user/remark`

请求类型：`POST`

参数示例：
```javascript
{
	"openid":"o-GHl046UPRVoaifvgsilDGd5VX4",
	"remark":"备注"
}
```

### 获取黑名单列表
接口地址：`/user/getBlackList`

请求类型：`GET`

### 批量拉黑用户
接口地址：`/user/blackUser`

请求类型：`POST`

参数示例：
```javascript
{
	"openid_list":["o-GHl046UPRVooifvggilFGd5VX4"]
}
```

### 批量取消拉黑用户
接口地址：`/user/unBlackUser`

请求类型：`POST`

参数示例：
```javascript
{
	"openid_list":["o-GHl046UPRVooifvggilFGd5VX4"]
}
```