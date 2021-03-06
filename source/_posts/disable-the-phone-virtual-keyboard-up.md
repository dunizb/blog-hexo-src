---
title: "小技巧|H5禁止手机虚拟键盘弹出"
date: 2017-09-24 23:12:28
categories:
  - Web技术
tags:
  - 移动Web
  - 小技巧
description: "H5禁止手机虚拟键盘弹出的两种方式详解"
---

工作中遇到如下需求，点击输入框弹出自定义弹窗，输入框是 input 标：

<!-- more -->

![](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201709/disable-virtual-keyboard-up/1.gif)
但是在移动端，input 会默认触发手机的虚拟键盘，如何阻止手机虚拟键盘弹起呢？目前我试过有两个方案，一个是给 input 添加`readonly`属性,另一个就是在 input 事件处理方法前面添加一句`document.activeElement.blur()` 。

## readonly

使用 readonly 方式来阻止虚拟键盘弹出应该是最简单最优雅的方式了。readonly 属性规定输入字段为只读。只读字段是不能修改的。不过，用户仍然可以使用 tab 键切换到该字段，还可以选中或拷贝其文本。

值得一提的是它的取值，只要声明了 readonly 属性，不管取什么值都可以，比如`readonly=""`、`readonly="readonly"`、`readonly="abc"`都是一样的

**优点：简单**
**缺点：在 iOS 的 Safari 中无效（未做更多情况测试）**

## document.activeElement.blur()

这是个什么玩意儿？`document.activeElement`是一个 Web API 接口。[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/activeElement)上的解释是：它返回当前页面中获得焦点的元素，也就是说，如果此时用户按下了键盘上某个键，会在该元素上触发键盘事件，该属性是只读的。

`document.activeElement`属性始终会引用 DOM 中当前获得了焦点的元素。元素获得焦点的方式有用户输入(通常是按 Tab 键)、在代码中调用 focus()方法和页面加载。

它里面有很多方法，在浏览器控制台查看，可以看到有很都方法：
![](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201709/disable-virtual-keyboard-up/2.gif)
MDN 上还展示了一个有意思的示例，[看这里](http://jsfiddle.net/w9gFj/)

那么`document.activeElement.blur()`为什么可以阻止虚拟键盘弹出呢？原因是：当你点击 input 的时候，`document.activeElement`获得了 DOM 中被聚焦的元素，也就是你点击的 input，而调用`.blur()`方法，`blur`我相信大家都知道吧，就是取消聚焦。获得被聚焦的元素然后强制 blur 以达到没有聚焦的样子、、、感觉绕了。

**优点：支持 Android、iOS**
**缺点：需要添加额外的 JS 代码**

这句代码加在什么地方？加入有如下 HTML

```html
<div class="calendar">
  <div>
    <input
      type="text"
      id="datePicker"
      class="date_picker"
      placeholder="点击选择入住日期"
    />
  </div>
</div>
```

那么这句 JS 加在事件处理方法中

```js
$("#datePicker").focus(function() {
  document.activeElement.blur();
});
```

## 总结

就当前需求来说，用`document.activeElement.blur()`确实是在绕弯子，直接使用`readonly`是最佳方案。但是`document.activeElement`很强大，可以做很多事。
