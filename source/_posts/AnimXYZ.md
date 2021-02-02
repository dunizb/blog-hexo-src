---
title: 世界上第一个可用于React、Vue、纯HTML和CSS的可组合CSS动画工具包— AnimXYZ
date: 2021-01-11 23:47:20
img: https://weixin-storage.oss-cn-shanghai.aliyuncs.com/202101/AnimXYZ/banner.png
categories:
  - 技术
tags:
  - 开源推荐
  - CSS
summary: "是什么让 AnimXYZ 如此之好，它是可组合的，这意味着你可以组合和混合不同的动画来创建自己的高度可定制的 CSS 动画，而无需编写一个单一的关键帧。"
---

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202101/AnimXYZ/banner.png)

## 什么是 AnimXYZ？

AnimXYZ 是一个 CSS 动画库，用于为你的网站创建自定义 CSS 动画。是什么让 AnimXYZ 如此之好，它是可组合的，这意味着你可以组合和混合不同的动画来创建自己的高度可定制的 CSS 动画，而无需编写一个单一的关键帧。

<!-- more -->

> 制作动画就像用文字描述一样简单

例如，你可以通过编写 `xyz = "fade big up"` 来创建动画，该动画可以使用 AnimXYZ 淡入淡出，按比例放大和向上移动。

AnimXYZ 还有一个小软件包，基本功能是 `2.68kb`，如果包含方便的实用程序，则是 `11.4kb`。

![fade big up](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202101/AnimXYZ/1.gif)

## 可定制性

AnimXYZ 是由 CSS 变量驱动的，AnimXYZ 允许你覆盖任何一个 CSS 变量来进一步定制/控制动画和几乎无限数量的自定义动画。

你可以通过在你的 CSS 中选择带有 `xyz` 属性的元素来编辑一个 AnimXYZ CSS 变量，并改变一个已定义的 AnimXYZ 变量的值，就像这样：

```css
.my-class-name {
  --xyz-opacity: 0.5;
}
```

所有 AnimXYZ 变量的开头都带有 xyz 前缀，然后通常后面跟随 CSS 属性名称。

## 嵌套动画

AnimXYZ 支持嵌套动画，这允许我们在我们的动画元素（带有 `xyz` 属性的元素）中包裹多个元素来制作动画。嵌套动画看起来像这样：

```html
<div class="my-class-name" xyz="fade">
  <div class="xyz-in">Hello</div>
  <div class="xyz-in">Hello</div>
  <div class="xyz-in">Hello</div>
</div>
```

这将使所有包裹着 `.my-name-element` 的元素同时淡入。

![嵌套动画](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202101/AnimXYZ/2.gif)

## 错位动画

如果我们不想让嵌套动画同时发生，我们很幸运，因为 AnimXYZ 也支持错位动画，这意味着如果我们有一个嵌套动画，我们可以让每个元素一个接一个地到达/离开。我们可以通过在 `xyz` 属性中添加 `stagger` 来实现这一点，这将使动画从左到右错开，我们也可以通过使用 `stagger-rev` 来反转错开，所以现在它将从右到左错开。

![错位动画](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202101/AnimXYZ/3.gif)

## 文档

[AnimXYZ.com](https://animxyz.com/)

[AnimXYZ Docs](https://animxyz.com/docs/)

---

原文：[https://blog.zhangbing.site/2021/01/11/AnimXYZ/](https://blog.zhangbing.site/2021/01/11/AnimXYZ/)  
来源：[https://itnext.io/](https://itnext.io/worlds-first-composable-css-animation-toolkit-for-react-vue-plain-html-css-animxyz-1cd0b8229da1)
