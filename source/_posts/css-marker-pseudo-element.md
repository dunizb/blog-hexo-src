---
title: 使用CSS ::marker的自定义项目符号
date: 2020-11-22 15:51:55
img: https://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/css-marker-pseudo-element/banner1.png
categories:
  - HTML5$CSS3
tags:
  - CSS3
---

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/css-marker-pseudo-element/banner1.png)

**现在，在使用 `<ul>` 或 `<ol>` 时自定义数字或项目符号的颜色，大小或类型很简单。**

感谢 CSS `::marker`，我们可以更改内容以及项目符号和数字的某些样式。

<!-- more -->

## 浏览器兼容性

当 Chromium 86 发布时，`::marker` 将在桌面和 Android 的 Firefox、桌面 Safari 和 iOS Safari 以及基于 Chromium 的桌面和 Android 浏览器中得到支持。有关更新，请参见 MDN 的浏览器兼容性表。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/css-marker-pseudo-element/1.png)

## 伪元素

考虑以下基本 HTML 无序列表：

```html
<ul>
  <li>
    深网｜美团的头号创业项目：王兴发话“这场仗一定要打赢”
  </li>
  <li>
    外媒：华为受美国制裁出现转折，美国或允许部分公司提供非5G零件
  </li>
  <li>
    iPhone12不到一周就破发 经销商：办5G套餐能降1300多元
  </li>
  <li>
    威马汽车自燃事件续：官方承认缺陷电池并召回，占总销量3.6％
  </li>
  <li>
    iPhone
    背后的浓浓塑料情：富士康薅苹果羊毛，库克偷偷找备胎
  </li>
</ul>
```

我们知道会渲染成下面的样子

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/css-marker-pseudo-element/2.png)

每个 `<li>` 项开头的都有一个点。

今天我们很兴奋地讨论一下 `::marker` 伪元素，浏览器为你创建的项目符号元素设置样式。

> **关键术语**：伪元素表示文档中除文档树中存在的元素以外的元素。例如，您可以使用伪元素 `p::first-line` 来选择段落的第一行，即使没有任何 HTML 元素包装这行文本。

## 创建一个 marker

在每个列表项元素内自动生成 `::marker` 伪元素标记框，在实际内容和 `::before` 伪元素之前。

```css
li::before {
  content: "::before";
  background: lightgray;
  border-radius: 1ch;
  padding-inline: 1ch;
  margin-inline-end: 1ch;
}
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/css-marker-pseudo-element/3.png)

通常情况下，列表项是 `<li>` HTML 元素，但其他元素也可以通过 `display:list-item` 成为列表项。

```html
<dl>
  <dt>
    深网｜美团的头号创业项目：王兴发话“这场仗一定要打赢”
  </dt>
  <dd>
    L外媒：华为受美国制裁出现转折，美国或允许部分公司提供非5G零件
  </dd>
  <dd>
    iPhone12不到一周就破发 经销商：办5G套餐能降1300多元
  </dd>

  <dt>
    威马汽车自燃事件续：官方承认缺陷电池并召回，占总销量3.6％
  </dt>
  <dd>
    iPhone
    背后的浓浓塑料情：富士康薅苹果羊毛，库克偷偷找备胎
  </dd>
</dl>
```

```css
dd {
  display: list-item;
  list-style-type: "🤯";
  padding-inline-start: 1ch;
}
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/css-marker-pseudo-element/4.png)

## marker 样式

在使用 `::marker` 之前，列表可以使用 `list-style-type` 和 `list-style-image` 来改变列表项的符号，只需使用一行 CSS。

```css
li {
  list-style-image: url(/right-arrow.svg);
  /* OR */
  list-style-type: "👉";
  padding-inline-start: 1ch;
}
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/css-marker-pseudo-element/5.png)

这是方便的，但我们需要更多。改变颜色，大小，间距等！这就是 `::marker` 的用武之地，它允许从 CSS 中单独或全局地定位这些伪元素。

```css
li::marker {
  color: hotpink;
}

li:first-child::marker {
  font-size: 5rem;
}
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/css-marker-pseudo-element/6.png)

> **警告**：如果上面的列表没有粉红色的项目符号，则您的浏览器不支持 `::marker`。

`list-style-type` 属性提供的样式可能性非常有限。`::marker` 伪元素意味着你可以将标记本身作为目标，并直接对其应用样式，这就允许更多的控制。

也就是说，你不能在一个 `::marker` 上使用每一个 CSS 属性。规范中明确指出了允许和不允许的属性列表，如果你用这个伪元素尝试了一些有趣的东西，但没有成功，下面的列表是你的指南，让你了解什么可以和什么不可以用 CSS 来做。

### 允许的 CSS ::marker 属性

- `animation-*`
- `transition-*`
- `color`
- `direction`
- `font-*`
- `content`
- `unicode-bidi`
- `white-space`

更改 `::marker` 的内容是通过 `content` 而不是 `list-style-type` 来完成的。在下一个示例中，第一项使用 `list-style-type` 设置样式，第二项使用 `::marker` 设置样式。第一种情况下的属性适用于整个列表项，而不仅仅是标记，这意味着文本和标记都在动画化。当使用 `::marker` 时，我们可以只针对标记框而不是文本。

另外，注意不允许的 `background` 属性是没有效果的。

**List Style**

```css
li:nth-child(1) {
  list-style-type: "?";
  font-size: 2rem;
  background: hsl(200 20% 88%);
  animation: color-change 3s ease-in-out infinite;
}
```

**Marker Styles**

```css
li:nth-child(2)::marker {
  content: "!";
  font-size: 2rem;
  background: hsl(200 20% 88%);
  animation: color-change 3s ease-in-out infinite;
}
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/css-marker-pseudo-element/7.gif)

### 更改 marker 的内容

以下是一些样式标记的方法。

**更改所有列表项**

```css
li {
  list-style-type: "😍";
}

/* OR */

li::marker {
  content: "😍";
}
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/css-marker-pseudo-element/8.png)

**仅更改一个列表项**

```css
li:last-child::marker {
  content: "😍";
}
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/css-marker-pseudo-element/9.png)

**将列表项更改为 SVG**

```css
li::marker {
  content: url(/heart.svg);
  content: url(#heart);
  content: url("data:image/svg+xml;charset=UTF-8,<svg xmlns='http://www.w3.org/2000/svg' version='1.1' height='24' width='24'><path d='M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z' fill='none' stroke='hotpink' stroke-width='3'/></svg>");
}
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/css-marker-pseudo-element/10.png)

**更改编号列表**

那 `<ol>` 呢？默认情况下，有序列表项上的标记是数字，而不是项目符号。在 CSS 中，这些功能称为[Counters](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Lists_and_Counters/Using_CSS_counters)，功能非常强大。它们甚至有属性来设置和重设数字的开始和结束位置，或者将它们切换为罗马数字。我们可以给它设计样式吗？是的，我们甚至可以使用 marker content 值来构建我们自己的编号表示。

```css
li::marker {
  content: counter(list-item) "› ";
  color: hotpink;
}
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/css-marker-pseudo-element/11.png)

## 调试

Chrome DevTools 随时可以帮助你检查，调试和修改应用于 `::marker` 伪元素的样式。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/css-marker-pseudo-element/12.png)

## 未来的伪元素样式

您可以从以下位置找到有关 `::marker` 的更多信息：

- [CSS Lists, Markers and Counters](https://www.smashingmagazine.com/2019/07/css-lists-markers-counters/) from [Smashing Magazine](https://www.smashingmagazine.com/)
- [Counting With CSS Counters and CSS Grid](https://css-tricks.com/counting-css-counters-css-grid/) from [CSS-Tricks](https://css-tricks.com/)
- [Using CSS Counters](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Lists_and_Counters/Using_CSS_counters) from [MDN](https://developer.mozilla.org/)

能接触到一些一直难以样式化的东西，真是太好了，你可能会希望你能对其他自动生成的元素进行样式设计。你可能会对 `<details>` 或搜索输入自动完成指示器感到沮丧，这些东西在各浏览器中的实现方式并不相同。

---

原文：[https://web.dev/css-marker-pseudo-element/](https://web.dev/css-marker-pseudo-element/)
