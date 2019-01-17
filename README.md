微信第三方平台 Node 库 API，ES6 版本，基于[co-wechat-api](https://github.com/node-webot/co-wechat-api) 3.8.2 版本(ES6 版)和[co-wechat-open-api](https://github.com/liwenyue/co-wechat-open-api)2.0.6(ES6 版)

## 功能列表
- 发送客服消息（文本、图片、语音、视频、音乐、图文）
- 菜单操作（查询、创建、删除、个性化菜单）
- 二维码（创建临时、永久二维码，查看二维码 URL）
- 分组操作（查询、创建、修改、移动用户到分组）
- 用户信息（查询用户基本信息、获取关注者列表）
- 媒体文件（上传、获取）
- 群发消息（文本、图片、语音、视频、图文）
- 客服记录（查询客服记录，查看客服、查看在线客服）
- 群发消息
- 公众号支付（发货通知、订单查询）
- 微信小店（商品管理、库存管理、邮费模板管理、分组管理、货架管理、订单管理、功能接口）
- 模版消息
- 网址缩短
- 语义查询
- 数据分析
- JSSDK 服务端支持
- 素材管理
- 摇一摇周边

## Installation

```sh
$ npm install es-wechat-open-api
or
$ npm install https://github.com/ZyqGitHub1/co-wechat-open-api.git
```

## Usage

### 网络请求处理

```javascript
// TODO:
// 1.将微信定时推送的componentTicket存储在redis之中
// 示例代码如下
componentTicketRequest => {
  await this.redis.set(
    'componentTicket',
    componentTicketRequest.componentVerifyTicket
  );
};

// 2.在授权成功的回调处理部分将授权方的accessToken和refreshToken存储在redis之中
// 示例代码如下
authorizerCallbackRequest => {
  const authorizerAppId =
    authorizerCallbackRequest.authorization_info.authorizer_appid;
  const accessToken =
    authorizationInfo.authorizerCallbackResponse.authorizer_access_token;
  const expiresIn = authorizerCallbackRequest.authorization_info.expires_in;
  const refreshToken =
    authorizerCallbackRequest.authorization_info.authorizer_refresh_token;
  const accessTokenInstance = {
    accessToken,
    expireTime: new Date().getTime() + (expiresIn - 20) * 1000
  };
  await redis.set(
    `authorizerAppToken:${authorizerAppId}`,
    JSON.stringify({ accessTokenInstance, refreshToken })
  );
};

```

### 调用第三方平台api

```js
// IMPORT:
// 必须在保证redis数据库中已经存在网络请求处理部分获取到的
// componentTicket，accessTokenInstance，refreshToken
const { ComponentAPI, API } = require('co-wechat-open-api');
const Redis = require('ioredis');
const redis = new Redis();

const componentAPI = new ComponentAPI({
  componentAppId: 'componentAppId',
  componentAppSecret: 'componentAppSecret',
  getComponentTicket: async () => {
    const ticket = await this.redis.get('componentTicket');
    return ticket;
  },
  getComponentToken: async () => {
    const ticket = await this.redis.get('componentToken');
    return ticket;
  },
  saveComponentToken: async componentToken => {
    await this.redis.set('componentToken', JSON.stringify(componentToken));
  }
});


const wechatOpenApi = new API({
  componentApi: componentAPI,
  authorizerAppId: 'authorizerAppId',
  getToken: async () => {
    const tokens = await redis.get('authorizerAppToken:authorizerAppId');
    return JSON.parse(tokens);
  },
  saveToken: async () => {
    await redis.set(
      'authorizerAppToken:authorizerAppId',
      JSON.stringify({ accessToken, refreshToken })
    );
  }
});

(async () => {
  const menuConfig = await wechatOpenApi.getMenuConfig();
  console.log(menuConfig);
})();
```

## 详细 API

原始 API 文档请参见：[消息接口指南](http://mp.weixin.qq.com/wiki/index.php?title=消息接口指南)。

更多内容请移步至[github](https://github.com/ZyqGitHub1/co-wechat-open-api.git)

## License

The MIT license.
