---
title: CSS 定位之 sticky 定位及应用
date: 2018-12-31 19:12:36
categories:
- 技术
tags:
- CSS
---

CSS 有两个最重要的基本属性，前端开发必须掌握：`display` 和 `position`。
<!-- more -->

`display` 属性指定网页的布局：`flex` 和网格布局 `grid`。本文介绍非常有用的 `position` 属性的 `sticky` 定位。

`position` 属性用来指定一个元素在网页上的位置，一共有5种定位方式，即 `position` 属性主要有五个值。
1. static
2. relative
3. fixed
4. absolute
5. sticky

前四个相信大家都很熟悉，最后一个 `sticky` 是2017年浏览器才支持的。

## 1.sticky 属性值功能

`sticky` 跟前面四个属性值都不一样，它会产生动态效果，很像 `relative` 和 `fixed `的结合：一些时候是 `relative` 定位（定位基点是自身默认位置），另一些时候自动变成 `fixed` 定位（定位基点是视口）。

因此，它能够形成“动态固定”的效果。比如，网页的搜索工具栏，初始加载时在自己的默认位置（relative 定位）。

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/css-sticky/1.png)

页面向下滚动时，工具栏变成固定位置，始终停留在页面头部（fixed定位）。

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/css-sticky/2.png)

等到页面重新向上滚动回到原位，工具栏也会回到默认位置。

`sticky` 生效的前提是，必须搭配 top、bottom、left、right 这四个属性一起使用，不能省略，否则等同于 relative 定位，不产生“动态固定”的效果。原因是这四个属性用来定义“偏移距离”，浏览器把它当作 `sticky` 的生效门槛。

它的具体规则是，当页面滚动，父元素开始脱离视口时（即部分不可见），只要与 `sticky` 元素的距离达到生效门槛，`relative` 定位自动切换为 `fixed` 定位；等到父元素完全脱离视口时（即完全不可见），`fixed` 定位自动切换回 `relative` 定位。

请看下面的示例代码。（注意，除了已被淘汰的 IE 以外，其他浏览器目前都支持 `sticky`。但是，Safari 浏览器需要加上浏览器前缀 `-webkit-`。）
```css
#toolbar { 
  position: -webkit-sticky; /* safari 浏览器 */ 
  position: sticky; /* 其他浏览器 */ 
  top: 20px;
}
```
上面代码中，页面向下滚动时，`#toolbar` 的父元素开始脱离视口，一旦视口的顶部与`#toolbar` 的距离小于 20px（门槛值），`#toolbar` 就自动变为 `fixed` 定位，保持与视口顶部 20px 的距离。页面继续向下滚动，父元素彻底离开视口（即整个父元素完全不可见），`#toolbar` 恢复成 `relative` 定位。

## 2.sticky 的应用

`sticky` 定位可以实现一些很有用的效果。除了上面提到“动态固定”效果，这里再介绍两个。

### 2.1 堆叠效果

堆叠效果（stacking）指的是页面滚动时，下方的元素覆盖上方的元素。下面是一个图片堆叠的例子，下方的图片会随着页面滚动，覆盖上方的图片（查看 [demo](https://jsbin.com/fegiqoquki/edit?html,css,output)）。

![堆叠效果](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/css-sticky/1.gif)

HTML 代码就是几张图片。
```html
<div><img src="pic1.jpg"></div>
<div><img src="pic2.jpg"></div>
<div><img src="pic3.jpg"></div>
```

CSS 代码极其简单，只要两行。
```css
div {
  position: sticky;
  top: 0;
}
```
它的原理是页面向下滚动时，每张图片都会变成fixed定位，导致后一张图片重叠在前一张图片上面。详细解释可以看[这里](https://dev.to/vinceumo/slide-stacking-effect-using-position-sticky-91f)。

### 2.2 表格的表头锁定

大型表格滚动的时候，表头始终固定，也可以用 `sticky` 实现（查看 [demo](https://jsbin.com/decemanohe/edit?html,css,output)）。

![表格的表头锁定](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/css-sticky/2.gif)

CSS 代码也很简单。
```css
th {
  position: sticky;
  top: 0; 
}
```
需要注意的是，`sticky` 必须设在 `<th>` 元素上面，不能设在 `<thead>` 和 `<tr>` 元素，因为这两个元素没有 `relative` 定位，也就无法产生 `sticky` 效果。

全文完。

