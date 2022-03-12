---
title: 关于web网站 鉴权系统缓存引起的问题
tags: []
categories: []
date: 2022-03-12 16:52:12
---

### 多tab页 "状态" 未实时同步

>#### 场景一 
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
>
>#### 解决问题的思路
><u>我们希望在当前用户登录状态发生变化时，实时通知其他tab页更新登录状态</u>
> * Q1. 如何通知?
>
> * Q2. 如何更新登录状态?
>