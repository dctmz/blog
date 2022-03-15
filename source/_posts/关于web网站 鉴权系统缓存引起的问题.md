---
title: 关于web网站 鉴权系统缓存引起的问题 多tab 页 "状态" 未实时同步
tags: []
categories: []
date: 2022-03-12 16:52:12
---

在开发单页面应用的时候，碰到了用户新开了一个页面，并重新登录一个新账号，老页面数据发生错误的问题。

<!-- more -->

## 背景

在开发项目中碰到了如下问题：

1.  登录页提交登录
2.  服务器返回 token
3.  前端利用浏览器的缓存机制缓存 token（技术上的选择有 cookie, localStorage, sessionStorage，通常的技术方案会选择 localStorage）
4.  用 token 获取当前账号的 user data，并保存 userData 写入计算机内存中。
5.  跳转至网站首页
6.  新开一个浏览器 tab 页，输入网址
7.  前端从 localStorage get token
8.  同 第 4 步（此时 token 一定能获取到并且有效）
9.  跳转至当前路径页面（有可能是 404）
10. 任意一个 tab 页 登出**!!! 此时会导致 2 个 tab 页状态不一致：**（用 `tab-1` `tab-2` 来表示打开的 2 个浏览器 tab 页）

- **tab-1 是登出状态**
- **tab-2 是登录状态** （看起来是登录状态，实际上 localStorage 中的数据 已经被 clear）
  **假如此时 **tab-1** 登录另一个账号，按照此前的登录逻辑，那么 new token 会被写入 localStorage 中。**

### 此时的问题如下所示:

```javascript
localSotrage -> new token
tab-1 -> new userData
tab-2 -> old userData
```

---

### 解决问题的思路

我们希望在当前用户登录状态发生变化时，实时通知其他 tab 页更新登录状态
`token`是系统鉴权的唯一令牌，和`登录态`密切相关。`登录态`的变化也就体现为`token`的变化
**因此我们需要做的是: 当`token`发生变化，实时通知其他 tab 页更新`token`**

#### Q1. 如何通知?

<mark>核心问题: 浏览器多 tab 页(同源)之间通信</mark>

1. websocket
2. setInterval + cookie
3. web storage event
4. BroadCast Channel
5. Shared Worker
6. Service Worker
7. IndexedDB

经过阅读资料和文档，比较合适的方案是 3 和 4，也就是`localStorage`的`storage`事件和`BroadcastChannel API`。它们都可以做跨 tab 页面通信，`BroadcastChannel`可以自定义事件。`storage`则是当`localStorage`发生变化触发。

由于`token`是存储在`localStorage`中，因此监听`storage`事件是最合适的。只要`token`被改变`storage`事件就会被触发。

#### Q2. 如何更新登录状态?

根据业务场景，我们选择简单粗暴的方式: `window.location.reload()`，被通知的 tab 页直接刷新。
被刷新的页面程序逻辑:

1. 从`localStorage`获取`token`
2. 用`token`获取用户信息`user data`
3. `token`无效，获取数据失败，则清空缓存，跳转至登录页
4. 成功获取`user data`，则当前页更新为新的用户信息

```javascript
window.addEventListener("storage", (event) => {
  const { key, oldValue, newValue } = event;
  if (key === token && oldValue !== newValue) {
    window.location.reload();
  }
});
```

## 参考文档:

- [浏览器多个标签页之间的通信](https://juejin.cn/post/6844903614125719560)
- [浏览器多页面间的通信](https://juejin.cn/post/6999583696894296100)
- [前端跨页面通信：Broadcast Channel](https://www.jianshu.com/p/12bb9acd9165)
- [BroadcastChannel](https://developer.mozilla.org/zh-CN/docs/Web/API/BroadcastChannel)
- [storage](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/storage_event)
