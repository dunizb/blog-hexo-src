---
title: 【小技巧】在React中防范XSS攻击
date: 2019-11-24 22:36:46
img: http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/XSS-Attacks-in-React/banner.jpeg
categories:
  - 前端框架&工具&工具
tags:
  - React
summary: "在本文中，我们将查看几个用 React 编写的代码示例，这样您也可以保护您的站点和用户"
---

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/XSS-Attacks-in-React/banner.jpeg)

跨站点脚本（XSS）攻击是一种将恶意代码注入网页然后执行的攻击。这是前端 Web 开发人员必须应对的最常见的网络攻击形式之一，因此了解攻击的工作原理和防范方法非常重要。

在本文中，我们将查看几个用 React 编写的代码示例，这样您也可以保护您的站点和用户。

<!-- more -->

## 示例 1：React 中成功的 XSS 攻击

对于我们所有的示例，我们将实现相同的基本功能。我们将在页面上有一个搜索框，用户可以在其中输入文本。点击“Go”按钮将模拟运行搜索，然后一些确认文本将显示在屏幕上，并向用户重复他们搜索的术语。这是任何允许你搜索的网站的标准行为。

![Search demo](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/XSS-Attacks-in-React/1.png)

很简单，对吧？会出什么问题呢？

好吧，如果我们在搜索框中输入一些 HTML 怎么办？让我们尝试以下代码段：

```html
<img src="1" onerror="alert('Gotcha!')" />
```

现在会发生什么？

![XSS攻击被执行](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/XSS-Attacks-in-React/2.png)

哇，`onerror` 事件处理程序已执行！那不是我们想要的！我们只是不知不觉地从不受信任的用户输入中执行了脚本。

![渲染残破的图像](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/XSS-Attacks-in-React/3.png)

然后，破碎的图像将呈现在页面上，那也不是我们想要的。

那么我们是怎么到这里的呢？好吧，在本例中渲染搜索结果的 JSX 中，我们使用了以下代码：

```html
<p style="{searchResultsStyle}">
  You searched for:
  <b
    ><span
      dangerouslySetInnerHTML="{{"
      __html:
      this.state.submittedSearch
      }}
  /></b>
</p>
```

用户输入被解析和渲染的原因是我们使用了 `dangerouslySetInnerHTML` 属性，这是 React 中的一个特性，它的工作原理就像原生的 `innerHTML` 浏览器 API 一样，由于这个原因，一般认为使用这个属性是不安全的。

## 示例 2：React 中的 XSS 攻击失败

现在，让我们看一个成功防御 XSS 攻击的示例。这里的修复方法非常简单：为了安全地渲染用户输入，我们不应该使用 `dangerouslySetInnerHTML` 属性。相反，让我们这样编写输出代码：

```html
<p style="{searchResultsStyle}">
  You searched for: <b>{this.state.submittedSearch}</b>
</p>
```

我们将输入相同的输入，但这一次，这里是输出：

![防止XSS攻击](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/XSS-Attacks-in-React/4.png)

很好！用户输入的内容在屏幕上只呈现为文字，威胁已被解除。

这是个好消息！React 默认情况下会对渲染的内容进行转义处理，将所有的数据都视为文本字符串处理，这相当于使用原生 `textContent` 浏览器 API。

## 示例 3：在 React 中清理 HTML 内容

所以，这里的建议似乎很简单。只要不要在你的 React 代码中使用`dangerouslySetInnerHTML`你就可以了。但如果你发现自己需要使用这个功能呢？

例如，也许您正在从诸如 Drupal 之类的内容管理系统（CMS）中提取内容，而其中某些内容包含标记。（顺便说一句，我可能一开始就不建议在文本内容和来自 CMS 的翻译中包含标记，但对于本例，我们假设您的意见被否决了，并且带有标记的内容将保留下来。）

在这种情况下，您确实想解析 HTML 并将其呈现在页面上。那么，您如何安全地做到这一点？

答案是在渲染 HTML 之前对其进行清理。与完全转义 HTML 不同，在渲染之前，您将通过一个函数运行内容以去除任何潜在的恶意代码。

您可以使用许多不错的 HTML 清理库。和任何与网络安全有关的东西一样，最好不要自己写这些东西。有些人比你聪明得多，不管他们是好人还是坏人，他们比你考虑得更多，一定要使用久经考验的解决方案。

我最喜欢的清理程序库之一称为[sanitize-html](https://www.npmjs.com/package/sanitize-html)，它的功能恰如其名。您从一些不干净的 HTML 开始，通过一个函数运行它，然后得到一些漂亮、干净、安全的 HTML 作为输出。如果您想要比它们的默认设置提供更多的控制，您甚至可以自定义允许的 HTML 标记和属性。

![清理你的HTML](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/XSS-Attacks-in-React/5.png)

## 结束

就是这样了。如何执行 XSS 攻击，如何防止它们，以及如何在必要时安全地解析 HTML 内容。

**祝您编程愉快，安全无虞！**

完整的代码示例可在 GitHub 上找到：https://github.com/thawkin3/xss-demo

---

本文翻译自：[levelup.gitconnected.com](https://levelup.gitconnected.com/protecting-against-xss-attacks-in-react-52442d9fff4c)，作者：Tyler Hawkins
