---
title: fix moment Deprecation warning
tags: [moment, js, warning]
categories: [开发]
date: 2021-12-30 10:13:26
---

在开发后台也页面的时候，发现控制台报了如下警告 ⚠️

```
react_devtools_backend.js:4045 Deprecation warning: value provided is not in a recognized RFC2822 or ISO format. moment construction falls back to js Date(), which is not reliable across all browsers and versions. Non RFC2822/ISO date formats are discouraged. Please refer to http://momentjs.com/guides/#/warnings/js-date/ for more info.
Arguments:
[0] _isAMomentObject: true, _isUTC: false, _useUTC: false, _l: undefined, _i: 2021/12/21, _f: undefined, _strict: undefined, _locale: [object Object]
Error
```

![](https://gitee.com/dctxf/pic/raw/master/1640830703708-1640830703698.png)

<!-- more -->

## 原因

翻译了一下，大概意思是

> 弃用警告：提供的值不是可识别的 RFC2822 或 ISO 格式。Moment 构造退回到 js date()，它在所有浏览器和版本中都不可靠。不推荐使用非 RFC2822/ISO 日期格式。有关更多信息，请访问http://momentjs.com/guides/#/warnings/js-date/。

后来找了一下后端返回的时间格式为 "YYYY/MM/DD"

## 解决方案

1. ### 重新格式化

   ```js
   const str = "2021/11/11";
   moment(str.replace("/", "-")).format("YYYY-MM-DD");
   ```

2. ### 关闭提示

   ```js
   moment.suppressDeprecationWarnings = true;
   ```

3. ### 增加时间构造

   ```js
   const str = "2021/11/11";
   moment(new Date(str)).format("YYYY-MM-DD");
   ```

4. ### 🔥 后端改为正确格式(推荐)

   追其根本是后端返回格式和前端要的格式不匹配，所以改为正确格式才能一劳永逸

## 参考资料

- https://momentjs.com/guides/#/warnings/js-date/
- https://stackoverflow.com/questions/39969570/deprecation-warning-in-moment-js-not-in-a-recognized-iso-format
- https://en.wikipedia.org/?title=RFC_2822&redirect=no
