---
title: 封装axios
date: 2022-03-02 15:05:34
updated: 2022-03-02 15:05:34
categories: 技术
tags: axios
---

### 背景

axios是业界比较好的ajax调用封装，但是项目在使用上还是有些繁琐，因此在业务使用上再一次封装axios

### 目的

1. 增加接口时候能够通过配置一个config，快速增加接口
2. 接口分为注册和调用，将接口转为一个函数来进行调用
3. 请求拦截统一做处理，比如加请求头
4. 响应拦截做状态码错误处理
5. 快速切换mock与实际接口
6. 通过id取消请求

### 目录结构

.

├── api

│  ├── index.js // 获取单例，注册接口

│  ├── login.js  // 接口配置

│  └── single-api.js // 单例，提供注册和调用接口的函数

├── index.js // 抛出axios调用

└── plugins

  ├── handleError.js // 错误处理

  ├── handleResponse.js // 成功处理

  └── setConfig.js // 提供基本axios配置，merge请求时候动态修改的配置，比如请求头

### 使用流程

![](https://raw.githubusercontent.com/liuyu666/md-img/master/axios%E4%BD%BF%E7%94%A8%E6%B5%81%E7%A8%8B%E5%9B%BE.png)

### 类图

![img](http://www.plantuml.com/plantuml/png/HOv12i9034NtEKMMBQLtgBXouKPF4ATnYBIPSP8AKdftfr1mzNd_mUSFvbHycIS84vdXNJHEVCg2AwpLkOGDFqb6ZEmNDN8D3FrC6IdBbJ9qL0fzTpA0BZrU71mBHp7dqXnSxJdfKs8B_L69xd7ZzyBcpN-mXGq6rh5--W40)

###### 使用

```js
// login.js

export default {
 getBanner: { method: 'get', url: '/home/banner' },
 login: { method: 'post', url: '/v1/dashboard/login' },
};
```

```js
// 全局services

import loginConfig from './login';
const SingleApi = biz.vmi.SingleApi; // 项目启动之前挂载在全局

SingleApi.getInstance().register(loginConfig);
```

```js
// 页面请求

const SingleApi = biz.vmi.SingleApi;
const apis = SingleApi.getInstance().apis;
const data = await apis.login(this.loginForm);
```

### 优化开发流程

在开发过程中，我们一般是跟服务端约定好接口定义，这个时候我们可以通过mock来先进行开发，走通整个开发流程，这里使用的是eolinker，在这个平台上定义好请求以及返回格式，可以进行mock

这里的做法是在接口config这里加一个isMock字段，然后在，实际发起请求的request那里修改传参方式，以及在webpack的devServer设置proxy，将数据代理到mock服务器地址

```js
export default {
 login: { method: 'post', url: '/v1/dashboard/login', isMock: true }, // isMock
 getConfig: { method: 'get', url: '/v1/dashboard/config', isMock: true },
 getCaptcha: { method: 'get', url: '/v1/dashboard/captcha', isMock: true },
};
```

**webpack 配置**

```
module.exports = {
  devServer: {
    proxy: {
      "/v1": {
        "target": "mock地址",
        "changeOrigin": true,
        "pathRewrite": { "^/v1": "" },
        "secure": true
      },
    },
  },
};
```

**发起请求的地方**

```js
// 这里对于mock数据的时候，做了一些处理
_injectRequest (apiMap) {
    const requestMap = {};
    for (let [key, value] of apiMap) {
      let { method, url, config = {}, isMock = false } = value;
      method = method.toUpperCase();
      requestMap[key] = (dataOrParams = {}, instanceConf = {}) => {
        const keyName = isMock ?
          'params'
          : ['PUT', 'POST', 'PATCH'].includes(method) ? 'data' : 'params';
        return axios.request({
          method,
          url: isMock ? '/v1' : url,
          // [keyName]: method === 'POST' ? qs.stringify(dataOrParams) : dataOrParams,
          [keyName]: isMock ? { uri: url, ...dataOrParams} : dataOrParams,
          ...Object.assign(config, instanceConf)
        });
      };
    }
    return requestMap;
  }

```

##### 实际的效果就是

- 项目起了一个端口是8088，发请求的时候是 -> localhost:8088/v1/dashboard/login

- 因为webpack配置了代理，并且将'/v1'替换为 ‘’，就会转发到 →mock地址/dashboard/login

- 再看实际发起请求时候，当mock时候，会将路径重写为v1，将实例路径通过params传值，也就是?uri=/v1/dashboard/login

- 所以login这个请求，从 localhost:8088/v1/dashboard/login?username=admin 变为 mock地址?uri=/v1/dashboard/login&username=admin

参考文章： <https://segmentfault.com/a/1190000027078266>
