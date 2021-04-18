---
title: HTML在锚点元素（链接）上定义了ping属性是干什么用的？
date: 2021-04-16 20:40:24
categories:
  - HTML5&CSS3
summary: "ping属性提供了一种实现“链接点击跟踪”的轻巧方式"
---

## 锚点元素上的 ping 属性

锚元素的 `ping` 属性接受以空格分隔的 URL 列表。当锚定义 ping URL 并有人单击它时，浏览器将向这些指定的 URL 发送 `POST` 请求。如果您要跟踪和分析用户如何与您的网站进行交互，此功能可能会有所帮助。

<!-- more -->

```html
<a
  href="https://www.stefanjudis.com/popular-posts/"
  ping="https://www.stefanjudis.com/tracking/"
  >Read popular posts</a
>
```

要查看其运行情况，请打开您的开发人员工具，然后单击下面的链接。👇

[Read popular posts](https://www.stefanjudis.com/popular-posts/)

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/ping-attribute-on-anchor/1.jpeg)

实际上，当您在开发人员工具中打开 network 面板时(启用“保存日志”来查看浏览器导航到新 URL 后的请求)，您会在单击“ping 链接”后看到一个请求飞向定义的 URL。

POST 请求的有效载荷是单字 `PING`。`ping-to` 请求头中保存着链接的目的地，另外还有 `user-agent` 等附加信息。有趣的是， `content-type` 是 `text/ping`。🙈

总而言之，`ping` 属性提供了一种实现“链接点击跟踪”的轻巧方式。这是否意味着您今天就可以使用它？

## 浏览器对 ping 属性的支持

在 MDN 上查看属性的浏览器兼容性表时，您会发现浏览器支持还不错。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/ping-attribute-on-anchor/2.jpeg)

Chromium 浏览器（Chrome、Edge 等）支持。火狐浏览器的支持是在浏览器功能标志（browser.send_pings）后面，而 Safari...。Safari 被错误地标注为不支持。

## 为什么没人使用 ping？

我是原生 HTML 解决方案的忠实拥护者。问题是为什么没有人使用 `ping` 属性（或者您知道使用它的网站吗？）？

我只能在这里推测，但一个原因可能是用户分析主要由第三方提供商（例如 Google Analytics（分析））驱动。

作为一个 Web 开发者，要使用这些，你所要做的就是在你的网站中嵌入一个脚本。JavaScript 将跟踪所有的用户行为，并且没有任何基础的设置就能工作。

如果您的跟踪基于 `ping` 属性，则必须调整站点上的所有链接。这个过程包括更多的维护和开发工作，这是反对使用 `ping` 属性的有力论据。

---

翻译自[www.stefanjudis.com](https://www.stefanjudis.com/today-i-learned/html-defines-a-ping-attribute-on-anchor-elements-links/)
