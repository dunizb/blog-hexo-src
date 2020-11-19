---
title: CSS3贝塞尔曲线实战：创建链接悬停动画效果
date: 2020-11-19 13:55:14
categories:
  - 技术
tags:
  - CSS
  - 实战
---

我们将使用 CSS3 动画过渡来创建简单但引人入胜的链接悬停效果，将鼠标悬停在链接上时，会弹出一个小弹出框。

我们还将看一下**CSS3 Cubic-Bezier（贝塞尔）曲线**，它是 CSS 过渡，为弹出框提供了更加流畅的运动，而不是僵化的机械运动。

这是我们最后的效果：

![](https://content.taskhub.work/pub/wR01bd-aGrvQ1P)

让我们开始吧！

<!-- more -->

## HTML 部分

这是我们链接的 HTML，图标来自 iconfont.cn。

```html
<div class="container">
  <section>
    <a href="#">
      <i class="fab fa-instagram"></i>
      <span>Instagram</span>
    </a>
    <a href="#">
      <i class="fab fa-github"></i>
      <span>Github</span>
    </a>
  </section>
</div>
```

当您将鼠标悬停在链接上时，span 标签将成为弹出框。接下来，我们进入 CSS。

## CSS 样式和动画

我们将 div 容器居中，以使两个链接在屏幕上居中。这也使对小弹出框进行动画处理变得容易，因为它们将从链接的顶部弹出。

```css
div.container {
  display: inline-block;
  position: absolute;
  top: 50%;
  left: 50%;
  -ms-transform: translate(-50%, -50%);
  -webkit-transform: translate(-50%, -50%);
  transform: translate(-50%, -50%);
}
```

接下来，我们对链接进行样式设置，创建简单的背景悬停效果，并定位社交媒体图标。

```css
a {
  color: #fff;
  background: #8a938b;
  border-radius: 4px;
  text-align: center;
  text-decoration: none;
  position: relative;
  display: inline-block;
  width: 120px;
  height: 100px;
  padding-top: 12px;
  margin: 0 2px;
  -o-transition: all 0.5s;
  -webkit-transition: all 0.5s;
  -moz-transition: all 0.5s;
  transition: all 0.5s;
  -webkit-font-smoothing: antialiased;
}
a:hover {
  background: #5a665e;
}
i {
  font-size: 45px;
  vertical-align: middle;
  display: inline-block;
  position: relative;
  top: 20%;
}
```

接下来，我们将对弹出文本进行样式设置和动画处理。

```css
a span {
  color: #666;
  position: absolute;
  font-family: "Chelsea Market", cursive;
  bottom: 0;
  left: -15px;
  right: -15px;
  padding: 15px 7px;
  z-index: -1;
  font-size: 14px;
  border-radius: 5px;
  background: #fff;
  visibility: hidden;
  opacity: 0;
  -o-transition: all 0.5s cubic-bezier(0.68, -0.55, 0.265, 1.55);
  -webkit-transition: all 0.5s cubic-bezier(0.68, -0.55, 0.265, 1.55);
  -moz-transition: all 0.5s cubic-bezier(0.68, -0.55, 0.265, 1.55);
  transition: all 0.5s cubic-bezier(0.68, -0.55, 0.265, 1.55);
} /* 当图标处于悬停状态时，文本将弹出 */
a:hover span {
  bottom: 130px;
  visibility: visible;
  opacity: 1;
}
```

CSS3 **Cubic-Bezier**曲线由四个点**p0**，**p1**，**p2**和**p3**定义。 p0 点是曲线的起点，而 p3 点是曲线的终点。曲线越线性，运动就越僵硬(或不那么流畅)。

如果一个点一开始是正数，而下一个点是负数，那么运动一开始就会很慢。当点值变得比之前的点值高时，运动加快。

这就是 CSS 中 Cubic-Bezier 点的含义。由于动画短，所以动作很细微。弹出框从正方形底部开始时缓慢开始，然后开始加速到顶部。

尽管您可以创建没有 Cubic-Bezier 曲线过渡的动画，但动画的差异如下：

有 Cubic-Bezier 曲线过渡的动画

![](https://content.taskhub.work/pub/93BpVz-BjqAdk)

没有 Cubic-Bezier 曲线过渡的动画

![](https://content.taskhub.work/pub/pjeX0w-LdQDQp9)

可以看到，动画为悬停效果增添了生气。

最后一组 CSS 涉及样式化弹出框底部的小箭头。要了解有关在 CSS 中如何制作三角形的更多信息，请查看此 CSS 技巧文章。

## 总结

我们创建了一个简约的按钮样式链接。链接具有基本的背景悬停效果，但我们并没有止步于此。我们添加了一个小弹出框来显示链接的文本。在 CSS3 Cubic-Bezier 塞尔曲线的帮助下，动画流畅且令人愉悦。

这类知识非常有用，可以作为你显示社交媒体帐户的网站设计的一部分。

本文示例演示和完整代码请访问如下地址，建议 PC 端打开 [https://coding.zhanbing.site](https://coding.zhangbing.site/view.html?url=./list/Animated-Link-CSS3-Cubic-Bezier.html)

![](https://content.taskhub.work/pub/83mpbo-wzpkDQM)
