---
title: 【译】Web中的图像技术总结，前端开发中各种图片引入的优点缺点及实例
date: 2020-04-24 13:31:45
categories:
  - 技术
tags:
  - 前端
  - HTML
  - CSS
---

前端开发人员在构建网站时需要做出的决定之一就是添加图片的技术。它可以是HTML `<img>`，也可以是通过CSS背景生成的图片，也可以是SVG `<image>`。选择正确的技术很重要，并且可以在性能和可访问性方面发挥巨大作用。

在这篇文章中，我们除了提到各种包含图片的方法外，还将了解到每种方法的优点和缺点，以及什么时候和为什么要使用每种方法的来龙去脉。

<!-- more -->

原文信息：
- 原文：https://ishadeed.com/article/image-techniques/
- 作者：Ahmad Shadeed

******

## 1. HTML `<Img>` 元素

最简单的情况下，图片元素必须包含 `src` 属性。

```html
<img src="cool.jpg" alt="">
```

### 1.1 设置宽度和高度属性

在页面加载时，它们会在页面图片加载时发生一些布局变化。为了避免这种情况，我们可以设置 `width` 和 `height` 属性：

```html
<img src="cool.jpg" width="200" height="100" alt="">
```

虽然对某些人来说，这可能看起来有点过时，但它是有用的。让我们用图片来清楚地理解这个概念：

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/image-techniques/1.gif)

你注意到了吗，右边的图片即使还没有加载也会保留其空间吗？这是因为宽度和高度已经设置好了。它有明显的区别！

[Demo](https://codepen.io/shadeed/pen/a42ab701809acfecdd4d8f472bb6c043?editors=0100)

### 1.2 用CSS隐藏图片

可以用CSS隐藏图片，但是它仍然会被加载到页面中。因此，在执行此操作时请小心，如果一个图片应该被隐藏，那么它可能是出于装饰的目的。

```css
img {
  display: none;
}
```

同样，以上内容也不会阻止浏览器加载图片，即使该图片在视觉上是隐藏的。原因是 `<img>` 被视为[替换元素](https://developer.mozilla.org/en-US/docs/Web/CSS/Replaced_element)，因此我们无法控制其加载的内容。

### 1.3 可访问性问题

HTML图片应该通过将 `alt` 属性设置为有意义的描述来访问，这对屏幕阅读器用户来说是非常有帮助的。

但是，如果不需要 `alt` 描述，请不要删除，如果删除了就会读出图片的`src`！这对可访问性(无障碍)环境是非常不利的。

不仅如此，如果图片因为某种原因没有加载，并且它有一个明确的 `alt`，它将作为一个备用值回退显示。既然有一些有趣的事情我想让大家知道，那我们就从视觉上说说吧。

我们有以下图片：

```html
<img class="food-thumb" width="300" height="200" src="cheescake.jpg">
<img class="food-thumb" width="300" height="200" src="cheescake.jpg" alt="">
```

`src` 无效，图片没有正常加载。第一个没有 `alt` 属性，而第二个是空的 `alt` 属性。你能期待这个视觉效果吗？

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/image-techniques/2.png)

没有 `alt` 的图片仍然保留其空间，这很混乱，并且对可访问性不利。虽然另一个折叠了，以适应其空的 `alt` 属性的内容，但由于它的边框，导致了它作为一个小点出现。

但是，当存在 `alt` 属性值时，它将如下所示：

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/image-techniques/3.png)

这不是很好的反馈吗？另外，当图片源发生故障时，可以向其中添加伪元素。

### 1.4 响应式图片

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/image-techniques/4.png)

`<img>` 的优点在于，可以针对特定视口大小将其扩展为具有多个版本的图片。例如，这可用于商品图片。

我们有两种不同的方式来生成一组响应式图片：

#### 1.4.1 `srcset` 属性

```html
<img src="small.jpg" srcset="medium.jpg 500w, large.jpg 800w" alt="">
```

这是一个简单的例子。对我来说，我不认为使用 `srcset` 是根据屏幕宽度显示多个图片大小的完美解决方案。只能让浏览器选择合适的图片，而我们对此无能为力。

#### 1.4.2 HTML Picture 元素

```html
<picture>
  <source srcset="large.jpg" media="(min-width: 800px)" />
  <source srcset="medium.jpg" media="(min-width: 500px)" />
  <img src="small.jpg" />
</picture>
```

另一种选择是使用 `<picture>` 元素。我更喜欢这种方式，因为它更容易预测。

[Demo](https://codepen.io/shadeed/pen/d703aee137f38c138f2323a0252548ac?editors=1100)

### 1.5 调整图片的大小

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/image-techniques/5.png)

我们可以使用 `<img>` 的一大优点就是 `object-fit` 和 `object-position` 属性。它们让我们可以控制 `<img>` 的内容如何调整大小和位置，就像CSS背景图片一样。

> object-fit 的可能值为：fill，contain，cover，none，scale-down

可以这样使用：

```css
img {
  object-fit: cover;
  object-position: 50% 50%;
}
```

现在，我们已经介绍了 `<img>` 元素，是时候继续探索第二种技术了。

## 2. CSS背景图片

当使用CSS背景显示图片时，它需要一个具有内容或特定宽度或高度的元素。通常，背景图片的主要用途应该是用于装饰目的。

### 2.1 如何使用CSS背景图片

简单来说，我们需要一个元素。

```html
<div class="element">Some content</div>
```

```css
.element {
  background: url('cool.jpg');
}
```

### 2.2 多背景

使用CSS背景图片的好处是可以轻松地控制多个背景。考虑下面的例子：

```css
.element {
  background: url('cool-1.jpg'), url('cool-2.jpg');
}
```

### 2.3 隐藏图片

我们可以在特定的视口上隐藏和显示图片，而不会让图片被下载。如果图片没有用CSS设置，就不会被下载。这是比使用 `<img>` 更多的好处。

```css
@media (min-width: 700px) {
  .element {
    background: url('cool-1.jpg');
  }
}
```

在上面的示例中，我们有一个背景图片，仅在视口宽度大于 `700px` 时显示。

###  2.4 可访问性问题

如果使用不正确，背景图片会对无障碍浏览不利。例如，将其用于文章的大拇指，这对文章至关重要。

### 2.5 非开发人员无法下载

你可能会觉得很有趣，但是普通人知道，如果要保存图像，只需单击鼠标左键，然后选择保存即可。CSS背景图片并非如此。您必须先检查元素，然后在DevTools中的 `url` 中打开链接，然后才能下载随CSS添加的图像。

### 2.6 伪元素

可以使用伪元素与CSS背景图片一起使用，例如，在图片的顶部显示一个叠加元素。对于 `<img>` 来说，除非我们为覆盖层添加一个单独的元素，否则无法做到这一点。

## 3. SVG `<Image>`

SVG被认为是图像，它的最大功能在于缩放而不影响质量。另外，使用SVG，我们可以嵌入 `JPG`，`PNG` 或 `SVG` 图像。请参见下面的HTML：

```html
<svg width="200" height="200">
  <image href="cheesecake.jpg" height="100%" width="100%" preserveAspectRatio="xMidYMid slice" />
</svg>
```

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/image-techniques/6.png)

你是否注意到了 `prepareAspectRatio`？这样一来，可以使图像占据SVG的整个宽度和高度，而不会被拉伸或压缩。

当 `<image>` 宽度较大时，它将填充其父级（SVG）宽度而不会拉伸。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/image-techniques/7.png)

这非常类似于CSS中的 `object-fit:cover` 或 `background-size:cover`。

### 3.1 可访问性问题

关于SVG的可访问性，这使我想起了 `<title>` 元素。例如，我们可以像下面这样添加它：

```html
<svg width="200" height="200">
  <title>A photo of blueberry Cheescake</title>
  <image href="cheesecake.jpg" height="100%" width="100%" preserveAspectRatio="xMidYMid slice" />
</svg>
```

我们甚至可以使用 `<desc>` 元素：

```html
<svg width="200" height="200">
  <title>A photo of blueberry Cheescake</title>
  <desc>A meaningful description about the image</desc>
  <image href="cheesecake.jpg" height="100%" width="100%" preserveAspectRatio="xMidYMid slice" />
</svg>
```

### 3.2 非开发人员无法下载

在检查元素并复制图像的URL之前，不可能下载嵌入到SVG中的图像。然而，如果我们想要阻止用户下载特定的图像，这可能是一件好事。至少，它将减少下载图像的机会很容易。

[Demo](https://codepen.io/shadeed/pen/38225ba6b2cd706ca5bff48c131e83ce?editors=1100)

## 4. 使用举例

### 4.1 Hero Section

在构建 hero section 时，我们有时需要在标题和其他内容下面有一个图像。如下图所示：

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/image-techniques/8.png)

注意这里有一个图像。你将如何构建它？好吧，让我先补充一些要求：

- 在与后端CMS整合时，图片应该是很容易动态变化的。
- 其上方有一个覆盖层，有助于使内容易于阅读。
- 图像有三种尺寸：小、中和大。它们每个都用于特定的视口。

在开始解决方案之前，让我们先问问自己这种背景的性质。这是一些入门问题：

1. 为用户保留这个图像很重要吗，还是可以跳过它？
2. 我们是否需要在所有视口尺寸上使用它？
3. 它是静态的还是动态变化的？

#### 4.1.1 Hero - 解决方案1

通过使用多个CSS背景，我们可以将一个背景作为叠加层，将另一个背景作为实际图像。请参阅下面的CSS：

```css
.hero {
  background-image: linear-gradient(rgba(0, 0, 0, 0.4), rgba(0, 0, 0, 0.4)), var('landscape.jpg');
  background-repeat: no-repeat;
  background-size: 100%, cover;
}
```

虽然此解决方案有效，但可以使用JavaScript动态更改背景图片。见下面：

```html
<section class="hero" style="background: linear-gradient(rgba(0, 0, 0, 0.5), rgba(0, 0, 0, 0.5)), url('landscape.jpg');">
  <!-- Hero content -->
</section>
```

我添加了一个内联的CSS背景。虽然这是可行的，但它看起来很丑，而且不实用。

也许我们可以使用CSS变量？让我们来探索一下。

```html
<section class="hero" style="--bg-url: url('landscape.jpg')">
  <!-- Hero content -->
</section>
```

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/image-techniques/9.png)

现在，我们可以轻松地更新 `--bg-url` 变量，这将动态更改背景。这比内联的CSS好一百万倍。

解决方案1要点：

1. 解决方案只有在图像不重要的情况下才是好的
2. 当无法从后端CMS动态更改图片时

[Demo](https://codepen.io/shadeed/pen/17978a2d824fd51a3b27c2c2d099a522)

#### 4.1.2 Hero - 解决方案2

对于此解决方案，我们将使用HTML图像。见下面：

```html
<section class="hero">
  <h2 class="hero__title">Using Images in CSS</h2>
  <p class="hero__desc">An article about which and when to use</p>
  <img src="landscape.jpg" alt="">
</section>
```

在CSS中，我们需要将图片绝对定位在内容下方，并且还需要使用伪元素作为叠加层。

```css
.hero {
  position: relative;
}

.hero img {
  position: absolute;
  left: 0;
  top: 0;
  z-index: -1;
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.hero:after {
  content: "";
  position: absolute;
  left: 0;
  top: 0;
  z-index: -1;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.4);
}
```

此解决方案的优点在于，可以轻松更改图片的 `src` 属性。同样，如果图像很重要，它将会更加有用。

另外，我喜欢使用HTML `<img>` 的地方是可以在图片没有加载的情况下添加一个回退方法，这个回退至少可以保持内容的可读性。

```css
.hero img {
  /* 其他样式 */
  background: #2962ff;
}
```

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/image-techniques/10.png)

好处是，只有在图像源失败的情况下，背景才会起作用。那不是很酷吗？

[Demo](https://codepen.io/shadeed/pen/73a2ca78141fcab39d6db9d5bd982728?editors=1100)

### 4.2 网站Logo

Logo是很重要的，因为它可以将网站与其他网站区分开。要嵌入Logo，我们有两种选择：

- `<img>` --> png，jpg，或者 svg
- 内联SVG
- 背景图像

让我们学习使用哪种技术以及如何选择合适的技术。

#### 4.2.1 带有详细信息的Logo

当一个LOGO有很多细节或形状时，用内嵌式SVG可能没有那么多好处。我建议使用 `<img>`，图片类型可以是`png`、`jpg` 或 `svg`。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/image-techniques/11.png)

```html
<a href="#"><img src="logo.svg" alt="Nature Food"></a>
```

#### 4.2.2 一个需要动画的简单Logo

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/image-techniques/12.png)

我们有一个简单的Logo，其中包含形状和文字。悬停时，形状和文本需要更改颜色。怎么做？对我来说最好的解决方案是使用内联SVG。

```html
<a href="#">
  <svg class="logo" width="115" height="47" xmlns="http://www.w3.org/2000/svg">
    <g transform="translate(-5 -5)" fill="none" fill-rule="evenodd">
      <rect fill="#D8D8D8" transform="rotate(45 28.5 28.5)" x="9" y="9" width="39" height="39" rx="11" />
      <text font-family="Rubik-Medium, Rubik" font-size="25" font-weight="400" fill="#6F6F6F">
        <tspan x="63.923" y="36.923">Rect</tspan>
      </text>
    </g>
  </svg>
</a>
```

```css
.logo rect,
.logo text {
  transition: 0.3s ease-out;
}

.logo:hover rect,
.logo:hover text {
  fill: #4a7def;
}
```

[Demo](https://codepen.io/shadeed/pen/4005077cc543647148007f4834c0585c?editors=0100)

#### 4.2.3 响应式Logo

这让我想起了Smashing Magazine的Logo，我喜欢它从一个小图标变成一个完整的Logo。参见下面的模型：

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/image-techniques/13.png)

完美的解决方案是 `<picture>` 元素，可以在其中添加Logo的两个版本。见下文：

```html
<a class="logo" href="/">
  <picture>
    <source media="(min-width: 1350px)" srcset="sm-logo--full.svg">
    <img src="sm-logo.svg" alt="Smashing Magazine">
  </picture>
</a>
```

在CSS中，我们需要将视口的宽度更改为等于或大于 `1350px`。

```css
.logo {
  display: inline-block;
  width: 45px;
}

@media (min-width: 1350px) {
  .logo {
    width: 180px;
  }
}
```

简单明了的解决方案。

[Demo](https://codepen.io/shadeed/pen/6cf55d4e87b7c443820bd5f8694587a8?editors=1100)

#### 4.2.4 渐变Logo

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/image-techniques/14.png)

当Logo具有渐变时，从Illustrator或Sketch等设计应用程序将其导出的过程可能并不完美，有时会中断。

使用SVG，我们可以轻松地为徽标添加渐变，我添加了 `<linearGradient>` 并将其用作文本填充。

```html
<svg class="logo" width="115" height="47" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="gradient" x1="0%" y1="100%" x2="0%" y2="0%">
      <stop offset="0%" stop-color="#4a7def"></stop>
      <stop offset="50%" stop-color="#ab4787"></stop>
    </linearGradient>
  </defs>
  <g transform="translate(-5 -5)" fill="none" fill-rule="evenodd">
    <rect fill="#AB4787" transform="rotate(45 28.5 28.5)" x="9" y="9" width="39" height="39" rx="11" />
    <text font-family="Rubik-Medium, Rubik" font-size="30" font-weight="400" fill="url(#gradient)">
      <tspan x="63.923" y="36.923">Rect</tspan>
    </text>
  </g>
</svg>
```

[Demo](https://codepen.io/shadeed/pen/9bf3bee3d08a40411effb5d65f25b5c1?editors=1100)

### 4.3 用户头像

对于用户头像，它们有很多形状，但最常见的是矩形或圆形。在这个用例中，我很有兴趣解释一个你可能会觉得有用的重要技巧。

首先，我们来看看下面的模拟图。注意，我们有一个完美的头像，而且它们是100%的清晰。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/image-techniques/15.png)

但是，当用户上传半白色头像或非常浅的头像时，此设计将失败。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/image-techniques/16.png)

注意到上面的模拟图中，你要真的聚焦好了才知道里面有一个圆形。这就是一个问题，为了解决这个问题，我们应该在头像内部添加一个边框，这将是在图像太亮的情况下作为备用。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/image-techniques/17.png)

我们有两种选择可以做到这一点：

- `<img>` 元素
- 具有 `<div>` 的 `<img>`
- 具有CSS背景的 `<div>`
- SVG `<image>`

其中哪一个最好？让我们来探索。

#### 4.3.1 使用 HTML `<img>`

您可能想到的第一件事就是添加边框，对吗？让我们来探讨一下（很抱歉，在下面的部分中，您可能会看到很多我的脸）。

```css
.avatar {
  border: 2px solid #f2f2f2;
}
```

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/image-techniques/18.png)

我们的目标是要有一个与图像相融合的内部边框，具有实边是不实际的。

#### 4.3.2 使用具有 `<div>` 的 `<img>`

现在的问题是，要添加内边框，我们不能使用内部 `box-shadow`，因为它对图像不起作用。解决的方法是用 `<div>` 包裹头像，并添加一个专门用于内边框的元素。

```html
<div class="avatar-wrapper">
  <img class="avatar" src="shadeed2.jpg" alt="A photo of Ahmad Shadeed">
  <div class="avatar-border"></div>
</div>
```

```css
.avatar-wrapper {
  position: relative;
  width: 150px;
  height: 150px;
}

.avatar-border {
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  border-radius: 50%;
  border: 2px solid rgba(0, 0, 0, 0.1);
}
```

通过在 `<div>` 上设置一个10%的黑色边框，我们可以确保边框与暗色图像融合，只有在图像颜色较浅的情况下，边框才会显现出来。请看下面的模拟图。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/image-techniques/19.png)

[Demo](https://codepen.io/shadeed/pen/da23d9a18dac14692a97e1bc6e86a5ff?editors=1100)

#### 4.3.3 具有CSS背景的 `<div>`

如果我要使用 `<div>` 来显示头像，则可能表示该图像具有装饰性。我记得一个用例，它是分散在页面中的随机头像。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/image-techniques/20.png)

我们可以有这样的东西：

```html
<div class="avatar" style="--img-url: url(shadeed2.jpg)"></div>
```

```css
.avatar {
  background: var(--img-url) center/cover;
  width: 150px;
  height: 150px;
  border-radius: 50%;
  box-shadow: inset 0 0 0 2px rgba(#000, 0.1);
}
```

[Demo](https://codepen.io/shadeed/pen/39eb9dac364ec15b9ab9bae7fe3a7148?editors=0100)

#### 4.3.4 SVG `<image>`

对我来说，这是最有趣的解决方案。我在检查[Facebook的新设计](https://ishadeed.com/article/new-facebook-css/)时注意到了它。

```html
<svg role="none" style="height: 36px; width: 36px;">
  <mask id="avatar">
    <circle cx="18" cy="18" fill="white" r="18"></circle>
  </mask>
  <g mask="url(#avatar)">
    <image x="0" y="0" height="100%" preserveAspectRatio="xMidYMid slice" width="100%" xlink:href="avatar.jpg" style="height: 36px; width: 36px;"></image>
    <circle cx="18" cy="18" r="18"></circle>
  </g>
</svg>
```

我先对其进行剖析，它包含以下内容：

1. 用于将图像剪切为圆形的蒙层
2. 对其应用了蒙层的group
3. 图像本身带有 `preserveAspectRatio = "xMidYMid"`
4. 用于内边框的圆圈

在CSS中，我们将具有以下内容：

```css
circle {
  stroke-width: 2;
  stroke: rgba(0, 0, 0, 0.1);
  fill: none;
}
```

[Demo](https://codepen.io/shadeed/pen/b17d34b5c23cc90fdc4573779544c8c7?editors=0100)

这就是用户头像用例的全部内容。

### 4.5 带图标的输入框

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/image-techniques/21.png)

通常会看到带有图标的输入框，如何添加？当输入被聚焦时会发生什么？让我们来探索一下。

```html
<p>
  <label for="name">Full name</label>
  <input type="text" id="name">
</p>
```

对我来说，处理这种情况的最佳解决方案是CSS背景图片。简单，快捷，不需要添加更多元素。

```css
input {
  background-color: #fff;
  background-image: url('user.svg');
  background-size: 20px 20px;
  background-position: left 10px center;
  background-repeat: no-repeat;
}
```

要更改焦点上的图标颜色，我们可以使用url编码的SVG，并且很容易做到这一点。Yoksel的这个[工具](https://yoksel.github.io/url-encoder/)很棒。

[Demo](https://codepen.io/shadeed/pen/81f64125425dba02f7a0b725fa837381?editors=0110)

### 4.6 CSS 打印

用户可能需要打印web页面。假设我们有一份食谱，你想把它打印出来，这样你就可以在厨房里看它，而不需要查看你的手机或电脑。

对于包含说明性步骤的菜谱，重要的是将它们打印出来，否则用户将无法从打印web页面中获得任何好处。

#### 4.6.1 避免使用图像作为CSS背景

当一个图像作为CSS背景被包含进来时，它不会被打印出来，取而代之的是一个空白区域。如下图所示：

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/image-techniques/22.png)

就是这样的情况。我们可以通过强制浏览器显示图片来解决这个问题，虽然这对Firefox和IE来说不起作用。

```css
.element {
  background: url('cheesecake.png') center/cover no-repeat;
  -webkit-print-color-adjust: exact; /* 强制浏览器以打印模式呈现背景 */
}
```

但是，使用HTML `<img>` 会更安全，因为它可以打印而不会出现任何问题。

[Demo](https://codepen.io/shadeed/pen/97d7b13765718132a4fe71b6bc43e388?editors=0100)

全文完。


