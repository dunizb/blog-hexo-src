---
title: CSS新增的:where和:is伪类函数是什么？
date: 2021-04-14 19:31:24
categories:
  - 技术
tags:
  - CSS
summary: ":is()和:where()都是伪类函数，可以帮助缩短和停止创建选择器时的重复"
---

## 什么是 :is 与 :where？

`:is()` 和 `:where()` 都是伪类函数，可以帮助缩短和停止创建选择器时的重复。它们都接受选择器的参数数组（id，类，标签等），并选择可以在该列表中选择的任何元素。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/css-where-is/1.gif)

这对如何帮助我们编写更短的选择器可能没有多大意义，所以让我们尝试使用 `:where()` 和 `:is()` 。

## 如何使用 :is 与 :where？

`:where()` 可以帮助我们解决类似这样的问题

```css
.btn span > a:hover,
#header span > a:hover,
#footer span > a:hover {
  ...;
}
```

变成这样的东西

```css
:where(.btn, #header, #footer) span > a:hover {
  ...;
}
```

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/css-where-is/2.gif)

和 `:is()` 可以帮助将相同的示例添加到该示例中

```css
is(.btn, #header, #footer) span > a:hover {
  ...;
}
```

## :is 与 :where 和有什么不一样？

`:where()` 和 `:is()` 看起来和功能都是一样的，但是它们之间有一个区别要记住，那就是它们有不同的特殊性。`:where()` 是简单的，其特异性总是为 0，而 `:is()` 的特异性为最强的选择器。

## 什么是 CSS 特异性（简而言之）？

在 CSS 中有四个层次的特异性层次。每一个级别或类别都有不同的分数，我们可以将所有的分数相加来计算选择器的特异性。

哪个选择器的数量最多，哪个元素的样式就会被应用到该元素上，这就是为什么有时当你写 CSS 时，你的样式不会被应用，会在开发工具中显示为划线。

**特异性等级评分**

- ID——特异性得分为 `100`
- 内联样式——特异性得分为 `1000`
- 元素和伪类——特异性得分为 `1`
- 类、伪类和属性——特异性得分为 `10`

例如

```css
button.btn {
  color: red;
}
.btn {
  color: green;
}
```

`.btn` = 10

`button.btn` = 1 + 10 = 11

如果我们把 `.btn` 类放在 `<button>` 标签上，文字就会变成红色，因为 `button.btn` 选择器的分数高于 `.btn` 选择器。

正如你所看到的，有两种不同的专属性级别的伪类，这是因为不同的伪类可能具有不同的专属性，这取决于你使用的伪类以及如何使用它们。

---

翻译自[itnext.io](https://itnext.io/css-where-is-pseudo-class-functions-33964d0de461)，作者：Fletcher Rippon
