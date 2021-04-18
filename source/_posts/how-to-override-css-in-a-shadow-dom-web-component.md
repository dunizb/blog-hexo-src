---
title: 如何在Shadow DOM/Web组件中覆盖CSS
date: 2021-02-01 00:13:31
img: https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202102/shadow-dom-web-component/banner.png
categories:
  - HTML5&CSS3
tags:
  - HTML5
summary: "影子 DOM（Shadow DOM）为我们提供了范围限定的样式封装，并提供了一种让我们随意选择进入（尽可能少）外界的方法。"
---

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202102/shadow-dom-web-component/banner.png)

> 本文翻译自[levelup.gitconnected.com](https://levelup.gitconnected.com/how-to-override-css-in-a-shadow-dom-web-component-38ee2ad79cce)，作者：Cesar William Alvarenga

Web 组件（Web Components）的主要目的之一是提供封装——能够隐藏 HTML 标记结构和 CSS 样式，并与页面上的其他代码分离，这样不同的部分就不会冲突，通过这种方式，这样代码就可以保持漂亮和干净。

影子 DOM（Shadow DOM）为我们提供了范围限定的样式封装，并提供了一种让我们随意选择进入（尽可能少）外界的方法。

<!-- more -->

**但是，如果我想使我的组件可自定义某些样式属性，该怎么办？**

本文介绍了使用 CSS 自定义属性穿透 Shadow DOM 并使你的 Web 组件可自定义的基础知识。

## 创建一个 HTML 元素

我们将使用扩展基本 HTML Element 的 JavaScript 类创建自定义 HTML 元素。然后，我们将使用要创建的标签名称和刚刚创建的类调用`customElements.define()`。

```javascript
class AppCard extends HTMLElement {...}

window.customElements.define('app-card', AppCard);
```

在此示例中，我们将创建此简单的 Material Design 卡片，当我们在 HTML 上添加此元素时将显示该元素：`<app-card> </ app-card>`。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202102/shadow-dom-web-component/1.png)

首先，我们创建 Shadow DOM root，然后将 HTML 和 CSS 字符串分配给 Shadow DOM root 的 innerHTML，如下所示。

```javascript
class AppCard extends HTMLElement {
  constructor() {
    super();

    const shadowRoot = this.attachShadow({ mode: "open" });

    shadowRoot.innerHTML = `
      <style>
        .card {
          background-color: #fff;
          ...
        }
      </style>
      <div class="card">
        <div>Card title</div>
      </div>
    `;
  }
}

window.customElements.define("app-card", AppCard);
```

## 覆盖尝试

在此示例中，我们要修改卡的背景颜色。如果它是 HTML 中的简单 `div` 元素，则可以覆盖 `card` 类或通过 CSS 选择器访问 `div` 元素。但是，以下尝试将无效：

```css
// access the div
app-card > div {
  background-color: #2196f3;
}

// override card class
app-card > .card {
  background-color: #2196f3;
}
```

## 使用 CSS 自定义属性

为了解决这个问题，我们可以使用[CSS 自定义属性](https://developer.mozilla.org/en-US/docs/Web/CSS/--*)（CSS 变量）。可以使用 CSS 中定义的 CSS 自定义属性来更改自定义元素中的某些 CSS 属性。

按照我们的例子，我们将使用属性 `background-color` 上的变量 `card-bg` 来获取谁在使用自定义元素所定义的颜色。

```javascript
class AppCard extends HTMLElement {
  constructor() {
    super();

    const shadowRoot = this.attachShadow({ mode: "open" });

    shadowRoot.innerHTML = `
      <style>
        .card {
          background-color: var(--card-bg, #fff);
          ...
        }
      </style>
      <div class="card">
        <div>Card title</div>
      </div>
    `;
  }
}

window.customElements.define("app-card", AppCard);
```

现在，我们将使用 `app-card` 自定义元素，并在 Body 元素的 CSS 中创建 `card-bg` 变量，我们将十六进制颜色 `#2196F3` 分配给变量。

```html
<html>
  <head>
    <style>
      body {
        --card-bg: #2196f3;
      }
    </style>
  </head>
  <body>
    <app-card></app-card>
  </body>
</html>
```

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202102/shadow-dom-web-component/2.png)

## 总结

使用这种策略，我们可以在你的文档中拥有一个封装的 CSS 元素，同时我们可以允许使用 CSS 对一些属性进行自定义。你可以在这里访问一个[完整的例子](https://codepen.io/cesarwbr/pen/abNyEPe)。
