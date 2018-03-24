---
title: jQuery的三组基本动画与自定义动画总结
date: 2016-07-04 22:47:00
categories:
- 前端开发
tags:
- jQuery
---

jQuery提供了三组基本动画，分别是显示与隐藏、淡入与淡出、滑入与画出，这三组基本动画都是标准的、有规律的的效果，jQuery还提供了一个自定义动画。
<!-- more -->
1、显示（show）与隐藏（hide）
=============================
显示(show)与隐藏(hide)是一组动画

### 1.1 show方法
show([speed,[easing],[callback]])
+ 参数speed，可选，动画的执行时间
   - 如果不传，就没有动画效果。
   - 毫秒值(比如1000),动画在1000毫秒执行完成**(推荐)**
   - 固定字符串，slow(200)、normal(400)、fast(600)，如果传其他字符串，则默认为normal。
+ 参数callback，可选，执行完动画后执行的回调函数，每个元素执行一次。
+ 参数easing，可选，这里先不讲，后面统一讲

### 1.2 hide方法

与show方法的用法完全一致。

### 1.3 原理

show和hide修改的是元素的width、height、opacity。

2、滑入（sliderDown）与隐藏（sliderUp）
=============================
滑入(slideUp)与滑出(slideDown)是一组动画，效果与卷帘门类似

slideUp/slideDown,使用方法与show/hide基本一致。

### 2.1 用法
slideDown([speed],[easing],[callback])
+ 参数speed，可选，动画的执行时间
   - 如果不传，**默认为normal，注意区分show/hide**
   - 毫秒值(比如1000),动画在1000毫秒执行完成**(推荐)**
   - 固定字符串，slow(200)、normal(400)、fast(600)，如果传其他字符串，则默认为normal。
+ 参数callback，可选，执行完动画后执行的回调函数，每个元素执行一次。
+ 参数easing，可选，这里先不讲，后面统一讲

### 2.2 滑入画出切换（slideToggle）
$(selector).slideToggle(speed,callback);
如果是隐藏状态，那么执行slideDown操作，如果是显示状态，那么执行slideUp操作。

### 2.3 原理

slideDown和slideUp修改的是元素的height，通过高度变化（向下、向上增大）来动态地显示所有匹配的元素。

3、淡入（fadeIn）与淡出（fadeOut）
=============================
fadeIn/fadeOut使用方法与show/hide、slideDown/slideUp一致。

### 3.1  用法
fadeIn([speed],[easing],[callback])
+ 参数speed，可选，动画的执行时间
   - 如果不传，**默认为normal**
   - 毫秒值(比如1000),动画在1000毫秒执行完成**(推荐)**
   - 固定字符串，slow(200)、normal(400)、fast(600)，如果传其他字符串，则默认为normal。
+ 参数callback，可选，执行完动画后执行的回调函数，每个元素执行一次。
+ 参数easing，可选，这里先不讲，后面统一讲

### 3.2  淡入淡出切换（fadeToggle）
fadeToggle([speed,[easing],[callback]])
如果当前元素处于隐藏状态，那么执行fadeIn操作，如果处于显示状态，那么执行fadeOut操作。

### 3.3 淡入淡出到某个值（fadeTo）
fadeTo(speed,opacity,[easing],[callback]])
把所有匹配元素的不透明度以渐进方式调整到指定的不透明度
+ 参数speed，必须
+ 参数opacity，0-1之间的数值(比如0.4)，表示淡到某一个值。
+ 参数callback，可选，执行完动画后执行的回调函数，每个元素执行一次。

**与淡入淡出的区别：**淡入淡出只能控制元素的不透明度从 完全不透明 到完全透明；而fadeTo可以指定元素不透明度的具体值。并且时间参数是必需的！

fade系列方法：修改的是元素的opacity。

4、三组基本动画总结
=============================

a. Query给我们提供了三组动画，show/hide、slideUp/slideDown、fadeIn/fadeOut。
b. 动画切换方法：slideToggle、fadeToggle，注意：show和hide没有切换的方法。
c. 淡入淡出到某个值：fadeTo方法。
d. show/slideDown/fadeIn三个是显示效果、hide/slideUp/fadeOut三个是隐藏效果。
e. show/hide修改的是元素的height,width,opacity。slide系列方法修改的是元素的height。fade系列方法修改的是元素的opacity。这三种方法修改的这些值，都是带数字的，**因为带了数字才能做渐变**。

5、自定义动画（animate）
=============================
animate(params,[speed],[easing],[callback])
+ 参数params，必须，要执行动画的CSS属性，带数字
+ 参数speed，可选，执行动画时长
+ 参数easing，可选，这里先不讲，后面统一讲
+ 参数callback，可选，执行完动画后执行的回调函数，每个元素执行一次。

6、easing参数
=============================
现在来说说easing参数的作用，这个参数是控制动画的速度样式，这个参数只有两个取值：
+ swing：摆钟运动，在开头和结尾移动慢，在中间移动速度快。
+ linear：匀速移动。

在不指定easing参数时，jQuery动画默认值是swing。

7、动画队列
=============================

在同一个元素上执行多个动画，那么对于这个动画来说，后面的动画会被放到动画队列中，等前面的动画执行完成了才会执行（联想：地铁进站）。

8、停止动画
=============================
要停止动画，可以使用stop()方法。stop(clearQueue, jumpToEnd)。
### 8.1、stop()
stop方法接受两个参数，这个两个参数都是可选的，为Boolean值：
+ clearQueue，是否清除动画队列；
+ jumpToEnd，是否跳转到动画的最终效果。

当然了，一般我们不需要传递参数，直接使用stop()。如果直接使用stop()方法，则会理解停止当前正在执行的动画，如果接下来还有动画等待进行，则以当前状态开始接下来的动画。

### 8.2、判断元素是否处于动画状态
**动画积累**：在使用animate()方法的时候，要避免动画积累而导致的动画与用户的行为不一致。当用户快速在某个元素上执行animate动画时，就会出现动画积累。

解决方法是判断元素是否处于动画状态，如果元素不处于动画状态，才为元素添加新的动画，否则不添加。
```js
if( ! $(element).is(":animate") ){    //判断元素是否正处于动画状态
    //如果当前没有进行动画，则添加新的动画
}
```