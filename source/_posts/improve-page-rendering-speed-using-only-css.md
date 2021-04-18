---
title: 仅使用CSS就可以提高页面渲染速度的4个技巧
date: 2020-12-28 15:13:42
img: https://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/page-rendering-speed-only-css/banner.jpeg
categories:
  - HTML5$CSS3
tags:
  - WebAPI
  - CSS3
  - 翻译
summary: "我们不需要写一条 JavaScript 语句就能获得所有的性能"
---

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/page-rendering-speed-only-css/banner.jpeg)

用户喜欢快速的网络应用，他们希望页面加载速度快，功能流畅。如果在滚动时有破损的动画或滞后，用户很有可能会离开你的网站。作为一名开发者，你可以做很多事情来改善用户体验。本文将重点介绍 4 个可以用来提高页面渲染速度的 CSS 技巧。

<!-- more -->

## 1. Content-visibility

一般来说，大多数 Web 应用都有复杂的 UI 元素，它的扩展范围超出了用户在浏览器视图中看到的内容。在这种情况下，我们可以使用内容可见性（ `content-visibility` ）来跳过屏幕外内容的渲染。如果你有大量的离屏内容，这将大大减少页面渲染时间。

这个功能是最新增加的功能之一，也是对提高渲染性能影响最大的功能之一。虽然 `content-visibility` 接受几个值，但我们可以在元素上使用 `content-visibility: auto;` 来获得直接的性能提升。

让我们考虑一下下面的页面，其中包含许多不同信息的卡片。虽然大约有 12 张卡适合屏幕，但列表中大约有 375 张卡。正如你所看到的，浏览器用了 1037ms 来渲染这个页面。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/page-rendering-speed-only-css/1.png)

下一步，您可以向所有卡添加 `content-visibility` 。

> 在这个例子中，在页面中加入 `content-visibility` 后，渲染时间下降到 150ms，这是 6 倍以上的性能提升。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/page-rendering-speed-only-css/2.png)

正如你所看到的，内容可见性是相当强大的，对提高页面渲染时间非常有用。根据我们目前所讨论的东西，你一定是把它当成了页面渲染的银弹。

### content-visibility 的限制

然而，有几个领域的内容可视性不佳。我想强调两点，供大家参考。

- **此功能仍处于试验阶段。**截至目前，Firefox（PC 和 Android 版本）、IE（我认为他们没有计划在 IE 中添加这个功能）和，Safari（Mac 和 iOS）不支持内容可见性。
- **与滚动条行为有关的问题。**由于元素的初始渲染高度为 0px，每当你向下滚动时，这些元素就会进入屏幕。实际内容会被渲染，元素的高度也会相应更新。这将使滚动条的行为以一种非预期的方式进行。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/page-rendering-speed-only-css/3.gif)

为了解决滚动条的问题，你可以使用另一个叫做 `contain-intrinsic-size` 的 CSS 属性。它指定了一个元素的自然大小，因此，元素将以给定的高度而不是 0px 呈现。

```css
.element {
  content-visibility: auto;
  contain-intrinsic-size: 200px;
}
```

然而，在实验时，我注意到，即使使用 `conta-intrinsic-size`，如果我们有大量的元素， `content-visibility` 设置为 `auto` ，你仍然会有较小的滚动条问题。

因此，我的建议是规划你的布局，将其分解成几个部分，然后在这些部分上使用内容可见性，以获得更好的滚动条行为。

## 2. Will-change 属性

浏览器上的动画并不是一件新鲜事。通常情况下，这些动画是和其他元素一起定期渲染的。不过，现在浏览器可以使用 GPU 来优化其中的一些动画操作。

> 通过 will-change CSS 属性，我们可以表明元素将修改特定的属性，让浏览器事先进行必要的优化。

下面发生的事情是，浏览器将为该元素创建一个单独的层。之后，它将该元素的渲染与其他优化一起委托给 GPU。这将使动画更加流畅，因为 GPU 加速接管了动画的渲染。

考虑以下 CSS 类：

```css
// In stylesheet
.animating-element {
  will-change: opacity;
}

// In HTML
<div class="animating-elememt">
  Animating Child elements
</div>
```

当在浏览器中渲染上述片段时，它将识别 `will-change` 属性并优化未来与不透明度相关的变化。

> 根据 Maximillian Laumeister 所做的性能基准，可以看到他通过这个单行的改变获得了超过 120FPS 的渲染速度，而最初的渲染速度大概在 50FPS。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/page-rendering-speed-only-css/4.png)

![5](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/page-rendering-speed-only-css/5.png)

### 什么时候不是用 will-change

虽然 `will-change` 的目的是为了提高性能，但如果你滥用它，它也会降低 Web 应用的性能。

- **使用 `will-change` 表示该元素在未来会发生变化。**因此，如果你试图将 `will-change` 和动画同时使用，它将不会给你带来优化。因此，建议在父元素上使用 `will-change` ，在子元素上使用动画。

  ```css
  .my-class {
    will-change: opacity;
  }
  .child-class {
    transition: opacity 1s ease-in-out;
  }
  ```

- **不要使用非动画元素。**当你在一个元素上使用 `will-change` 时，浏览器会尝试通过将元素移动到一个新的图层并将转换工作交给 GPU 来优化它。如果您没有任何要转换的内容，则会导致资源浪费。

最后需要注意的是，建议在完成所有动画后，将元素的 `will-change` 删除。

## 3.减少渲染阻止时间

今天，许多 Web 应用必须满足多种形式的需求，包括 PC、平板电脑和手机等。为了完成这种响应式的特性，我们必须根据媒体尺寸编写新的样式。当涉及页面渲染时，它无法启动渲染阶段，直到 CSS 对象模型（CSSOM）已准备就绪。根据你的 Web 应用，你可能会有一个大的样式表来满足所有设备的形式因素。

> 但是，假设我们根据表单因素将其拆分为多个样式表。在这种情况下，我们可以只让主 CSS 文件阻塞关键路径，并以高优先级下载它，而让其他样式表以低优先级方式下载。

```html
<link rel="stylesheet" href="styles.css" />
```

![单一样式表](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/page-rendering-speed-only-css/6.png)

将其分解为多个样式表后：

```html
<!-- style.css contains only the minimal styles needed for the page rendering -->
<link rel="stylesheet" href="styles.css" media="all" />
<!-- Following stylesheets have only the styles necessary for the form factor -->
<link
  rel="stylesheet"
  href="sm.css"
  media="(min-width: 20em)"
/>
<link
  rel="stylesheet"
  href="md.css"
  media="(min-width: 64em)"
/>
<link
  rel="stylesheet"
  href="lg.css"
  media="(min-width: 90em)"
/>
<link
  rel="stylesheet"
  href="ex.css"
  media="(min-width: 120em)"
/>
<link rel="stylesheet" href="print.css" media="print" />
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/page-rendering-speed-only-css/7.png)

如您所见，根据样式因素分解样式表可以减少渲染阻止时间。

## 4.避免@import 包含多个样式表

通过 `@import`，我们可以在另一个样式表中包含一个样式表。当我们在处理一个大型项目时，使用 `@import` 可以使代码更加简洁。

> 关于 `@import` 的关键事实是，它是一个阻塞调用，因为它必须通过网络请求来获取文件，解析文件，并将其包含在样式表中。如果我们在样式表中嵌套了 `@import`，就会妨碍渲染性能。

```css
#style.css @import url("windows.css");
#windows.css @import url("componenets.css");
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/page-rendering-speed-only-css/8.png)

与使用 `@import` 相比，我们可以通过多个 `link` 来实现同样的功能，但性能要好得多，因为它允许我们并行加载样式表。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/page-rendering-speed-only-css/9.png)

## 总结

除了我们在本文中讨论的 4 个方面，我们还有一些其他的方法可以使用 CSS 来提高网页的性能。CSS 最近的一个特性： `content-visibility`，在未来的几年里看起来是如此的有前途，因为它给页面渲染带来了数倍的性能提升。

> 最重要的是，我们不需要写一条 JavaScript 语句就能获得所有的性能。

我相信你可以结合以上的一些功能，为终端用户构建性能更好的 Web 应用。希望这篇文章对你有用，如果你知道什么 CSS 技巧可以提高 Web 应用的性能，请在下面的评论中提及。谢谢大家。

---

原文：[https://blog.zhangbing.site/2020/12/28/improve-page-rendering-speed-using-only-css/](https://blog.zhangbing.site/2020/12/28/improve-page-rendering-speed-using-only-css/)  
翻译自：[blog.bitsrc.io](https://blog.bitsrc.io/improve-page-rendering-speed-using-only-css-a61667a16b2)
