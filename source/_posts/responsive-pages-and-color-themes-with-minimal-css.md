---
title: 如何使用最少的CSS创建响应式页面和颜色主题
date: 2021-01-04 21:28:02
categories:
  - HTML5$CSS3
tags:
  - CSS3
---

想用彩色主题搭建一个响应式网站？从根本上入手吧。

如果您碰巧路过我的网站，您可能会注意到我把它美化了一下。[Victoria.dev](https://victoria.dev/)现在可以更好地响应您的设备和偏好。

大多数现代设备和 Web 浏览器都允许用户为用户界面选择浅色或深色主题。使用 CSS 媒体查询，您可以更改自己的网站样式以匹配此用户设置！

媒体查询也是一种常见的方式，让网页上的元素改变以适应不同的屏幕尺寸。当与设置在根元素上的自定义属性相结合时，这是一个特别强大的工具。

以下是如何使用 CSS 媒体查询和自定义属性，仅用几行 CSS 就能改善访问者的浏览体验。

## 如何迎合人们的色彩偏好

`prefers-color-scheme` 媒体功能可以查询到用户选择的颜色方案。如果没有设置主动偏好， `light` 选项是首选版本，它在现代浏览器中都有不错的支持。

此外，在某些设备上阅读的用户还可以根据时间表设置明暗主题。例如，我的手机白天在整个 UI 中使用浅色，而晚上则使用深色，你可以让你的网站也跟着学。

通过在你的 `:root` 伪类上为你的颜色主题设置自定义属性，避免重复大量的 CSS。为您希望支持的每个主题创建一个版本，这里有一个简单的例子：

```css
:root {
  color-scheme: light dark;
}

@media (prefers-color-scheme: light) {
  :root {
    --text-primary: #24292e;
    --background: white;
    --shadow: rgba(0, 0, 0, 0.15) 0px 2px 5px 0px;
  }
}

@media (prefers-color-scheme: dark) {
  :root {
    --text-primary: white;
    --background: #24292e;
    --shadow: rgba(0, 0, 0, 0.35) 0px 2px 5px 0px;
  }
}
```

如您所见，您可以使用自定义属性来设置各种值。要将这些变量与其他 CSS 元素一起用作变量，请使用 `var()` 函数：

```css
header {
  color: var(--text-primary);
  background-color: var(--background);
  box-shadow: var(--shadow);
}
```

在这个快速的例子中，`header` 元素现在将根据用户的浏览器设置来显示他们喜欢的颜色。用户根据浏览器以不同的方式设置首选的配色方案。这是几个例子。

### Firefox 中的明暗模式

您可以通过在地址栏中键入 `about:config` 来测试 Firefox 中的 `light` 和 `dark` 模式。如果警告弹出，请接受警告，然后在搜索中键入 `ui.systemUsesDarkTheme`。

为设置选择一个 `Number` 值，然后为暗输入 `1`，为亮输入 `0`。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202101/color-themes/1.png)

### Brave 浏览器的明暗模式

如果您使用的是 Brave 浏览器，请在“Settings”>“Appearance”>“Brave colors”中找到颜色主题设置。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202101/color-themes/2.png)

## 如何使用可变缩放

您还可以使用自定义属性轻松调整文本或其他元素的大小，具体取决于用户的屏幕大小， `width` 媒体功能会测试视口的宽度。

虽然 `width: _px` 将匹配确切的大小，但是您也可以使用 `min` 和 `max` 来创建范围。

查询时，最 `min-width: _px` 以匹配超过 `_` 像素的内容， `max-width: _px` 以匹配最大 `_` 像素的内容。

使用以下查询在 `:root` 上设置自定义属性以创建比率：

```css
@media (min-width: 360px) {
  :root {
    --scale: 0.8;
  }
}

@media (min-width: 768px) {
  :root {
    --scale: 1;
  }
}

@media (min-width: 1024px) {
  :root {
    --scale: 1.2;
  }
}
```

然后使用 `calc()` 函数使元素响应。这里有一些例子：

```css
h1 {
  font-size: calc(42px * var(--scale));
}

h2 {
  font-size: calc(26px * var(--scale));
}

img {
  width: calc(200px * var(--scale));
}
```

在这个例子中，将初始值乘以 `--scale` 自定义属性，可以使标题和图片的大小神奇地调整到用户的设备宽度。

相对单位 rem 也会有类似的效果，您可以使用它来定义元素相对于根元素声明的字体大小的大小。

```css
h1 {
  font-size: calc(5rem * var(--scale));
}

h2 {
  font-size: calc(1.5rem * var(--scale));
}

p {
  font-size: calc(1rem * var(--scale));
}
```

当然，你也可以将两个自定义属性相乘。例如，在 `:root` 上设置 `--max-img` 为自定义属性，可以帮助你以后不必在多个地方更新一个像素值，从而节省时间。

```css
img {
  max-width: calc(var(--max-img) * var(--scale));
}
```

---

原文：[https://blog.zhangbing.site/2020/01/01/responsive-pages-and-color-themes-with-minimal-css/](https://blog.zhangbing.site/2020/01/04/responsive-pages-and-color-themes-with-minimal-css/)  
来源：[https://www.freecodecamp.org](https://www.freecodecamp.org/news/responsive-pages-and-color-themes-with-minimal-css/)  
作者：Victoria Drake
