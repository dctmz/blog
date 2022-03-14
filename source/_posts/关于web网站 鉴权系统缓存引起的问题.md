---
title: 关于web网站 鉴权系统缓存引起的问题
tags: []
categories: []
date: 2022-03-12 16:52:12
---

## 多tab页 "状态" 未实时同步

>### 场景一 
>1. 登录页提交登录
>2. 服务器返回 token
>3. 前端利用浏览器的缓存机制缓存token（技术上的选择有 cookie, localStorage, sessionStorage，通常的技术方案会选择 localStorage）
>4. 用 token 获取当前账号的 user data，并保存 userData 写入计算机内存中。
>5. 跳转至网站首页
>6. 新开一个浏览器 tab 页，输入网址
>7. 前端从 localStorage get token 
>8. 同 第4步（此时 token 一定能获取到并且有效）
>9. 跳转至当前路径页面（有可能是404）
>10. 任意一个 tab 页 登出
>
><u>**!!! 此时会导致2个tab页状态不一致：**</u>（用 **tab-1** **tab-2** 来表示打开的2个浏览器tab页）
>* __tab-1 是登出状态__
>* __tab-2 是登录状态__ （<u>看起来是登录状态，实际上 localStorage 中的数据 已经被 clear</u>）
>
><u>__假如此时 **tab-1** 登录另一个账号，按照此前的登录逻辑，那么 new token 会被写入 localStorage 中。__</u>
><mark>此时的问题如下所示:</mark>
>```javascript
> localSotrage -> new token
>
> tab-1 -> new userData
> tab-2 -> old userData
>```
***
>### 解决问题的思路
><u>我们希望在当前用户登录状态发生变化时，实时通知其他tab页更新登录状态</u>
><u>```token```是系统鉴权的唯一令牌，和```登录态```密切相关。```登录态```的变化也就体现为```token```的变化</u>
><u>**因此我们需要做的是: 当```token```发生变化时，实时通知其他tab页更新```token```**</u>
> * Q1. 如何通知?
> <mark>核心问题: 浏览器多tab页(同源)之间通信</mark>
> <u>1. websocket</u>
> <u>2. setInterval + cookie</u>
> <u>3. web storage event</u>
> <u>5. BroadCast Channel</u>
> <u>4. Shared Worker</u>
> <u>6. Service Worker</u>
> <u>7. IndexedDB</u>
>经过阅读资料和文档，比较合适的方案是3和4，也就是```localStorage```的```storage```事件和```BroadcastChannel API```。它们都可以做跨tab页面通信，```BroadcastChannel```可以自定义事件。```storage```则是当```localStorage```发生变化时触发。
><u></u>
>由于```token```是存储在```localStorage```中，因此监听```storage```事件是最合适的。只要```token```被改变```storage```事件就会被触发。
><u></u>
>参考文档:
>https://juejin.cn/post/6844903614125719560
>https://juejin.cn/post/6999583696894296100
>https://www.jianshu.com/p/12bb9acd9165
>https://developer.mozilla.org/zh-CN/docs/Web/API/BroadcastChannel/BroadcastChannel
>https://developer.mozilla.org/zh-CN/docs/Web/API/Window/storage_event
>
> * Q2. 如何更新登录状态?
> <u>根据业务场景，我们选择简单粗暴的方式: ```window.location.reload()```，被通知的tab页直接刷新。</u>
> 被刷新的页面程序逻辑:
><u>1. 从```localStorage```获取```token```
><u>2. 用```token```获取用户信息```user data```
><u>3. ```token```无效，获取数据失败，则清空缓存，跳转至登录页
><u>4. 成功获取```user data```，则当前页更新为新的用户信息
>
>```javascript
window.addEventListener('storage', (event) => {
  const { key, oldValue, newValue } = event;
  if (key === token && oldValue !== newValue) {
    window.location.reload();
  }
});
```