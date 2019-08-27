---
title: CSS行高(line-height)及文本垂直居中原理
date: 2016-05-14 12:02:00
categories:
- 前端
tags:
- CSS
description: "在CSS中，line-height 属性设置两段段文本之间的距离，也就是行高，如果我们把一段文本的line-height设置为父容器的高度就可以实现文本垂直居中了。那么，它怎么就垂直居中了？为了弄清楚它，下面我们先来看几个概念。"
---

在CSS中，line-height 属性设置两段段文本之间的距离，也就是行高，如果我们把一段文本的line-height设置为父容器的高度就可以实现文本垂直居中了，比如下面的例子：
```html
<!DOCTYPE html>
    <html lang="en">
    <head>
       <meta charset="UTF-8">
       <title>Document</title>
       <style>
       div {
           width: 300px;
           height: 200px;
           border: 1px solid red;
       }
       span {
           line-height: 200px;
       }
      </style>
</head>
<body>
   <div>
       <span>文本垂直居中原理</span>
   </div>
</body>
</html>
```

这样，span标签中的文字就相对于div垂直方向居中了，想要文本水平居中设置text-align：center即可。
![1.png](//ww1.sinaimg.cn/large/006tNc79ly1g5d8an2j2uj308x0620ia.jpg)
那么，它怎么就垂直居中了？为了弄清楚它，下面我们先来看几个概念。

1. 行框
-------------------------
在浏览器中，会将给每一段文本生成一个**行框**，行框的高度就是行高。行框由上间距、文本高度、下间距组成，上间距的距离与下间距的距离是相等的。
![2.png](//ww3.sinaimg.cn/large/006tNc79ly1g5d8aoc6ikj30ey08wa9v.jpg)
默认情况下一行文本的行高分为：上间距，文本的高度，下间距，并且上间距是等于下间距的，所以文字默认在这一行中是垂直居中的。

2. 文本中的几条线
--------------------------------
![3.png](//ww1.sinaimg.cn/large/006tNc79ly1g5d8aqu3yij30hv09bq3o.jpg)
几条线与行高的关系图解：
![4.png](//ww1.sinaimg.cn/large/006tNc79ly1g5d8bn6ay8j30ni08cq3y.jpg)
文本的行高也可以看成是基线到基线的距离。
![5.png](//ww4.sinaimg.cn/large/006tNc79ly1g5d8c5w2d7j30kw0by75m.jpg)
如果一段文本的高度为16px，如果给他设置line-height的高度为200，那么相当于，文本的上下间距的高度增加了，但是文本本身的高度依然是16是不变的，并且一直默认在行框中垂直居中，而上间距和下间距平分了200px的高度并且减去文本本身的高度。所以，容器被这一行文本占满，而本身文字在自己的一行中是垂直居中的，所以看起来就像是在容器中垂直居中。

3. Chrome浏览器的默认值
--------------------------------------------------------
谷歌浏览器字体的默认大小是：16px，字体的最小值为：12px，默认行高为：18px；默认情况下如果没有给div设置高度，那么这个div的高度会比其中文本的大小大一点（这个大多少现在没有办法确定）

4. 行高的单位
----------------------------------
**px(像素)**
设置起来是最直接的，同时也最方便的。

**%(百分号)**
如果line-height单位设置为%，那么将来在计算的时候，基数是当前标签中的文本的字体的大小。
如果是%,%之前的数据一定是整数 ：150% ，200%

**em**
效果跟%是一样一样的。
注意：一行em的大小相当于是当前标签中的font-size的大小。
如果是em,em之前的数据一定是：1.2em ,1.5em ,2em

**不带单位**
如果不涉及到继承，那么带不带单位（em）都是一样的效果，但是如果涉及到继承的话，那么就有很大的区别了：
+ 如果单位是em，那么将来在继承的时候，我们的浏览器会先将行高对应的具体的数值计算出来以后再继承。
+ 如果没有单位，那么将来在继承的时候，我们的浏览器会先将line-height这个属性继承给子元素，再在子元素的font-size来计算。line-height: 1.5。

5.行高可以被继承
----------------------------------
我们知道，CSS的三大特性是继承、层叠、优先级。line-height也是可以被继承的，如下面的示例：
```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
	<style>
	.father {
    	/*line-height: 20px;*/
	}
	</style>
</head>
<body>
    <div class="father">
    	  <div class="children">丰趣海淘</div>
    </div>
</body>
</html>
```

在不给.father设置行高的情况下，.children的文字行高默认为18
![.children的文字行高默认为18](//ww4.sinaimg.cn/large/006tNc79ly1g5d8c6nvngj30h306w3yy.jpg)
接着我们给div设置一个行高等于20px
```css
.father {
    line-height: 20px;
}
```

我们再来看看.children标签的的变化，.children标签的文字行高变成20px了
![.children标签的文字行高变成20px了](//ww3.sinaimg.cn/large/006tNc79ly1g5d8c7n5c3j30gs06maaj.jpg)
而且，不管我们给行高设置什么单位（px、%、em、不带单位）都可以被继承。

6. 行高计算的基数
-----------------------------------
如果行高的单位不是px，那么将来行高要进行计算：这个计算需要一个基数，这个基数是当前标签的字体大小，而不是浏览器默认字体大小。以上面的例子为例，我们并没有设置任何字体大小，此时我们把line-height设置为150%，那么文字的行高将变为24px（16px*1.5=24）。
```css
div {
    line-height: 150%;
}
```

效果如下  
![8.png](//ww4.sinaimg.cn/large/006tNc79ly1g5d8c8lix7j30av064mxi.jpg)
此时我们在给div设置一个font-size等于20px：
```css
div {
   line-height: 150%;
   font-size:20px;
}
```

那么文字行高将会变成30px，20px*1.5=30px;
![9.png](//ww1.sinaimg.cn/large/006tNc79ly1g5d8c9kyr0j30cv06fgm1.jpg)