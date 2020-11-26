---
title: 【小技巧】配置好用的Chrome DevTools，让前端开发调试更友好
date: 2019-11-21 21:12:52
img: https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/blogCover/1.jpg
categories:
  - 工具
tags:
  - Chrome
  - 小技巧
---

总结和发现的一些好用的 Chrome 开发者工具配置

<!-- more -->

## 显示网络请求的 Method 和 status

![enabel-method.gif](https://i.loli.net/2019/11/23/lD5vhzJMktofwYd.gif)

在 Firefox 中，status 显示有颜色区分，且状态、方法和地址的顺序阅读更加友好，喜欢 Firefox 的朋友可以试试
![Firefox-request.png](https://i.loli.net/2019/11/23/1LFjqI5acrJDf6Q.png)

## 请求行使用大行模式

该模式会在 Name 中显示源地址
![use-lager-request-row.png](https://i.loli.net/2019/11/23/wlT6UJWRoFCDAYP.png)

## 显示 CSS 布局层级信息

![CSS-layers.png](https://i.loli.net/2019/11/23/Oe1Egtv6cBMXUQD.png)

然后我们在 Layers 标签中看到页面布局信息，还可以 360° 旋转，这个在 Firefox 中表现更好
![CSS-layers-view.png](https://i.loli.net/2019/11/23/DUlRXZsOn7LGeqr.png)

## 开启 Source 面板中代码折叠

我们在查看页面源码的时候，js 代码默认是不可以折叠的，开启这个开关即可以折叠代码了，`Settings -> Preferences -> Sources -> 勾选Code Folding`

![Sources-code-folding.png](https://i.loli.net/2019/11/23/xYvK6lmAwDq8Up1.png)

效果
![Sources-code-folding-view.png](https://i.loli.net/2019/11/23/d3va5q68ys7Do1L.png)

## 开启页面元素查看时显示标尺

设置 `Settings -> Preferences -> Elements -> 勾选Show rules`
![show-rules.png](https://i.loli.net/2019/11/23/9EsnVptaF1BGRYm.png)

效果
![show-rules-view.png](https://i.loli.net/2019/11/23/F7IQgfxKLwOJTCq.png)

## 模拟网速和禁止缓存

![disable-cache-online.png](https://i.loli.net/2019/11/23/ExQouRfMZTUSmPL.png)

全文完。
