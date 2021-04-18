---
title: 零碎笔记：移动Web特别样式处理
date: 2016-10-10 20:49:00
categories:
  - 学习笔记
tags:
  - 移动Web
summary: "高清图片跟我们平时下的那种电影高清图片是不一样的，移动Web的高清图片的概念是我这张图这么大，清晰度这么多，那么我们在移动设备上就该展示这么清晰。"
---

## 高清图片问题

高清图片跟我们平时下的那种电影高清图片是不一样的，移动 Web 的高清图片的概念是我这张图这么大，清晰度这么多，那么我们在移动设备上就该展示这么清晰。那么为什么会产生模糊呢？假如一张图片 `100px * 100px` 那我们在移动设备上就以 `100px * 100px` 去展示，这想想也是没有问题的。

<!-- more -->

但我们想想，在 Retina 屏幕，一个 px 等于两个 dp，那你拿 `100px*100px` 实际上是拿了 `200dp * 200dp` 物理像素去渲染，图片就会被拉大被模糊。

在移动 Web 页面上渲染图片，为了避免图片产生模糊，图片的宽高应该用物理像素单位渲染，即是 `100 * 100` 的图片，应该使用 `100dp * 100dp。`

```css
width: (w_value/dpr) px;
height: (h_value/dpr) px;
```

那说白了，在高清屏幕下，假如 100*100 的图片，那么我们就应该使用 50*50 的 px 去渲染，那如果在 iPhone6 Plus 下这时候 dpr 大于 2 的话，那我们就应该除以它的 dpr(3)这样的方式。

## 一像素边框

一像素的边框的问题其实最根本的问题还是 Retina 屏幕的问题，那我们设置了 border:1px，那 1px 就等于 2 个 dp，那它实际上这个 1 像素边框拿了 2 个 dp 来渲染，所以那条线就不是很细，如果我们拿 1dp 来渲染的话就会更加精致更加细腻。

那怎么解决呢？

如果把 border:0.5px 可以不可以呢？答案是不可以的，因为它仅仅是 ios8 可以用。

实际上更通用的方案是使用 sacleY(0.5)来进行缩放处理：

```css
.sidbar .folder li{
    padding:8px 0 8px 15px;
    color:#7c7c7c;
    cursor:pointer;
    position:relative;
}
.folder li + li:before{
    position:absolute;
    top:-1px;
    left:0;
    content:'',
    width:100%;
    height:1px;
    border-top:1px solid #ddd;
    -webkit-transform:scaleY(0.5);
}
```

当然，网上还有其他方案可以选择。

## 相对单位 rem

为了适应各大屏幕的手机，px 略显固定，不能根据尺寸的大小而改变，使用相对单位更能体验页面的兼容性。相对单位有两个：

- em：是根据父节点的 font-size 为相对单位
- rem：是根据 html 的 font-size 为相对单位

em 在多层嵌套下，变得非常难以使用和维护了，rem 更加能作为全局同意设置的度量，我们推荐使用 rem。

rem 的基值设置多少好？回归我们的目的：为了适应各大手机屏幕，我们可以这样： rem = screen.width / 20 ，或者除以 10 等。

```css
// 默认320px
html {
  font-size: 32px;
}
// iphone6
@media (min-device-wdith: 375px) {
  html {
    font-size: 37.5px;
  }
}
// ihpone6 plus
@media (min-device-width: 414px) {
  html {
    font-size: 41.4px;
  }
}
```

如 iphone5 上的 rem 基值为 32px，渲染一张 64*64px 的 div，则用 2rem*2rem 去渲染。换算公式非常简单：

```css
width: px/rem基值
height: px/rem基值
```

## 不使用 rem 的情况：font-size

一般来讲 font-size 是并不应该使用 rem 等相对单位的。因为字体的大小是趋于阅读的实用性，并不适合与排版布局。

同理，趋向于一些固定的元素的特性。我们不使用 rem 而改为使用 px 去确保在不同的屏幕上表现一致（跟 rem 的目的相反）。

## 多行文本溢出…

![图片描述][2]
单行文本溢出，对 title 类的使用非常多，而多行文本类，在详情介绍则用的比较多。

```css
/*单行文本溢出*/
.inaline {
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}
/*多行文本溢出*/
.intwoline {
  display: -webkit-box !important;
  overflow: hidden;
  text-overflow: ellipsis;
  word-break: break-all;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 4;
}
```

属性-webkit-line-clamp 就可以控制几行溢出，4 就是第四行进行截断加“…”

---

**参考资料：**
[Hello，移动 Web](//www.imooc.com/learn/494)

[1]: /img/bVD3ef
[2]: http://img.mukewang.com/57fa57ac000171eb02870090.jpg
