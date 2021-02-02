---
title: 实战：使用CSS和JS创建“前后”图像比较效果
date: 2021-01-04 21:54:17
img: https://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/before-after-image-slider/banner.gif
cover: true
top: true
categories:
  - 技术
tags:
  - JavaScript
  - 实战
summary: "使用 html range input创建前后图像比较效果的分步教程。"
---

![https://coding.zhangbing.site/view.html?url=./list/slider-before-after-image.html](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/before-after-image-slider/banner.gif)

使用 html **range input**创建前后图像比较效果的分步教程。使用 CSS 和 JavaScript，JS 部分代码非常少，主要是 HTML、CSS，和实现思路。

<!-- more -->

## 介绍

如果你有两张图像进行比较，则“前后”图像滑块是一个有效而又简单的 UI 元素。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/before-after-image-slider/1.gif)

该“slider”元素使你的用户可以控制两个图像在屏幕上的显示方式，并自由浏览两个不同的图像。你可能认为它需要一些库来创建这样的效果，但实际上，它是一个非常直接和容易编码的 UI。有了 CSS 和 JS 的基础知识，每个人都可以创建它。

在本教程中，我将详细解释这个 UI 背后的概念，如何实现它，以及进一步增强的建议。

## 结果演示

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/before-after-image-slider/9.gif)

## 逐步指南

### 步骤 1.了解概念

这个“图像 slider”的概念非常简单，你只需要两个组件，图像容器和一个 slider。

图片容器只是一个普通的 div，两张相同大小的图片相互重叠。一个作为“背景”，另一个作为“前景”。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/before-after-image-slider/3.png)

我们将使用绝对定位使前景图像直接置于背景图像之上。背景图的宽度总是 100%，而前景图的宽度会根据用户的输入而改变，使背景图的一部分出现。

第二个组件是“slider”。为了使事情简单化，我们可以使用 html `range`输入元素。它允许用户在你定义的最小值和最大值之间通过拖动来选择一个值。使用 javascript 中的事件监听器可以很容易地获取输入值。

```html
<input
  type="range"
  min="1"
  max="100"
  value="50"
  class="slider"
  id="myRange"
/>
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/before-after-image-slider/4.gif)

> 使用默认 slider 输入的一个缺点是，样式是有限的。你不能在设计上太过疯狂，如果你正在寻找一个更可定制的 slider，你可能需要自己构建它。然而，这不是本教程的重点。

我们将使滑块具有 100％的容器宽度和高度，并将其放置在图像容器的顶部。当用户拖动滑块时，我们同时更新前景的宽度，创建一种用户拖动图像的错觉。

![当滑块值更新时，我们将更改前景的宽度](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/before-after-image-slider/5.png)

### 步骤 2.创建图像容器

首先创建容器，这是一个内部有两个 div 的简单结构。由于我们不希望图像根据包含它们的 div 宽度进行缩放，因此我们将对图像应用 `background-image` 而不是 `<img>` 标签。我们需要使用的一种重要样式是 `background-size` 属性，并确保图像始终保持相同大小。

HTML:

```html
<div class="container">
  <div class="img background-img"></div>
  <div class="img foreground-img"></div>
</div>
```

CSS:

```css
.container {
  position: relative;
  width: 900px;
  height: 600px;
  border: 2px solid white;
}
.img {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-size: 900px 100%;
}
.background-img {
  background-image: url("https://i.loli.net/2020/12/28/1dGpFx3zJ9Pjcme.jpg");
}
.foreground-img {
  background-image: url("https://i.loli.net/2020/12/28/xIZmjtBR5VWoqiz.jpg");
  width: 50%;
}
```

> 为了使本教程更容易些，我对所有内容都使用了固定大小。如果你不想使用 SCSS，只需将样式设置为扁平而不是嵌套

![我使用了同一图像的两个版本（有色和无色），因此它们完全对齐](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/before-after-image-slider/6.png)

现在我们有了容器，让我们添加滑块。

### 步骤 3.创建滑块

我们的滑块需要覆盖整个图像，并用细白条“划分”图像的前后部分。可以通过设置滑块和滑块（你拖动的部分）的样式来完成。我们需要使滑块和拇指的默认外观不可见，然后在其上应用我们自己的样式。

HTML（在图片下方）：

```html
<div class="container">
  ...
  <input
    type="range"
    min="1"
    max="100"
    value="50"
    class="slider"
    name="slider"
    id="slider"
  />
</div>
```

```css
.container {
  position: relative;
  width: 900px;
  height: 600px;
  border: 2px solid white;
}
.img {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-size: 900px 100%;
}
.background-img {
  background-image: url("https://i.loli.net/2020/12/28/1dGpFx3zJ9Pjcme.jpg");
}
.foreground-img {
  background-image: url("https://i.loli.net/2020/12/28/xIZmjtBR5VWoqiz.jpg");
  width: 50%;
}

.slider {
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute;
  -webkit-appearance: none;
  appearance: none;
  width: 100%;
  height: 100%;
  background: rgba(242, 242, 241, 0.3);
  outline: none;
  margin: 0;
  transition: all 0.2s;
}
.slider:hover {
  background: rgba(242, 242, 241, 0.1);
}
.slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 6px;
  height: 600px;
  background: white;
  cursor: pointer;
}
```

我在滑块上应用了一个略微可见的灰色背景，在悬停时，使颜色更加透明。当用户将鼠标悬停在图像上时，创建“焦点”效果。对于滑块，它只是一个白色背景 div，具有容器的整个高度。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/before-after-image-slider/7.gif)

我们现在有了一个工作的滑块，让我们把它和前景图片的宽度联系起来。

### 步骤 4.将事件侦听器添加到滑块

最后一步是将滑块上的值链接到前景图像的宽度，这很容易实现（因为我们使用本地 html `range` input 作为滑块）。应用事件侦听器时，可以在 `event.target.value` 中获得 `1–100` 的值。

然后，我们只需要选择前景元素，并在滑块更新时更改它的宽度即可。

JS：

```js
const slider = document.getElementById("slider");
slider.addEventListener("input", function(e) {
  document.querySelector(".foreground-img").style.width =
    e.target.value + "%";
});
```

> 如果它没有按预期工作，请尝试查看你是否正确获取了 slider 的值，然后再次检查 CSS 的 background-size 属性

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/before-after-image-slider/8.gif)

太酷了！功能正常。作为使用 `range` 输入的额外好处是我们甚至可以在容器内单击，使滑块移动到被单击的位置。

我们可以（可能应该）添加到 UI 的另一件事是在滑块上做一个“拖动我”圆圈图标，以指示这是可拖动的组件。

### 步骤 5（可选）在滑块上添加圆圈拇指

原生的 `range` 输入有它的优势（容易实现，容易取值等），但在样式上我们可以做的不多。由于我们用白色分隔线代替了默认的 “圆圈”拇指，所以我们需要用某种方式把圆圈加回来。

一种快速(但不实用)的方法是添加另一个元素，它与滑块完全无关，但位于滑块的中心，并通过 javascript“跟随”滑块的移动。这正是我们要做的。

HTML：

```html
<div class="container">
  ...
  <div class="slider-button"></div>
</div>
```

CSS：

```css
.slider-button {
  pointer-events: none;
  position: absolute;
  width: 30px;
  height: 30px;
  border-radius: 50%;
  background-color: white;
  left: calc(50% - 18px);
  top: calc(50% - 18px);
  display: flex;
  justify-content: center;
  align-items: center;
}
.slider-button::after {
  content: "";
  padding: 3px;
  display: inline-block;
  border: solid #5d5d5d;
  border-width: 0 2px 2px 0;
  transform: rotate(-45deg);
}
.slider-button::before {
  content: "";
  padding: 3px;
  display: inline-block;
  border: solid #5d5d5d;
  border-width: 0 2px 2px 0;
  transform: rotate(135deg);
}
```

> after 和 before 元素在圆圈按钮内添加两个“箭头”

JS：

```js
const slider = document.getElementById("slider");
let sliderPos;
slider.addEventListener("input", function(e) {
  sliderPos = e.target.value;
  document.querySelector(
    ".foreground-img"
  ).style.width = `${sliderPos}%`;
  document.querySelector(
    ".slider-button"
  ).style.left = `calc(${sliderPos}% - 18px)`;
});
```

我们还需要做的一件事是让圆圈成为不可选择的，这样鼠标事件总是会转到滑块。通过一些精心的定位和 JS，我们让圆圈拇指和滑块一起移动。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/before-after-image-slider/9.gif)

这样，我们的“前后”图像滑块就完成了，你现在可以选择你最喜欢的图片并进行实验。

## 源码

效果和全部代码：[https://coding.zhangbing.site](https://coding.zhangbing.site/view.html?url=./list/slider-before-after-image.html)

## 总结

如前所述，这个 UI 元素的概念非常简单，你不需要安装另一个库来达到这种效果。话虽如此说，这是一个非常基本的实现，正如你所看到的，我使用了许多固定像素大小。如果你想要一个更“高级”的设计，我建议花点时间研究一下不同窗口大小下的设计变化。

在此示例中，我使用了黑白图片和彩色图片的比较。但是，我看到了许多其他具有时间变化特征的示例（例如 100 年前和现在的同一座城市）。对于这类设计，实现是类似的。

另外，由于我们使用的是背景图片属性（不受容器大小的影响），因此你还可以使用动画 gif 轻松创建静态到动态的滑块。

如果你喜欢本教程或有其他想法，请发表评论！
