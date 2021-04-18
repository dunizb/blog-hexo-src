---
title: 在Vue.js中加载字体的最佳做法
date: 2021-04-07 00:28:55
img: http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/best-practices-for-loading-fonts-in-vue/banner.png
categories:
  - 前端框架
tags:
  - Vue.js
summary: "探讨在 Vue 应用程序中加载字体的最佳实践"
---

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/best-practices-for-loading-fonts-in-vue/banner.png)

> 翻译自[blog.logrocket.com](https://blog.logrocket.com/best-practices-for-loading-fonts-in-vue/)，作者：Kelvin Gobo

添加字体不应该对性能产生负面影响。在本文中，我们将探讨在 Vue 应用程序中加载字体的最佳实践。

## 正确声明`font-face`的字体

确保正确声明字体是加载字体的重要方面。这是通过使用 `font-face` 属性来声明你选择的字体来实现的。在你的 Vue 项目中，这个声明可以在你的根 CSS 文件中完成。在进入这个问题之前，我们先来看看 Vue 应用的结构。

```
/root
  public/
    fonts/
      Roboto/
        Roboto-Regular.woff2
        Roboto-Regular.woff
    index.html
  src/
    assets/
      main.css
    components/
    router/
    store/
    views/
    main.js
```

我们可以像这样在 `main.css` 中进行 `font-face` 声明：

```css
// src/assets/main.css

@font-face {
  font-family: "Roboto";
  font-weight: 400;
  font-style: normal;
  font-display: auto;
  unicode-range: U+000-5FF;
  src: local("Roboto"),
    url("/fonts/Roboto/Roboto-Regular.woff2") format("woff2"),
    url("/fonts/Roboto/Roboto-Regular.woff") format("woff");
}
```

首先要注意的是 `font-display:auto`。使用 `auto` 作为值可以让浏览器使用最合适的策略来显示字体。这取决于一些因素，如网络速度、设备类型、闲置时间等。

要想更多地控制字体的加载方式，你应该使用 `font-display: block`，它指示浏览器短暂地隐藏文本，直到字体完全下载完毕。其他可能的值有 `swap`、`fallback` 和 `optional`。你可以在[这里](https://css-tricks.com/almanac/properties/f/font-display/)阅读更多关于它们的信息。

需要注意的是 `unicode-range: U+000-5FF`，它指示浏览器只加载所需的字形范围（U+000 - U+5FF）。你还想使用[woff 和 woff2](https://css-tricks.com/understanding-web-fonts-getting/)字体格式，它们是经过优化的格式，可以在大多数现代浏览器中使用。

另外需要注意的是 `src` 顺序。首先，我们检查字体的本地副本是否可用(`local("Roboto”)`)并使用它。很多 Android 设备都预装了 Roboto，在这种情况下，我们将使用预装的副本。如果没有本地副本，则在浏览器支持的情况下继续下载 woff2 格式。否则，它会跳至支持的声明中的下一个字体。

## 预加载字体

一旦你的自定义字体被声明，你可以使用 `<link rel="preload">` 告诉浏览器提前预加载字体。在 `public/index.html` 中，添加以下内容：

```html
<link
  rel="preload"
  as="font"
  href="./fonts/Roboto/Roboto-Regular.woff2"
  type="font/woff2"
  crossorigin="anonymous"
/>
```

`rel = “preload”` 指示浏览器尽快开始获取资源，`as = “font”` 告诉浏览器这是一种字体，因此它优先处理请求。还要注意`crossorigin=“anonymous"`，因为如果没有这个属性，预加载的字体会被浏览器丢弃。这是因为浏览器是以匿名方式获取字体的，所以使用这个属性就可以匿名请求。

使用 `link=preload` 可以增加自定义字体在需要之前被下载的机会。这个小调整大大加快了字体的加载时间，从而加快了您的 Web 应用程序中的文本渲染。

## 使用 link = preconnect 托管字体

当使用[Google fonts](https://fonts.google.com/)等网站的托管字体时，你可以通过使用 `link=preconnect` 来获得更快的加载时间。它告诉浏览器提前建立与域名的连接。

如果您使用的是 Google 字体提供的 Roboto 字体，则可以在 `public/index.html` 中执行以下操作：

```html
<link rel="preconnect" href="https://fonts.gstatic.com" />
...
<link
  href="https://fonts.googleapis.com/css2?family=Roboto&display=swap"
  rel="stylesheet"
/>
```

这样就可以建立与原点https://fonts.gstatic.com 的初始连接，当浏览器需要从原点获取资源时，连接已经建立。从下图中可以看出两者的区别。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/best-practices-for-loading-fonts-in-vue/1.png)

当加载字体时没有使用 `link=preconnect` 时，你可以看到连接所需的时间（DNS 查找、初始连接、SSL 等）。当像这样使用`link=preconnect` 时，结果看起来非常不同。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/best-practices-for-loading-fonts-in-vue/2.png)

在这里，你会发现 DNS 查找、初始连接和 SSL 所花费的时间已经不存在了，因为前面已经进行了连接。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/best-practices-for-loading-fonts-in-vue/3.png)

## 使用 service workers 缓存字体

字体是静态资源，变化不大，所以它们是缓存的好候选。理想情况下，您的 Web 服务器应该为字体设置一个较长的 `max-age expires` 头，这样浏览器缓存字体的时间就会更长。如果你正在构建一个渐进式网络应用（PWA），那么你可以使用[service workers](https://developers.google.com/web/fundamentals/primers/service-workers)来缓存字体，并直接从缓存中为它们提供服务。

要开始使用 Vue 构建 PWA，请使用 vue-cli 工具生成一个新项目：

```shell
vue create pwa-app
```

选择**Manually select features**选项，然后选择**Progressive Web App (PWA) Support**：

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/best-practices-for-loading-fonts-in-vue/4.png)

这些就是我们生成 PWA 模板所需要的唯一东西。完成后，你就可以把目录改为 `pwa-app`，然后为 app 服务。

```shell
cd pwa-app
yarn serve
```

你会注意到在 `src` 目录下有一个文件 `registerServiceWorker`，其中包含了默认的配置。在项目的根目录下，如果`vue.config.js` 不存在，请创建它，如果存在，请添加以下内容：

```js
// vue.config.js
module.exports = {
  pwa: {
    workboxOptions: {
      skipWaiting: true,
      clientsClaim: true,
    },
  },
};
```

vue-cli 工具使用[PWA plugin](https://cli.vuejs.org/core-plugins/pwa.html)生成 service worker。在底层，它使用[Workbox](https://developers.google.com/web/tools/workbox)来配置 service worker 和它控制的元素、要使用的缓存策略以及其他必要的配置。

在上面的代码片段中，我们要确保我们的应用程序始终由 service worker 的最新版本控制。这是必要的，因为它确保我们的用户总是查看应用程序的最新版本。您可以签出 Workbox 配置文档，以获得对生成的 service worker 行为的更多控制。

接下来，我们将自定义字体添加到 `public` 目录。我有以下结构：

```
root/
  public/
    index.html
    fonts/
      Roboto/
        Roboto-Regular.woff
        Roboto-Regular.woff2
```

一旦完成了 Vue 应用程序的开发，就可以通过从终端运行以下命令来构建它：

```
yarn build
```

这将结果输出到 `dist` 文件夹中。如果你检查文件夹的内容，你会注意到一个类似于 `precache-manifest.1234567890.js` 的文件。它包含了要缓存的资产列表，这只是一个包含修订版和 URL 的键值对的列表。

```js
self.__precacheManifest = (self.__precacheManifest || []).concat([
  {
    "revision": "3628b4ee5b153071e725",
    "url": "/fonts/Roboto/Roboto-Regular.woff2"
  },
  ...
]);
```

`public/` 文件夹中的所有内容都是默认缓存的，其中包括自定义字体。有了这个地方，你可以用像 service 这样的包来[serve](https://www.npmjs.com/package/serve)你的应用程序，或者把 `dist` 文件夹托管在 web 服务器上查看结果。你可以在下面找到一个应用程序的截图。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/best-practices-for-loading-fonts-in-vue/5.png)

在随后的访问中，字体是从缓存中加载的，这可以加快应用程序的加载时间。

## 结论

在这篇文章中，我们研究了在 Vue 应用程序中加载字体时应用的一些最佳实践。使用这些实践将确保你提供的字体看起来不错，而不影响应用的性能。
