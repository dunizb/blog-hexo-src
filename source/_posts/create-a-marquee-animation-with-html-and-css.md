---
title: 用HTML和CSS创建一个无缝滚动动画
date: 2021-03-19 22:13:17
categories:
  - 技术
tags:
  - 实战
summary: "有一些图片，在页面上无限循环地水平移动"
---

有一天，我访问了一个网站，它有一个很酷的功能，我想在我的个人网站上实现。有一些图片，在页面上无限循环地水平移动。我心想，如何才能重新实现这个功能呢？

我在谷歌上搜索了一下，找了图像转盘、图像滑块、水平图像动画，但是没有找到我想要的东西。后来，我终于看到了一个词，Marquee。这正是我所需要的！我惊讶地发现，居然有一个叫 `marquee` 的 HTML 标签，可以实现我所需要的功能，唯一的问题是，这个标签已经过时了。

![https://developer.mozilla.org/en-US/docs/Web/HTML/Element/marquee](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202101/Marquee-Animation/1.png)

继续下一个解决方案，我决定使用基本的 CSS 关键帧来实现此动画。在这篇文章中，我将与你分享我的解决方案，并指导你如何构建一个无限滚动水平动画。下面是我们将要构建的预览，如果你只想查看完整的源代码，可以在文章结尾查看我的 GitHub 存储库。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202101/Marquee-Animation/2.gif)

## 开始

首先创建一个新项目，并在文本编辑器中打开它。创建一个 HTML 文件、一个 CSS 文件和一个文件夹来保存图像。在我的例子中，我从 Unsplash 下载了一些动物图片。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202101/Marquee-Animation/3.png)

## HTML

首先，打开 `index.html` 文件。如果你使用的是 VS Code，你应该安装了 Emmet，你可以通过输入感叹号和 Enter 来创建一个 HTML 模板。然后，在头部标签中链接你的 `style.css` 文件。

在 body 中，创建一个带有 `marquee` 类的 `div` 作为 marquee 动画的容器。在 `div` 中，创建一个无序列表，包含 5 个列表项，每个列表项元素将包含一张图片。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202101/Marquee-Animation/4.png)

## CSS

接下来，打开你的 `styles.css` 文件，我们将在项目中添加一些基本样式。注意 `marquee` 类和 `marqee-item` 类的宽度，我们将容器的宽度设置为 800px，每张图片的宽度为 200px。目前，你应该看到屏幕上只显示了 4 张图片，因为我们将容器上的 overflow 设置为 hidden。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202101/Marquee-Animation/5.png)

## 创建平滑的无限滚动

最后，让我们添加我们的动画。为 `marquee-content` 类创建一个从右向左滚动的关键帧。我们将 `translatex` 设置为-1000px，因为我们有 5 张图片，总计 1000px，这样，5 个图像将在重新启动循环之前完全滚动到容器外部。

```css
@keyframes scrolling {
  0% {
    transform: translateX(0);
  }
  100% {
    transform: translatex(-1000px);
  }
}
```

我们的动画目前可以正常播放，并将继续无限滚动。然而，有一个问题，你可以看到下面。我们想让下一个循环图像出现在上一个图像之后，以模拟连续的效果。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202101/Marquee-Animation/6.gif)

为此，我们需要在最后一张图像之后填充空白。打开 `index.html` 文件，我们将通过重复前 4 张图片来填充多余的空间，而不是只有 5 张图片，总共会有 9 个列表项元素。

现在，当循环结束时，我们将再次显示前 4 张图片，而不是在下一个循环开始前的空白处。然后，循环将以第一张图片在开始时结束，与下一个循环将以第一张图片在开始时结束的时间完全相同。这样就可以模拟出我们想要的连续效果。

## 源码

最终的 HTML 和 CSS 文件请查看这里：[https://coding.zhangbing.site](https://coding.zhangbing.site/view.html?url=./list/marquee-animation.html)。
