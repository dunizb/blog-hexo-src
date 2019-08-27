---
title: CSS常见问题(三)
date: 2015-05-26 18:47:10
categories:
- 技术
tags:
- 前端
- CSS
description: "`display`的值除了`block`和`inline`，还有其他的值，比如`list-item`、`table-cell`等，但因为IE6和IE7浏览器支持的display类型很少，所以为了兼容IE，我们真正能用的display类型只有block、inline和none三种。"
---

## display：inline-block和hasLayout

`display`的值除了`block`和`inline`，还有其他的值，比如`list-item`、`table-cell`等，但因为IE6和IE7浏览器支持的display类型很少，所以为了兼容IE，我们真正能用的display类型只有block、inline和none三种。

对于另一中非常有用的display类型inline-block，其实IE和IE7下也是有办法实现的，但需要注意的是，并不是说IE6和IE7支持display：inline-block，它的实现其实是一种hack——触发行内元素的hasLayout。在前面[《CSS常见问题二：hasLayout》](//www.jianshu.com/p/35ec4d7200f9)我们讲过，hasLayout是IE浏览器为解释盒模型而设计的一个转有属性，它的设计初衷是用于块级元素的，如果触发行内元素的hasLayout，就会让行内元素拥有块级元素的特征。

先说说`display：inline-block`的特性吧，顾名思义，它是行内的块级元素，它拥有块级元素的特点，可以设置长宽，可以设置margin和padding值，但它却不是独占一行，它的宽度并不占满父元素，而是和行内元素一样，可以和其他行内元素排在同一行里。它集块级元素和行内元素的特点于一身，是个非常有用的display类型。

## relative、absolute和float

`position：relative`和`position：absolute`都可以改变元素在文档流中的位置，设置`position：relative`或`position：absolute`都可以让元素激活`left`、`right`、`top`、`bottom`和`z-index`属性（默认情况下，这些熟悉未激活，设置了也无效）。网页虽然看起来是平面的二维结构，但它其实是有Z轴的，Z轴的大小由`z-index`控制，默认情况下，所有元素都是在`z-index：0`这一层。元素根据自己的display类型、长宽、内外边距等属性顺序排列在`z-index：0`这一层里，这就是文档流。设置`position：relative`或`position：absolute`会让元素“浮”起来，也就是`z-index`的值大于0，它会改变正常情况下的文档流。不同的是`position：relative`会保留自己在`z-index：0`层的占位，left、top、right、bottom值是相对于自己在`z-index：0`层的位置，虽然它的实际位置可能因为设置了left、top、right、bottom值而偏离原来的位置，但对于其他仍然在`z-index：0`层的元素位置不会造成影响。而`position：absolute`会完全脱离文档流，不再在`z-index：0`层保留占位符，其left、top、right、bottom值是相对于自己最近的一个设置了`position：relative`和`position：absolute`的祖先元素的，如果祖先元素全都没有这是`position：relative`和`position：absolute`，那么就相对于body元素。

除了`position：relative`和`position：absolute`，float也能改变文档流，不同的是，float属性不会让元素“上浮”到另一个z-index层，它任然让元素在z-index：0层排列，float不像`position：relative`和`position：absolute`那样，它不能通过left、top、right、bottom属性精确地控制元素的坐标，它只能通过`float：left`或`float：right`来控制元素在同层里“左浮”“右浮”。float会改变正常的文档流排列，影响到周围元素。

另一个有趣的现象是`position：absolute`和float会隐式的改变display类型，不论之前什么类型的元素（display：none除外），只要设置了`position：absolute`、`float：left`或`float：right`中人一个，都会让元素以`display：inline-block`的方式显示：可以设置长宽，默认宽度并不占满父元素。就算我们显示的设置`display：inline`或者`display：block`，也任然是无效的（float在IE6下的双边距BUG就是利用添加`display：inline`来解决的）。值得注意的是，`position：relative`却不会隐式的改变display的类型。

跟多关于absolute、float、overflow的知识可以参考慕课网视频：[//www.imooc.com/space/teacher/id/197450]()