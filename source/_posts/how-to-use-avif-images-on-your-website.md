---
title: 什么是AVIF？如何在你的网站上使用AV1图像格式的图像
date: 2020-11-22 18:17:52
categories:
  - 技术
---

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/use-avif-images/banner.png)

AV1 图像格式或 AVIF 是地球上最新的图像编解码器。AVIF 是一种优化的图像格式，旨在使我们的图像更小，同时保持相同的质量（无损），AVIF 的文件扩展名是 `.avif`。

在本文中，我想谈谈它的功能和好处，以及为什么你应该开始使用 AVIF。我还将向你展示在你的网站上包含 AVIF 图像的安全方法。

<!-- more -->

## 什么是 AVIF，它如何工作？

AVIF 是从开放媒体联盟（AOM）开发的如今流行的视频格式 AV1 的关键帧中提取的。

![http://aomedia.org/](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/use-avif-images/1.png)

AOM 开发 AVIF 的目的是提供免版税的图像，与现有的图像格式相比，具有更好的压缩效率和更多的功能支持。

AVIF 现在有来自 Google，Netflix 和 Apple 等大公司的支持者。

## 为什么 AVIF 更好？

在它的前辈(**WebP**、**JPEG-XR**、**JPEG2000** 和**PNG**、**GIF**)之后，AVIF 兼容高动态范围成像。它支持全分辨率的 10 位和 12 位颜色，所生成的图像比其他已知格式小 10 倍。

AVIF 对于 Web 开发人员是一个不错的选择，因为：

- 它是免版税的，所以你可以免费使用，不用担心授权问题。
- 目前，它得到了许多大型技术公司的支持，例如 Google，Amazon，Netflix，Microsoft 等。
- 它具有最佳压缩率。
- 它具有更多的现代功能，如透明度，HDR，宽色域等等。

## 如何开始使用 AVIF 图像

现在，我们进入本教程的有趣部分。开始使用 AVIF 图像的主要方法有两种：

1. 一种是将旧图像转换为 AVIF。
2. 另一种方法是使用支持 AVIF 的图像编辑器创建 AVIF 图像。

### 如何将旧图像转换为 AVIF

由于 AVIF 仍处于起步阶段，因此以 AVIF 格式创建图像的最简单方法是转换旧格式。

这可以简单地在线完成，因为有许多在线 AVIF 图像转换器。[AVIF online converter](https://avif-converter.online/)是我的选择，因为它更简单，并且似乎是可用最快的在线转换器。

![https://avif-converter.online/](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/use-avif-images/1.1.png)

只需按照以下步骤将图像转换为 AVIF：

1. 访问 AVIF online converter。
2. 上传你的旧图片（可以是**PNG**，**JPEG**，**GIF**等）。
3. 等待网站处理转换。
4. 保存新的 AVIF 文件。

### 如何使用支持 AVIF 的图像编辑器创建 AVIF 图像

图像编辑器增加了对 AVIF 图像创建的支持。这些编辑器现在完全支持 AVIF 图像：

- Microsoft Paint –从“19H1”更新开始，你现在可以在 Microsoft Paint 上以“另存为” AVIF 格式绘制图像。
- 用于 Windows 和 Linux 的 GIMP 从 2.10.22 更新开始就提供了 AVIF 支持。
- Photoshop 开发人员也在讨论如何支持 AVIF，希望这将很快得到支持。

## 如何在你的网站上使用 AVIF

AVIF 仍然是一种相对较新的技术。但现在大多数现代浏览器都支持这种格式，这意味着你可以直接在 `<img>` 标签中使用它。只是要记住，并不是所有的浏览器都完全支持该格式。

使用 AVIF 的最好方法是通过内容协商，我们将使用支持内容协商的 HTML 5 `<picture>` 和 `<source>`。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/use-avif-images/2.png)

你可以在此处查看实际示例：https://lyty.dev/diy/how-to-use-avif-on-website.html。

### 哪些浏览器支持 AVIF

- 第一个完全支持 AVIF 的浏览器是 Chrome 85。
- Microsoft Windows 10 还在“19H1”更新中添加了支持。
- Mozilla 仍在努力支持 Firefox 中的图像格式。

## 最后

AVIF 是一个游戏规则的改变者，很快就会成为世界上真正的图像格式。由于它的潜在功能，它很可能很快就会在所有平台上获得全面支持。

与谷歌的**WebP**图像不同，苹果花了整整 10 年的时间来支持，**AVIF**很快就引起了苹果的兴趣，以至于他们现在为这个项目做出了贡献。

**你准备好在网站上开始使用 AVIF 了吗？**

---

原文：[https://www.freecodecamp.org/news/](https://www.freecodecamp.org/news/how-to-use-avif-images-on-your-website/)

作者：Erisan Olasheni
