---
title: 【译】前端开发人员的10个安全提示
date: 2020-04-27 21:20:12
img: https://weixin-storage.oss-cn-shanghai.aliyuncs.com/202004/10-security-tips/1.png
categories:
  - 技术
tags:
  - 前端
  - 翻译
---

Web 安全是前端开发人员经常忽略的主题。当我们评估网站的质量时，我们通常会查看性能，SEO 友好性和可访问性等指标，而网站抵御恶意攻击的能力却常常被忽略。

<!-- more -->

即使敏感的用户数据存储在服务器端，后端开发人员也必须采取重要措施来保护服务器，但最终，保护数据的责任在后端和前端之间共享。虽然敏感数据可能被安全地锁在后端仓库中，但前端掌握着前门的钥匙，窃取它们通常是获得访问权限的最简单方法。

> **后端和前端之间共同承担保护用户数据的责任。**

恶意用户可以采取多种攻击手段来破坏我们的前端应用程序，但是幸运的是，我们只需使用几个正确配置的响应头并遵循良好的开发实践，就可以在很大程度上减轻此类攻击的风险。在本文中，我将介绍 10 种简单的操作，可以通过这些简单的操作来改善对 Web 应用程序的保护。

## 测量结果

在我们开始改善网站安全性之前，重要的一点是要对我们所做更改的有效性提供反馈。虽然量化构成“良好开发实践”的内容可能比较困难，但是可以相当准确地度量安全头的强度。就像我们使用 Lighthouse 获取性能，SEO 和可访问性分数一样，我们可以使用 [SecurityHeaders.com](https://securityheaders.com/)根据当前响应头获取安全分数。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202004/10-security-tips/1.png)

SecurityHeaders 可以给我们的最高分是“A+”，我们应该始终为此努力。

## 关于响应头的说明

处理响应头曾经是后端的任务，但是如今，我们经常将 Web 应用程序部署到 Zeit 或 Netlify 等“无服务器”云平台，并配置它们以返回正确的响应标头成为前端责任。确保了解你的云托管提供商如何使用响应头，并进行相应配置。

下面来看一下具体的安全措施有哪些。

### 1.使用强大的内容安全策略

完善的内容安全策略(CSP)是前端应用程序安全的基石。CSP 是浏览器中引入的一种标准，用于检测和缓解某些类型的代码注入攻击，包括跨站点脚本（XSS）和点击劫持。

强 CSP 可以禁用可能有害的内联代码执行，并限制加载外部资源的域。可以通过将 `Content-Security-Policy` 头设置为以分号分隔的指令列表来启用 CSP。如果你的网站不需要访问任何外部资源，一个良好的头的起始值可能是这样的：

```
Content-Security-Policy: default-src 'none'; script-src 'self'; img-src 'self'; style-src 'self'; connect-src 'self';
```

在这里，我们将`script-src`、`img-src`、`style-src` 和 `connect-src` 指令设置为 `self`，以指示所有脚本、图像、样式表和 fetch 调用都应该被限制在 HTML 文档提供服务的同一来源。其他任何未明确提及的 CSP 指令将回退到 `default-src` 指令指定的值。我们将其设置为 `none` 表示默认行为是拒绝任何 URL 的连接。

然而，如今几乎任何 web 应用程序都不是独立的，所以你可能要调整这个头，以便你可以使用其他信任域，如域名 Google Fonts 或 AWS S3 bucket，但始终最好从以下开始最严格的政策，并在需要时稍后放宽。

您可以在[MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy)网站上找到 CSP 指令的完整列表。

### 2.启用 XSS 保护模式

如果用户输入确实注入了恶意代码，我们可以通过提供 `"X-XSS-Protection": "1; mode = block"` 头指令来指示浏览器阻止响应。

尽管大多数现代浏览器默认情况下都启用了 XSS 保护模式，并且我们也可以使用内容安全策略来禁用内联 JavaScript，但仍建议包含 `X-XSS-Protection`头，以确保不使用内联 JavaScript 的旧版浏览器具有更好的安全性。

### 3.禁用 iframe 嵌入以防止点击劫持攻击

点击劫持是一种攻击，网站 A 上的用户被诱骗对网站 B 执行某些操作。为了实现这一点，恶意用户将网站 B 嵌入到一个不可见的 iframe 中，然后将 iframe 放置在网站 A 上毫无防备的用户的光标之下，因此当用户单击，或者更确切地说，认为他们单击了网站 A 上的元素时，他们实际上是单击了网站 B 上的某个东西。

我们可以通过提供 `X-Frame-Options` 响应头来防止此类攻击，该响应头禁止在框架中呈现网站：

```
"X-Frame-Options": "DENY"
```

另外，我们可以使用`frame-ancestors` CSP 指令，该指令可以更好地控制父级可以或不能将页面嵌入 iframe 的程度。

### 4.限制对浏览器功能和 API 的访问

良好的安全做法的一部分是，限制对正确使用我们的网站所不需要的任何内容的访问。我们已经使用 CSP 应用了这个原则来限制网站可以连接的域的数量，但是它也可以应用到浏览器特性上。

我们可以使用 `Feature-Policy` 头指示浏览器拒绝访问我们的应用不需要的某些功能和 API。

我们将 `Feature-Policy` 设置为由分号分隔的一串规则，其中每个规则都是功能的名称，后跟其策略名称。

```
"Feature-Policy": "accelerometer 'none'; ambient-light-sensor 'none'; autoplay 'none'; camera 'none'; encrypted-media 'none'; fullscreen 'self'; geolocation 'none'; gyroscope 'none'; magnetometer 'none'; microphone 'none'; midi 'none'; payment 'none'; picture-in-picture 'none'; speaker 'none'; sync-xhr 'none'; usb 'none'; vr 'none';"
```

[Smashing Magazine](https://www.smashingmagazine.com/2018/12/feature-policy/)上有一篇很棒的文章，详细介绍了功能策略，但大多数情况下，您希望为所有不使用的特性设置 `none`。

### 5.不要泄露 referrer 值

当你点击一个链接，从你的网站导航，目的地网站将收到你的网站上最后一个位置的 URL 在一个 `referrer` 头。该 URL 可能包含敏感数据和半敏感数据（例如会话令牌和用户 ID），这些数据永远都不应公开。

为了防止`referrer` 值泄漏，我们将 `Referrer-Policy` 标头设置为 `no-referrer` ：

```
"Referrer-Policy": "no-referrer"
```

在大多数情况下，这个值应该是不错的，但是如果您的应用程序逻辑要求您在某些情况下保留 `referrer`，请查看[Scott Helme](https://scotthelme.co.uk/a-new-security-header-referrer-policy/)撰写的这篇文章，在这篇文章中，他分解了所有可能的头值以及何时应用它们。

### 6.不要根据用户输入设置 innerHTML 值

跨站点脚本攻击可以通过许多不同的 DOM API 进行，其中恶意代码被注入到网站中，但是最常用的是 `innerHTML`。

我们永远不应基于用户未过滤的输入来设置 `innerHTML`。用户可以直接操作的任何值——输入字段中的文本、URL 中的参数或本地存储项——都应该首先进行转义和清除。理想情况下，使用 textContent 而不是 innerHTML 可以完全避免生成 HTML 输出。如果确实需要为用户提供富文本编辑，请使用专业的第三方库。

不幸的是，`innerHTML` 并不是 DOM API 的唯一弱点，而且容易受到 XSS 注入攻击的代码仍然难以检测。这就是为什么一定要有一个严格的不允许内联代码执行的内容安全策略。

### 7.使用 UI 框架

诸如 React，Vue 和 Angular 之类的现代 UI 框架内置了良好的安全性，可以很大程度上消除 XSS 攻击的风险。它们自动对 HTML 输出进行编码，减少对 XSS 敏感 DOM API 的使用，并为潜在危险的方法（如`dangerouslySetInnerHTML`）提供明确而谨慎的名称。

### 8.保持你的依赖关系是最新的

快速浏览一下 `node_modules` 文件夹，就会确认我们的 web 应用程序是由数百(如果不是数千)个依赖项组成的 lego 拼图。确保这些依赖项不包含任何已知的安全漏洞对于网站的整体安全非常重要。

确保依赖关系保持安全和最新的最佳方法是使漏洞检查成为开发过程的一部分。为此，您可以集成[Dependabot](https://dependabot.com/)和[Snyk](https://snyk.io/)之类的工具，这些工具将为过时或潜在易受攻击的依赖项创建提取请求，并帮助您更快地应用修补程序。

### 9.添加第三方服务前请三思

第三方服务如 Google Analytics、Intercom、Mixpanel 等，可以为您的业务需求提供“一行代码”的解决方案。同时，它们会使你的网站更容易受到攻击，因为如果第三方服务受到损害，那么你的网站也会受到损害。

如果你决定集成第三方服务，请确保设置最强大的 CSP 策略，该策略仍将允许该服务正常运行。大多数流行的服务都记录了它们要求的 CSP 指令，因此请确保遵循其准则。

在使用 Google Tag Manager、Segment 或任何其他允许组织中任何人集成更多第三方服务的工具时，应该特别注意。有权使用此工具的人员必须了解连接其他服务的安全隐患，并且最好与开发团队进行讨论。

### 10.对第三方脚本使用子资源完整性

对于您使用的所有第三方脚本，请确保在可能的情况下包括 `integrity` 属性。浏览器具有 [Subresource Integrity](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity) 功能，该功能可以验证您正在加载的脚本的加密哈希，并确保它未被篡改。

你的 script 标签如下所示：

```html
<script
  src="https://example.com/example-framework.js"
  integrity="sha384-oqVuAfXRKap7fdgcCY5uykM6+R9GqQ8K/uxy9rx7HNQlGYl1kPzQho1wx4JwY8wC"
  crossorigin="anonymous"
></script>
```

值得说明的是，此技术对第三方库有用，但对第三方服务的作用较小。大多数情况下，当你为第三方服务添加脚本时，该脚本仅用于加载另一个从属脚本。无法检查依赖脚本的完整性，因为可以随时对其进行修改，因此在这种情况下，我们必须依靠严格的内容安全策略。

---

原文：[https://levelup.gitconnected.com/10-security-tips-for-frontend-developers-19e3dd9fb069](https://levelup.gitconnected.com/10-security-tips-for-frontend-developers-19e3dd9fb069)

作者：[Konstantin Lebedev](https://levelup.gitconnected.com/@koss_lebedev?source=post_page-----19e3dd9fb069----------------------)
