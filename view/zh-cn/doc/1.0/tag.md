### 介绍
标签的概念相当于用户分组，通过对用户设定不同的标签，可以针对各个标签用户实现不同功能，比如实现用户在公众号里可以看到不同的菜单。以下是标签相关的 API，**所有 POST 接口都以 `application/json` 形式调用。**

### 获取所有标签    
接口地址：`/tag/get`

请求类型：`GET`

### 新增标签
接口地址：`/tag/post`

请求类型：`POST`

参数示例：
```javascript
{
  "tag" : {
    "name" : "北京"
  }
}
```
### 修改标签
接口地址：`/tag/edit`

请求类型：`POST`

参数示例：
```javascript
{
  "tag" : {
    "id": 101,
    "name" : "上海"
  }
}
```
### 删除标签
接口地址：`/tag/delete`

请求类型：`POST`

参数示例：
```javascript
{
  "tag":{
       "id" : 107
  }
}
```
### 获取标签下粉丝列表
接口地址：`/tag/getUser`

请求类型：`POST`

参数示例：
```javascript
{
  "tagid" : 108,
  "next_openid":""
}
```
### 批量为用户打标签
接口地址：`/tag/batchTag`

请求类型：`POST`

参数示例：
```javascript
{
  "openid_list" : [
    "o-GHl046UPRVooifvggilFGd5VX4",
    "o-GHl02rmUPxtANEgd0Oi0Ju8XLQ"
  ],
  "tagid" : 108
}
```
### 批量为用户取消标签
接口地址：`/tag/batchUnTag`

请求类型：`POST`

参数示例：
```javascript
{
  "openid_list" : [
    "o-GHl046UPRVooifvggilFGd5VX4",
    "o-GHl02rmUPxtANEgd0Oi0Ju8XLQ"
  ],
  "tagid" : 108
}
```