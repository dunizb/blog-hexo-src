---
title: 小技巧|CSS如何实现文字两端对齐
date: 2017-09-17 17:14:44
categories:
- 前端开发
tags:
- HTML&CSS
- 小技巧
description: "小技巧|CSS如何实现文字两端对齐,text-align:justify的使用"
---

![小技巧|CSS文字两端对齐效果实现](http://dunizb.b0.upaiyun.com/article/201709/text-align-justify/banner.png)

需求如下，红框所在的文字有四个字的、三个字的、两个字的，如果不两端对齐可以选择居中对齐，或者右对齐。但是如果要像下面这样两端对齐呢？ 
![](http://dunizb.b0.upaiyun.com/article/201709/text-align-justify/1.png)

我相信以前很多人都这么干过：两个字中间使用&nbsp;来隔开达到四个字的宽度，三个字也可以，但是，像上图中“122账号”“122密码”这样的，就不好计算该用几个空格了。

假如我们有如下HTML：
```html
<div>这世间唯有梦想和好姑娘不可辜负！</div>
```
给它加点样式
```css
div{
  width:500px;
  border:1px solid red;
  text-align: justify;
}
```

初始效果是这样的 
![](http://dunizb.b0.upaiyun.com/article/201709/text-align-justify/2.png)

`text-align: justify`这是什么东西？CSS2中`text-align`有一个属性值为`justify`，为对齐之意。其实现的效果就是可以让一行文字两端对齐显示（文字内容要超过一行）。

但是光使用它依然没什么卵用…..

要使文字两端对齐，我们还得使用一个行内空标签来助阵，比如`<span>`、`<i>`等等，这里是我用`<i>`标签
```html
<div>这世间唯有梦想和好姑娘不可辜负！<i></i></div>
```
给这个`i`标签设置如下样式
```css
div i{
  display:inline-block;
  /*padding-left: 100%;*/
  width:100%;
}
```
`padding-left: 100%`和`width:100%`都可以达到效果，选用其一即可。效果如下 
![](http://dunizb.b0.upaiyun.com/article/201709/text-align-justify/3.png)

但是加入HTML元素又违反了结构表现分离的原则，我们可以改用after、before伪元素：
```css
div:after {
    content: " ";
    display: inline-block;
    width: 100%;
}
```

感谢 [@依韵_宵音](https://segmentfault.com/u/cdswyda) 的提醒



