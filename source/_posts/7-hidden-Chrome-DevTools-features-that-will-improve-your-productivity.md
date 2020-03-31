---
title: 7个能提高你生产力的隐藏Chrome DevTools功能
date: 2020-03-31 22:01:21
categories:
  - 工具
tags:
  - Chrome
---

开发人员工具对于软件开发是必不可少的。我们需要它们来开发、测试和调试我们的工作。作为web应用程序开发人员，您使用Chrome DevTools的几率非常高。

本文将向您展示Chrome DevTools的一些隐藏功能，以帮助您提高生产力，有些你可以能已经知道，有些你可能还不知道。

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/202003/chrome-devtools-7-tips/inspect.png)

<!-- more -->

## 在低端设备上测试Web应用性能

一般而言，我们开发人员都是在具有高速网络连接的高端设备上工作。但不幸的是，我们的用户并不能一直使用高端设备和高速互联网连接。

随着移动设备的兴起，我们都应该更加意识到这种情况。并非每个人都拥有超贵的手机或始终可以访问4G。

您知道如何轻松模拟低端设备和低速网络连接吗？

你可以很容易地在Chrome DevTools中控制CPU的能力和网络速度。这样，您可以测试您的Web应用程序性能并根据其进行优化。你可以这样做：
1. 打开 Chrome DevTools，按下 `CMD/CTRL + SHIFT + P` 打开命令菜单。
2. 输入 `Show Performance`，然后按回车键打开性能面板。

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/202003/chrome-devtools-7-tips/2.png)

按右边的齿轮图标打开捕获设置。现在，您可以为网络和CPU选择不同的限制选项。

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/202003/chrome-devtools-7-tips/3.png)

还有一个更简单的选项来模拟预定义的设备配置文件。

按下 `CMD/CTRL + SHIFT + M` 切换设备的工具栏，你可以在中档手机和低端手机之间进行选择，这些选项将相对地设置网络和CPU节流。

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/202003/chrome-devtools-7-tips/4.png)

## 捕获不同设备大小的屏幕截图

您已经创建了外观漂亮的网络应用，并希望捕获屏幕截图。幸运的是，Chrome DevTools支持，你可以很容易地为你的web应用捕捉一个正常的、全尺寸的或区域的屏幕截图。

打开 Chrome DevTools，按下 `CMD/CTRL + SHIFT + P` 打开命令菜单，输入 `screenshot` 查看所有截图捕捉选项，选择其中之一来捕获屏幕截图。

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/202003/chrome-devtools-7-tips/5.png)

还有一种更简单的方法来捕获普通和全尺寸的屏幕截图。

按下 `CMD/CTRL + SHIFT + M` 切换设备的工具栏，按设备工具栏右侧的三个点菜单，在这里，您可以在捕获屏幕截图和捕获全尺寸屏幕截图之间进行选择。

这些选项将捕获所选模拟设备视图的屏幕截图。

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/202003/chrome-devtools-7-tips/6.png)

## 更改用户代理

作为Web应用程序开发人员，您需要编写可在多个平台上运行的应用程序。似乎还不够，您还需要考虑不同平台上的不同浏览器。

您可能需要对特定浏览器的样式表进行有条件的更改，多亏了Chrome DevTools，你可以很容易地动态改变用户代理并测试所有这些。你可以这样做：
1. 打开Chrome DevTools，按下 `CMD/CTRL + SHIFT + P` 打开命令菜单。
2. 输入 `Show Network conditions` 按回车键打开网络条件面板，取消选中 `User agent` 选项右边的 `Select automatically` 复选框。

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/202003/chrome-devtools-7-tips/7.png)

现在，您可以从预定义的用户代理列表中进行选择。

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/202003/chrome-devtools-7-tips/8.png)


## 测试你的明亮(Light)和暗黑(Dark)主题

`preferreds-color-scheme` CSS属性可帮助您检测用户是否已请求系统使用浅色或深色主题。使用此属性，您可以轻松在暗色和亮色主题之间切换，而无需任何用户交互。

要测试此行为，您无需更改系统设置，Chrome DevTools可帮助您轻松实现。你可以这样做：
1. 打开 Chrome DevTools，按下 `CMD/CTRL + SHIFT + P` 打开命令行菜单
2. 输入 `Show Rendering` 后回车打开渲染面板。

您可以在其中使用 `Emulate CSS media feature prefers-color-scheme` 选项在 `preferred-color-scheme:light` 和 `preferred-color-scheme:dark` 之间进行选择。

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/202003/chrome-devtools-7-tips/9.png)

## 为视力障碍者测试您的Web应用程序

作为web应用程序开发人员，我们有责任确保我们的程序是可访问的。如果我们没有任视力障碍，就很难理解它是什么样子并根据它来测试我们的程序。幸运的是，Chrome DevTools也提供了解决方案。

使用视觉缺陷模拟，您可以测试您的web应用程序的人谁有视力模糊，原色盲，后色盲，三色盲，色盲。

打开 Chrome DevTools，按下 `CMD/CTRL + SHIFT + P` 打开命令行菜单，输入 `Show Rendering` 后回车打开渲染面板。

您可以使用 `Emulate vision deficiencies` 选项在 `Blurredvision`，`Protanopia`，`Deuteranopia`，`Tritanopia` 和 `Achromatopsia` 之间进行选择。

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/202003/chrome-devtools-7-tips/10.png)

## 按媒体资源过滤网络请求

您是否知道可以使用属性按照许多不同的条件过滤网络请求？

Chrome DevTools提供了很多选项来过滤网络请求。例如，您可以使用 `large-than:1k` 属性过滤大小大于 1kb 的请求。

打开 Chrome DevTools，按下 `CMD/CTRL + SHIFT + P` 打开命令行，输入 `Show Network` 后回车打开网络面板。将 `larger-than:1k` 写入过滤器输入，然后按Enter。

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/202003/chrome-devtools-7-tips/11.png)

## 在控制台中获取DOM节点引用

您是否曾经想过在控制台中获取DOM节点引用以进行一些测试？您可以使用JavaScript来做到这一点。您可以使用诸如 `document.getElementById` 之类的方法，并将节点分配给变量。

但是某些节点可能没有ID甚至没有Class。与其与选择器纠缠，不如想出一种更简单的方法。在这种情况下，您可以利用Chrome DevTools。

Chrome DevTools具有一项称为 `Store as global variable` 的功能。您可以轻松地在控制台中获取任何节点,你可以这样使用它：
1. 右键单击要在屏幕上获得的任何节点，在菜单中选择 `检查` 以打开Chrome DevTools并选择元素。
2. 右键单击元素面板中的节点，选择 `Store as global variable` ，之后，它将在控制台中的全局变量中可用。

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/202003/chrome-devtools-7-tips/12.png)

## 总结

Chrome DevTools功能强大。您可能还不知道很多其他功能。请查看以下资源部分以了解更多信息。

资源：
- https://developers.google.com/web/tools/chrome-devtools
- https://twitter.com/ChromeDevTools
- https://www.youtube.com/channel/UCnUYZLuoy1rq1aVMwx4aTzw
