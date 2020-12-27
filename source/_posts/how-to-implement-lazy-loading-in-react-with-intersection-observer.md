---
title: 实现高性能延迟加载的最新API：Intersection Observer
date: 2020-12-28 02:40:58
categories:
  - 技术
tags:
  - WebAPI
---

当你制作一个有许多图片需要在屏幕上加载的网站时，你应该注意它们在浏览器上的渲染性能。

如果在项目代码中使用 React，则可以通过 NPM 或 Yarn 安装[_react-lazyload_](https://www.npmjs.com/package/react-lazyload)软件包。它的图片加载方式不同，所以使用这个包时可以期待更好的性能。

在这篇文章中，我将向您展示使用 Intersection Observer 的另一种延迟加载方式。

<!-- more -->

## 什么是 IntersectionObserver

与多年前相比，Intersection Observer 是用于检测性能更好的元素的新 API，原因如下

- 滚动页面时延迟加载图像或其他内容
- 实现“无限滚动”的网站，当你滚动时，越来越多的内容会被加载和渲染，这样用户就不用再翻页了。
- 报告广告的可见度，以便计算广告收入。
- 根据用户是否会看到结果来决定是否执行任务或动画过程。

我们先来看看如何使用这个 API 的例子后，再来深谈这个 API。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/IntersectionObserver/1.gif)

Intersection Observer 是一个两个数组的构造函数。它需要一个回调函数，每当它所监视的元素出现在浏览器中时，这个回调函数就会被启动，并且需要一个包含信息的可选对象，[MDN 文档](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API#Creating_an_intersection_observer)。

### 为什么 Intersection Observer 更好？

当过去需要无限滚动时，因为需要检查滚动条当前位置的 `getClientBoundingRect` 会强制页面回流。

随着时间的推移，新的强大功能——Intersection Observer 应运而生，推荐使用它进行无限滚动。但是，它仍然使用`getClientBoundingRect` 来获取它所观察的元素的位置。那为什么认为它更好呢？

Intersection Observer 调用带有 `requestIdleCallback` 的回调函数，当当前 tick 期间没有任务运行时，该函数就会被启动。因此，尽管使用 Intersection Observer 的旧方式和新方式都可能导致回流，但 Intersection Observer 只在空闲期运行回调函数，因此在性能上更好。

## 正常的卡片视图

我使用随机图片选择器网站获取了随机图片，这是项目的基本结构。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/IntersectionObserver/2.gif)

本页面一次性加载约 50 张物品卡，在第一次渲染时也会加载约 50 张图片。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/IntersectionObserver/3.png)

您可以从开发者工具检查“网络”标签上加载了多少张图片。在这种情况下，从 67 个网络请求中加载 49 个图像。幸运的是，网络请求的瀑布并不长，但你不应该有“哦，我不需要关心这些”这样的想法。

如果屏幕上需要渲染成千上万张图像该怎么办？轻松理解我观点的最好例子是思考 Pinterest 的工作原理。他们会在第一次看到的时候向你展示各种图片，每当你向下滚动到底部的时候——惰性加载。

那么如何使用 Intersection Observer 实现延迟加载？这很简单。设置触发回调函数的阈值，并使 Intersection Observer API 继续监视该元素。

```js
const intersectionCallback = entries => {
  entries.forEach(entry => {
    // 如果元素在浏览器中显示的比例超过50％
    if (entry.intersectionRatio > 0.5) {
      // 图像加载...
      ...
    }
  });
}

const IntersectionObserver = new IntersectionObserver(intersectionCallback, {
  threshold: [0.5],
});
```

这是您可以传递到 Intersection Observer 的属性的列表。

- root —— Intersection Observer 将跟踪的根元素，如果省略它，根元素将被设置为视口的
- rootMargin —— 根元素的边距值，该规则与 CSS 边距相同。例如，如果 rootMargin 设置为“50px 0px 0px 0px”，根元素的区域将更改为不可见元素，其顶偏移量为原始根元素的 50px。
- threshold —— 数组中的值，每当跟踪元素通过阈值时，就会触发回调。如果阈值设置为[0，0.5，1]，当跟踪元素通过 0%，50%和 100%的点时，将启动回调，这指的是它在根元素上显示的程度。

## 懒加载卡片视图

在此示例的延迟加载中，仅当每张图片在屏幕上的显示比例超过 50％时，才会加载每张图片。

现在，让我们看一下图像加载状态。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/IntersectionObserver/4.gif)

看到只有向下滚动时加载的图像计数在增加吗？由于使用了 Intersection Observer，因此只有在适当的时候才允许浏览器加载图像。

## 浏览器支持

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/IntersectionObserver/5.png)

浏览器的支持范围还不错，但也不是那么好，因为 IE 根本不支持该 API。但是不用担心！ W3C 为项目中需要此功能的人分发了[IntersectionObserver 的 polyfill](https://github.com/w3c/IntersectionObserver/tree/master/polyfill)。

## NPM 中著名的 Lazy-Load 程序包

使用 IntersectionObserver 实现延迟加载组件非常简单，就像我之前在这篇文章中演示的那样。但是，您可能不想从头到尾实现它。有时您可能只想安装一个包。所以我要介绍一些有很多星星的 NPM 包。

- react-lazy-load：https://www.npmjs.com/package/react-lazy-load
- vanilla-lazyload：https://www.npmjs.com/package/vanilla-lazyload
- jquery-lazy：https://www.npmjs.com/package/jquery-lazy

## 总结

Intersection Observer 是一个浏览器专用的功能，它可以让你实现懒加载。您可以惰性地加载图片或重磅内容，以减少用户看到内容所需等待的总时间。

不过，IntersectionObserver 并不是所有的浏览器都完全支持，所以一定要检查 polyfill，或者你可以考虑安装一个已经做好的包。

## 参考

- https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API
- https://developer.mozilla.org/en-US/docs/Web/API/Window/requestIdleCallback
- https://developers.google.com/web/updates/2016/04/intersectionobserver

---

原文：[https://blog.zhangbing.site/2020/12/28/how-to-implement-lazy-loading-in-react-with-intersection-observer/](https://blog.zhangbing.site/2020/12/28/how-to-implement-lazy-loading-in-react-with-intersection-observer/)  
来源：[https://levelup.gitconnected.com/](https://levelup.gitconnected.com/how-to-implement-lazy-loading-in-react-with-intersection-observer-61c0e53ec8d)
