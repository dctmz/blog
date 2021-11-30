---
title: postman动态设置变量
tags: [postman, env]
categories: [开发工具]
date: 2021-11-30 16:35:30
---

本文介绍下我在开发中遇到了 postman 调试接口时，遇到了没个接口都需要带一个自定义的请求头，而且是动态获取的，这里记录下我是怎么做的

<!-- more -->

#### 原因

在开发中我碰到这种情况，每个请求接口都需要带上一个自定义的请求头，格式如下

```
"Access-Token":"token12345"
```

而且`token`是从登陆接口获取的，每半小时失效

这时候我设置了一个环境变量`{{TOKEN}}`

那么问题来了，我怎么动态设置这个 token 呢

#### 原理

postman是提供请求前置（pre-request Script）和请求后置（Tests）的，而且在里面可以设置变量，发送请求等动作

#### 方案

在Tests中设置，如下

```js
pm.test("设置token", function () {
  	// 获取登陆后的相应json
    var jsonData = pm.response.json();
  // 设置token
   pm.environment.set('TOKEN',jsonData.data.token)
});
```

此时当你每次发送请求的时候就会动态设置环境变量了
