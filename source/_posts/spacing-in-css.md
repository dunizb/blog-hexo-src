---
title: 译|CSS中的间距，前端开发中各种设置间距的优点缺点及实例
date: 2020-05-08 14:35:33
img: https://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/banner.png
categories:
  - 技术
tags:
  - 前端
  - CSS
  - 翻译
---

如果两个或多个元素很接近，那么用户就会认为它们以某种方式属于彼此。当对多个设计元素进行分组时，用户可以根据它们之间的空间大小来决定它们之间的关系。没有间距，用户将很难浏览页面并知道哪些内容相关而哪些内容无关。

<!-- more -->

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/banner.png)

在本文中，我将介绍有关 CSS 中的间距，实现此间距的不同方法以及何时使用 padding 或 margin 所需的所有知识。

## 间距类型

CSS 中的间距有两种类型，一种在元素外部，另一种在元素内部。对于本文，我将其称为**outer**和**inner**。假设我们有一个元素，它内部的间距是**inner**，外部的间距是**outer**。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/1.png)

在 CSS 中，间距可以如下：

```css
.element {
  padding: 1rem;
  margin-bottom: 1rem;
}
```

我使用 `padding` 来填充内部间距，使用 `margin` 来填充外部间距。很简单，不是吗？但是，当处理具有许多细节和子元素的组件时，这会变得越来越复杂。

### margin 外部间距

它用于增加元素之间的间距。例如，在上一个示例中，我添加了 `margin-bottom:1rem` 在两个堆叠的元素之间添加垂直间距。

由于可以沿四个不同的方向（top、right、 bottom、left）添加 margin，因此在深入研究示例和用例之前，一定要阐明一些基本概念，这一点很重要。

### margin 折叠

简而言之，当两个垂直元素具有 margin，并且其中一个元素的 margin 大于另一个元素时，发生边距折叠。在这种情况下，将使用更大的 margin，而另一个将被忽略。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/2.png)

在上面的模型中，一个元素有 `margin-bottom`，另一个元素有 `margin-top`，边距较大的元素获胜。

为避免此类问题，建议按照本文使用单向边距。此外，CSS Tricks 还在页边距底部和页边距顶部之间进行了投票。61%的开发者更喜欢 `margin-bottom` 而不是 `margin-top`。

请在下面查看如何解决此问题：

```javascript
.element:not(:last-child) {
  margin-bottom: 1rem;
}
```

使用 `:not` CSS 选择器，您可以轻松地删除最后一个子元素的边距，以避免不必要的间距。

另一个与边距折叠相关的例子是子节点和父节点。让我们假设如下:

```html
<div class="parent">
  <div class="child">I'm the child element</div>
</div>
```

```css
.parent {
  margin: 50px auto 0 auto;
  width: 400px;
  height: 120px;
}

.child {
  margin: 50px 0;
}
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/3.png)

请注意，子元素固定在其父元素的顶部。那是因为它的边距折叠了。根据 W3C，以下是针对该问题的一些解决方案：

- 在父元素上添加 `border`
- 将子元素显示更改为 `inline-block`

一个更直接的解决方案是将 `padding-top` 添加到父元素。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/4.png)

### 负 margin

它可以与四个方向一起使用以留出余量，在某些用例中非常有用。让我们假设以下内容：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/5.png)

父节点具有 `padding:1rem`，这导致子节点从顶部、左侧和右侧偏移。但是，子元素应该紧贴其父元素的边缘。负 margin 可以助你一臂之力。

```css
.parent {
  padding: 1rem;
}

.child {
  margin-left: -1rem;
  margin-right: -1rem;
  margin-top: -1rem;
}
```

如果您想更多地挖负 margin，建议阅读[这篇](https://www.quirksmode.org/blog/archives/2020/02/negative_margin.html)文章。

### padding 内部间距

如前所述，padding 在元素内部增加了一个内间距。它的目标可以根据使用的情况而变化。

例如，它可以用于增加链接之间的间距，这将导致链接的可点击区域更大。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/7.jpg)

必须提出的是，垂直方向的 padding 对于那些具有 `display:inline` 的元素不适用，比如 `<span>` 或 `<a>`。如果添加了内边距，它不会影响元素，内边距将覆盖其他内联元素。

这只是一个友好的提醒，应该更改内联元素的 `display` 属性。

```css
.element span {
  display: inline-block;
  padding-top: 1rem;
  padding-bottom: 1rem;
}
```

## CSS Grid 间隙

在 CSS 网格中，可以使用 `grid-gap` 属性轻松在列和行之间添加间距。这是行和列间距的简写。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/8.png)

```css
.element {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-gap: 16px; /* 为行和列都增加了16px的间隙。 */
}
```

gap 属性可以使用如下:

```css
.element {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-row-gap: 24px;
  grid-column-gap: 16px;
}
```

## CSS Flexbox 间隙

`gap` 是一个提议的属性，将用于 CSS Grid 和 flexbox，撰写本文时，它仅在 Firefox 中受支持。

```css
.element {
  display: flex;
  flex-wrap: wrap;
  gap: 16px;
}
```

## CSS 定位

它可能不是直接的元素间距方式，但在一些设计案例中却起到了一定的作用。例如，一个绝对定位的元素需要从其父元素的左边缘和上边缘定位 `16px`。

考虑以下示例，带有图标的卡片，其图标应与其父对象的左上边缘隔开。在这种情况下，将使用以下 CSS：

```css
.category {
  position: absolute;
  left: 16px;
  top: 16px;
}
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/9.png)

## 用例和实际示例

在这一节中，你将回顾一下在日常工作中，你在处理 CSS 项目时，会遇到的不同用例。

### header 组件

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/10.png)

在这种情况下，标题具有 logo，导航和用户个人资料。你能猜出 CSS 中的间距应该如何设置吗？好吧，让我为你添加一个骨架模型。

```html
<header class="c-header">
  <h1 class="c-logo"><a href="#">Logo</a></h1>
  <div class="c-header__nav">
    <nav class="c-nav">
      <ul>
        <li><a href="#">...</a></li>
      </ul>
    </nav>
    <a href="#" class="c-user">
      <span>Ahmad</span>
      <img class="c-avatar" src="shadeed.jpg" alt="" />
    </a>
  </div>
</header>
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/11.jpg)

Header 的左侧和右侧都有 padding，这样做的目的是防止内容物紧贴在边缘上。

```css
.c-header {
  padding-left: 16px;
  padding-right: 16px;
}
```

对于导航，每个链接在垂直和水平侧均应具有足够的填充，因此其可单击区域可以很大，这将增强可访问性。

```css
.c-nav a {
  display: block;
  padding: 16px 8px;
}
```

对于每个项目之间的间距，您可以使用 `margin` 或将 `<li>` 的 `display` 更改为 `inline-block`。内联块元素在它的兄弟元素之间添加了一点空间，因为它将元素视为字符。

```css
.c-nav li {
  /* 这将创建你在骨架中看到的间距 */
  display: inline-block;
}
```

最后，头像（avatar）和用户名的左侧有一个空白。

```css
.c-user img,
.c-user span {
  margin-left: 10px;
}
```

请注意，如果你要构建多语言网站，建议使用如下所示的 CSS 逻辑属性。

```css
.c-user img,
.c-user span {
  margin-inline-start: 1rem;
}
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/12.png)

请注意，分隔符周围的间距现在相等，原因是导航项没有特定的宽度，而是具有 padding。结果，导航项目的宽度基于其内容。以下是解决方案：

- 设置导航项目的最小宽度
- 增加水平 padding
- 在分隔符的左侧添加一个额外的 margin

最简单，更好的解决方案是第三个解决方案，即添加 `margin-left`。

```css
.c-user {
  margin-left: 8px;
}
```

### 网格系统中的间距：Flexbox

网格是间隔最常用的情况之一。考虑以下示例：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/13.jpg)

间距应在列和行之间。考虑以下 HTML 标记：

```html
<div class="wrapper">
  <div class="grid grid--4">
    <div class="grid__item">
      <article class="card"><!-- Card content --></article>
    </div>
    <div class="grid__item">
      <article class="card"><!-- Card content --></article>
    </div>
    <!-- And so on.. -->
  </div>
</div>
```

通常，我更喜欢将组件封装起来，并避免给它们增加边距。由于这个原因，我有 `grid__item`元素，我的 card 组件将位于其中。

```css
.grid--4 {
  display: flex;
  flex-wrap: wrap;
}

.grid__item {
  flex-basis: 25%;
  margin-bottom: 16px;
}
```

使用上述 CSS，每行将有四张卡片。这是在它们之间添加空格的一种可能的解决方案：

```css
.grid__item {
  flex-basis: calc(25% - 10px);
  margin-left: 10px;
  margin-bottom: 16px;
}
```

通过使用 CSS `calc()` 函数，可以从 `flex-basis` 中扣除边距。如你所见，这个方案并不是那么简单。我比较喜欢的是下面这个办法。

- 向网格项目添加 `padding-left`
- 在网格父节点上增加一个负值 `margin-left`，其 `padding-left` 值相同。

几年前，我从[CSS Wizardy](https://csswizardry.com/)那里学到了上述解决方案（我忘记了文章标题，如果您知道，请告诉我）。

```css
.grid--4 {
  display: flex;
  flex-wrap: wrap;
  margin-left: -10px;
}

.grid__item {
  flex-basis: 25%;
  padding-left: 10px;
  margin-bottom: 16px;
}
```

我之所以用了负 `margin-left`，是因为第一张卡有 `padding-left`，而实际上不需要。所以，它将把 `.wrapper` 元素推到左边，取消那个不需要的空间。

另一个类似的概念是在两边都添加填充，然后边距为负。这是 Facebook 故事的一个示例：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/14.jpg)

```css
.wrapper {
  margin-left: -4px;
  margin-right: -4px;
}

.story {
  padding-left: 4px;
  padding-right: 4px;
}
```

### 网格系统中的间距：CSS Grid

现在，到了激动人心的部分！使用 CSS Grid，你可以很容易地使用 `grid-gap` 添加间距。此外，你不需要关心网格项的宽度或底部空白，CSS Grid 为你做者一切！

```css
.grid--4 {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  grid-gap: 1rem;
}
```

就是这样！难道不是那么容易和直接吗？

### 按需定制

我真正喜欢 CSS Grid 的地方是 `grid-gap` 只在需要的时候才会被应用。考虑下面的模型。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/15.jpg)

没有 CSS 网格，就不可能拥有这种灵活性。首先，请参见以下内容：

```css
.card:not(:last-child) {
  margin-bottom: 16px;
}

@media (min-width: 700px) {
  .card:not(:last-child) {
    margin-bottom: 0;
    margin-left: 1rem;
  }
}
```

不舒服吧？这个如何？

```css
.card-wrapper {
  display: grid;
  grid-template-columns: 1fr;
  grid-gap: 1rem;
}

@media (min-width: 700px) {
  .card-wrapper {
    grid-template-columns: 1fr 1fr;
  }
}
```

完成了！容易得多。

### 处理底部 margin

假设以下组件堆叠在一起，每个组件都有底边距。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/16.jpg)

注意最后一个元素有一个空白，这是不正确的，因为边距只能在元素之间。

可以使用以下解决方案之一解决此问题：

解决方案 1-CSS `:not` 选择器

```css
.element:not(:last-child) {
  margin-bottom: 16px;
}
```

解决方案 2：相邻兄弟组合器

```css
.element + .element {
  margin-top: 16px;
}
```

虽然解决方案 1 具有吸引力，但它具有以下缺点：

- 它会导致 CSS 的特异性问题。在使用 `:not` 选择器之前不可能覆盖它。
- 万一设计中有不止一列，它将无法正常工作。参见下图。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/17.jpg)

关于解决方案 2，它没有 CSS 特异性问题。但是，它只能处理一个列栈。

更好的解决方案是通过向父元素添加负边距来取消不需要的间距。

```css
.wrapper {
  margin-bottom: -16px;
}
```

它用一个等于底部间距的值将元素推到底部。注意不要超过边距值，因为它会与同级元素重叠。

### Card 组件

Oh，如果我想把所有细节的 Card 组件间距都写进去的话，最后可能会出现书本上的内容。我就突出一个大概的模式，看看间距应该如何应用。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/18.png)

你能想到此卡片在哪里使用间距吗？参见下图。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/19.png)

```html
<article class="card">
  <a href="#">
    <div class="card__thumb">
      <img src="food.jpg" alt="" />
    </div>
    <div class="card__content">
      <h3 class="card__title">Cinnamon Rolls</h3>
      <p class="card__author">Chef Ahmad</p>
      <div class="card__rating"><span>4.9</span></div>
      <div class="card__meta"><!-- --></div>
    </div>
  </a>
</article>
```

```css
.card__content {
  padding: 10px;
}
```

上面的 padding 将向其中的所有子元素添加一个偏移量。然后，我将添加所有边距。

```css
.card__title,
.card__author,
.card__rating {
  margin-bottom: 10px;
}
```

对于评分和 `.car__meta` 元素之间的分隔线，我将添加它作为边框。

```css
.card__meta {
  padding-top: 10px;
  border-top: 1px solid #e9e9e9;
}
```

糟糕！由于对父元素 `.card__content` 进行了填充，因此边框没有粘在边缘上。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/20.png)

是的，你猜对了！负边距是解决办法。

```css
.card__meta {
  padding-top: 10px;
  border-top: 1px solid #e9e9e9;
  margin: 0 -10px;
}
```

糟糕，再次！出了点问题。内容粘在边缘！

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/21.png)

为了解决这个问题，内容应该从左右两边加垫（呵呵，看来加垫是个新词）。

```css
.card__meta {
  padding: 10px 10px 0 10px;
  border-top: 1px solid #e9e9e9;
  margin: 0 -10px;
}
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/22.png)

### 文章内容

我相信这是一个非常非常普遍的用例。由于文章内容来自 CMS（内容管理系统），或者是由 Markdown 文件自动生成的，因此无法为元素添加类。

考虑下面的示例，其中包含标题，段落和图像。

```html
<div class="wrapper">
  <h1>Spacing Elements in CSS</h1>
  <p><!-- content --></p>
  <h2>Types of Spacing</h2>
  <img src="spacing-1.png" alt="" />
  <p><!-- content --></p>
  <p><!-- content --></p>
  <h2>Use Cases</h2>
  <p><!-- content --></p>
  <h3>Card Component</h3>
  <img src="use-case-card-2.png" alt="" />
</div>
```

为了使它们看起来不错，间距应保持一致并谨慎使用。我从[type-scale.com](https://type-scale.com/)借了一些样式。

```css
h1,
h2,
h3,
h4,
h5 {
  margin: 2.75rem 0 1.05rem;
}

h1 {
  margin-top: 0;
}

img {
  margin-bottom: 0.5rem;
}
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/23.jpg)

如果一个 `<p>` 后面有一个标题，例如“Types of Spacing”，那么 `<p>` 的 `margin-bottom` 将被忽略。你猜到了，那是因为页边距折叠。

### Just In Case Margin

我喜欢把这个叫做 "Just in case" margin，因为这就是字面意思。考虑一下下面的模型图。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/24.png)

当元素靠近的时候，它们看起来并不好看。我是用 flexbox 搭建的。这项技术称为“对齐移位包装”，我从 CSS Tricks 中学到了它的名称。

```css
.element {
  display: flex;
  flex-wrap: wrap;
}
```

当视口尺寸较小时，它们的确以新行结尾。见下文：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/25.png)

需要解决的是中间设计状态，即两件物品仍然相邻，但两件物品之间的间距为零的设计状态。在这种情况下，我倾向于向元素添加一个 `margin-right`，这样可以防止它们相互接触，从而加快 `flex-wrap` 的工作速度。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/26.png)

### CSS 书写模式

根据 MDN：

> writing-mode CSS 属性设置了文本行是水平还是垂直排列，以及块的前进方向。

你是否曾经考虑过将边距与具有不同 `writing-mode` 的元素一起使用时应如何表现？考虑以下示例。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/27.png)

```css
.wrapper {
  /* 使标题和食谱在同一行 */
  display: flex;
}

.title {
  writing-mode: vertical-lr;
  margin-right: 16px;
}
```

标题被旋转了 90 度，在它和图像之间应该有一个空白区。结果表明，基于 `writing-mode` 的页边距工作得非常好。

我认为这些用例就足够了。让我们继续一些有趣的概念！

### 组件封装

大型设计系统包含许多组件。向其直接添加边距是否合乎逻辑？

考虑以下示例。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/28.png)

```html
<button class="button">Save Changes</button>
<button class="button button-outline">Discard</button>
```

按钮之间的间距应在哪里添加？是否应将其添加到左侧或右侧按钮？也许你可以如下使用相邻同级选择器：

```css
.button + .button {
  margin-left: 1rem;
}
```

这是不好的。如果只有一个按钮的情况怎么办？或者，当它垂直堆叠时在移动设备上将如何工作？很多很多的复杂性。

## 使用抽象组件

解决上述问题的一种方法是使用抽象的组件，其目标是托管其他组件，就像 Max Stoiber 所说的那样，这是将管理边距的责任移到了父元素上，让我们以这种思维方式重新思考以前的用例。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/29.png)

```html
<div class="list">
  <div class="list__item">
    <button class="button">Save Changes</button>
  </div>
  <div class="list__item">
    <button class="button button-outline">Discard</button>
  </div>
</div>
```

注意，我添加了一个包装器，并且每个按钮现在都包装在其自己的元素中。

```css
.list {
  display: flex;
  align-items: center;
  margin-left: -1rem; /* 取消第一个元素的左空白 */
}

.list__item {
  margin-left: 1rem;
}
```

就是这样！而且，将这些概念应用到任何 JavaScript 框架中都相当容易。例如：

```html
<List>
  <button>Save Changes</button>
  <button outline>Discard</button>
</List>
```

你使用的 JavaScript 工具应该将每个项包装在自己的元素中。

## 间隔组件

是的，你没看错。我在这篇文章中讨论了避免 margin 的概念，并使用间隔组件来代替它们。

让我们假设一个区域需要从左到右 24px 的空白，并记住这些限制：

- margin 不能直接用于组件，因为它是一个已经构建的设计系统。
- 它应该是灵活的。间距可能在 X 页上，但不在 Y 页上。

我在检查[Facebook 的新设计 CSS](https://ishadeed.com/article/new-facebook-css/)时首先注意到了这一点。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/30.jpg)

那是一个 `<div>`，内联样式宽度：`16px`，它唯一的作用是在左边缘和包装器之间增加一个空白空间。

引述这本 React 游戏手册中的内容。

> 但在现实世界中，我们确实需要组件之外的间距来合成页面和场景，这就是 margin 渗入组件代码的地方：用于组件的间距组合。

我同意。对于大型设计系统，不断向组件添加 margin 是不可伸缩的。这将最终导致一个令人毛骨悚然的代码。

### 间隔组件的挑战

现在你了解了间隔组件的概念，让我们深入研究使用它们时遇到的一些挑战。这是我想到的一些问题：

- 间隔组件如何在父级内部取其宽度或高度？在水平布局和垂直布局中，它将如何工作？
- 我们是否应该根据其父项的显示类型（Flex，Grid）对它们进行样式设置

让我们一一解决上述问题。

### 调整间隔组件的大小

可以创建一个接受不同变化和设置的间隔。我不是 JavaScript 开发人员，但我认为他们将其称为 Props。考虑来自 styled-system.com 的以下内容：

我们在一个 header 和一个 section 之间有一个隔板。

```html
<header />
<spacer mb="{4}" />
<section />
```

虽然这个有点不一样，一个间隔器在 logo 和导航之间建立一个自动间隔。

```html
<Flex>
  <Logo />
  <Spacer m="auto" />
  <Link>Beep</Link>
  <Link>Boop</Link>
</Flex>
```

你可能会认为，通过添加 `justify-content:space-between`，使用 CSS 做到这一点相当容易。

如果设计上需要改一下怎么办？那么，如果是这样的话，样式就应该改了。

见下文，你看到那里的灵活性了吗？

```html
<Flex>
  <Logo />
  <Link>Beep</Link>
  <Link>Boop</Link>
  <Spacer m="auto" />
  <Link>Boop</Link>
</Flex>
```

那么，如果是这样的话，就应该改变样式。你看出来有什么灵活性了吗？对于尺寸调整部分，可以根据其母体的尺寸调整间隔的尺寸。

对于上面的内容，也许你可以做一个叫 `grow` 的 prop，可以计算成 `flex-grow:1` 在 CSS 中。

```html
<Flex>
  <spacer grow="1" />
</Flex>
```

### 使用伪元素

我考虑过的另一个想法是使用伪元素创建间隔符。

```html
.element:after { content: ""; display: block; height: 32px;
}
```

也许我们可以选择通过一个伪元素而不是一个单独的元素来添加间隔器？例如：

```html
<Header spacer="below" type="pseudo" length="32">
  <Logo />
  <Link>Home</Link>
  <Link>About</Link>
  <Link>Contact</Link>
</Header>
```

直到今天，我还没有在项目中使用间隔组件，但是我期待可以使用它们的用例。

## CSS 数学函数：Min()，Max()，Clamp()

有可能有动态的边距吗?例如，根据视口宽度设置具有最小值和最大值的空白。答案是肯定的！我们可以。最近，Firefox 75 支持 CSS 数学函数，这意味着根据 CanIUse 在所有主流浏览器中都支持 CSS 数学函数。

让我们回想一下 Grid 用例，以了解如何在其中使用动态间距。

```css
.wrapper {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-gap: min(2vmax, 32px);
}
```

下面是 `min(2vmax，32px)` 的意思：使用一个等于 `2vmax` 的间隙，但不能超过 `32px`。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202005/spacing-in-css/dynamic-spacing.gif)

拥有这样的灵活性确实令人惊讶，并且为我们提供了构建更多动态和灵活布局的许多可能性。

---

> 来源：[https://ishadeed.com](https://ishadeed.com/article/spacing-in-css/#using-pseudo-elements)，作者：Ahmad Shadeed
>
> 翻译：公众号《前端外文精选》
