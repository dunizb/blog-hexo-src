---
title: 微信小程序WXS之谜
date: 2019-09-14 21:56:24
categories:
- 技术
tags:
- 前端
- 微信小程序
---
微信创造了 WXS ，除了提高性能，还有什么原因？
<!-- more -->

## 微信为何要创造 WXS
WXS（WeiXin Script）是微信创造的一套脚本语言，它的官方说法是：“WXS 与 JavaScript 是不同的语言，有自己的语法，并不和 JavaScript 一致”。

那微信为何要脱离 JavaScript ，单独创造一套语言呢？这要从微信小程序的底层逻辑（运行环境）讲起。

小程序的运行环境分为逻辑层和视图层，分别由 2 个线程管理，其中：
- `WXML` 模板和 `WXSS` 样式工作在视图层，界面使用 `WebView` 进行渲染。
- JavaScript 代码工作在逻辑层，运行在 JsCore 或 v8 里。

小程序在视图层与逻辑层两个线程间提供了数据传输和事件系统。这样的分离设计，带来了**显而易见的好处**：逻辑和视图分离，即使业务逻辑计算非常繁忙，也不会阻塞渲染和用户在视图层上的交互。

但同时也带来了**明显的坏处**：视图层（webview）中不能运行 JS，而逻辑层 JS 又无法直接修改页面 DOM，数据更新及事件系统只能靠线程间通讯，但跨线程通信的成本极高，特别是需要频繁通信的场景。

什么是需要频繁通讯的场景？最典型的例子就是用户持续交互的情况，比如触摸、滚动等。我们以侧滑菜单为例，假设在页面上滑动 A 元素，要求 B 元素跟随移动，一次滑动操作（touchmove）的响应过程如下：
1. touchmove 事件从视图层（Webview）传递到逻辑层，中间会由微信客户端（Native）做中转。
    ![](https://b2.bmp.ovh/imgs/2019/09/caab357682b911a0.png)
2. 逻辑层处理 `touchmove` 事件，计算需移动的位置，然后再通过 `setData` 传递到视图层，中间同样会由微信客户端（Native）做中转。
    ![](https://b2.bmp.ovh/imgs/2019/09/e2725502fac494a1.png)

一次 `touchmove` 的响应需要经过 视图层、Native、逻辑层三者之间 2 个完整来回的通信，通信的耗时开销较大，用户的交互就会出现延时卡顿的情况。

除了滚动、拖动交互外，在 for 循环里对数据做格式修改，也会造成逻辑层和视图层频繁通讯。

其实这类通信损耗问题，在业内由来已久，react native 和 weex 都有类似问题，weex 提供了 `bindingx` 来解决。

但对于小程序来讲，这类问题解决起来更容易。其实视图层的 webview，是有 js 环境的，只不过过去不给开发者开放。

如果在视图层的 js 直接处理滚动或拖动交互、直接处理数据格式，就能避免大量通信损耗。

但对于小程序平台而言，大量开放 webview 里的 js 编写，违反了它的初衷，比如开发者会直接操作 dom，影响性能体验。所以小程序平台提出一种新规范，限制 webview 里可运行的 js 的能力。这就是 wxs的由来。

从本质来讲，wxs 是一种被限制过的、运行在视图层 webview 里的 js。它并不是真的发明了一种新语言。

## WXS 特征及适用场景
WXS 具备如下特征：
- WXS 是可以在视图层（webview）中运行的 JS。
- WXS 无法修改业务数据，仅能设置当前组件的class和style。
- WXS 是被限制过的 JavaScript，可以进行一些简单的逻辑运算。
- WXS 可以监听 touch 事件，处理滚动、拖动交互。

故可以得出 WXS 的适用场景，主要包括：
- 用户交互频繁、仅需改动组件样式（比如布局位置），无需改动数据内容的场景，比如侧滑菜单、索引列表、滚动渐变等。
- 纯粹的逻辑计算，比如文本、日期格式化，通过 WXS 可以模拟实现 Vue 框架的过滤器, 如下是一个通过 wxs 便捷实现首字母大写的示例： 
```html
<wxs module="m1">
  // 首字母大写
  var capitalize = function(value) {
    if (!value) return ''
    value = value.toString()
    return value.charAt(0).toUpperCase() + value.slice(1)
  }
  module.exports = {
    capitalize: capitalize
  }
</wxs>
<view class="content">
  <view class="text-area">
    <!-- title 为当前页面 data 中定义的初始数据 -->
    <text class="title">{{m1.capitalize(title)}}</text>
  </view>
</view>
```

******
本文摘自【前端之巅】微信公众号：[谜之WXS，uni-app如何用它大幅提升性能](https://mp.weixin.qq.com/s?__biz=MzUxMzcxMzE5Ng==&mid=2247492501&idx=2&sn=585a50ad1ec2ba083bc370b0767bdd64&chksm=f95256d6ce25dfc092a4c4d1b273c83de7749fa2bc363d73be6acba974e064009eb64dd743a8&mpshare=1&scene=1&srcid=&sharer_sharetime=1568375136026&sharer_shareid=a1cefbdd3e6712df8aaf4240a0d50b87#rd)