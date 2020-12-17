---
title: 使用CSS计数器花式玩转列表编号
date: 2020-12-15 16:37:22
img: https://weixin-storage.oss-cn-shanghai.aliyuncs.com/202006/styling-numbered/banner.png
categories:
  - 技术
tags:
  - CSS
---

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202006/styling-numbered/banner.png)

在网页设计中，以有组织的方式表示数据非常重要，这样用户可以轻松地了解网站或内容的结构。最简单的方法是使用有序列表。

如果你需要对数字的外观进行更多的控制，你可能会认为你需要通过 HTML 或 JavaScript 在 DOM 中添加更多的元素，并对其进行样式化。幸运的是，CSS 计数器为你省去了很多麻烦。

在本教程中，我们将演示如何开始使用 CSS 计数器并介绍一些用例。

<!-- more -->

## 有序列表的问题

当编写如下所示的有序列表时，浏览器会自动显示数字。

```html
<ol>
  <li>My First Item</li>
  <li>My Second Item</li>
  <li>My Third Item</li>
</ol>
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202006/styling-numbered/1.png)

这很好，但它不允许你对数字进行样式化。例如，假设你需要把数字放在一个圆圈内。你会怎么做呢？

一种方法是完全摆脱列表，然后自己手动添加数字。

```html
<div><span>1</span> My First Item</div>
<div><span>2</span> My Second Item</div>
<div><span>3</span> My Third Item</div>

<style>
  div {
    margin-bottom: 10px;
  }
  div span {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    width: 25px;
    height: 25px;
    border-radius: 50%;
    background-color: #000;
    color: #fff;
  }
</style>
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202006/styling-numbered/2.png)

这样做可以做到我们需要它做的事情，但也有一些缺点。其一，手写数字很困难。而且，如果你需要修改一个数字怎么办？你就得一个一个地改。你可以使用 JavaScript 动态添加 `<span>` 元素来解决这些问题，但是这样会给 DOM 增加更多的节点，导致内存占用严重。

在大多数情况下，最好使用 CSS 计数器。让我们检查一下原因。

## CSS 计数器简介

CSS 计数器是网页范围变量，可以使用 CSS 规则更改其值。

首先，使用 `counter-reset` 属性设置一个计数器。 `list-number` 是此处要使用的变量名。

```css
.list {
  counter-reset: list-number;
}
```

接下来，使用 `counter-increment` 属性增加计数器的值。

```css
.list div {
  counter-increment: list-number;
}
```

现在，每次出现 `.list div` 元素时，`list-number` 变量都会增加一。

最后，在内容属性和 `counter()` 函数中使用 `:before` 伪元素来显示数字。

```css
.list div:before {
  content: counter(list-number);
}
```

这是完整的代码：

```html
<div class="list">
  <div>My first item</div>
  <div>My second item</div>
  <div>My third item</div>
</div>

<style>
  .list {
    counter-reset: list-number;
  }
  /** 注意，我们可以在:before伪元素中使用counter-increment **/
  .list div:before {
    counter-increment: list-number;
    content: counter(list-number);
  }
</style>
```

效果如下

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202006/styling-numbered/3.png)

我们还没到那儿。让我们设置 `:before` 伪元素的样式，使其看起来更好。

```css
.list div:before {
  counter-increment: list-number;
  content: counter(list-number);

  margin-right: 10px;
  margin-bottom: 10px;
  width: 35px;
  height: 35px;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  font-size: 16px;
  background-color: #d7385e;
  border-radius: 50%;
  color: #fff;
}
```

效果如下

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202006/styling-numbered/4.png)

## 改变起点

默认情况下，`counter-reset` 函数将计数器设置为 `0`，在第一次调用 `counter-increment` 函数后，从 `1` 开始。通过向 `counter-reset` 函数传递一个整数作为第二个参数来设置初始值。

```css
.list {
  counter-reset: list-number 1;
}
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202006/styling-numbered/5.png)

如果要从 `0` 开始，请将初始值设置为 `-1`。

```css
.list {
  counter-reset: list-number -1;
}
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202006/styling-numbered/6.png)

## 更改增量值

默认情况下，`counter-increment` 会将计数器的值增加一个。就像 `counter-reset` 一样，你可以为 `counter-increment` 属性定义一个偏移量。

在此示例中，`counter-reset` 将 `list-number` 设置为 `0`。每次调用 `counter-increment` 时，`list-number` 的值将增加 `2`，因此，你将看到数字分别为 `2`、`4` 和 `6`。

```css
.list {
  counter-reset: list-number;
}
.list div:before {
  counter-increment: list-number 2;
  // 其他样式
}
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202006/styling-numbered/7.png)

## 计数器格式

`counter()` 函数可以具有两个参数：计数器名称和计数器格式。对于第二个参数，可以使用任何有效的 `list-style-type` 值，包括：

- `decimal` (e.g., 1, 2, 3…)
- `lower-latin` (e.g., a, b, c…)
- `lower-roman` (e.g., i, ii, iii…)

默认值为 `decimal`。

例如，如果您像我一样热爱科学，则可以使用 `lower-greek` 进行 alpha-beta 值编号。

```css
.list div:before {
  counter-increment: list-number;
  content: counter(list-number, lower-greek);
  // ... 其他样式
}
```

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202006/styling-numbered/8.png)

## 嵌套的计数器

使用嵌套的有序列表时，编号始终以以下格式显示：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202006/styling-numbered/9.png)

如果你需要子列表项的数字（例如 `1.1`），则可以将 CSS 计数器与 `counters()` 函数一起使用。

```css
<ol>
  <li>
     My First Item
    <ol>
      <li>My Nested First Item</li>
      <li>My Nested Second Item</li>
    </ol>
  </li>
  <li>My Second Item</li>
</ol>

<style>
ol {
  list-style-type:none;
  counter-reset:list;
}
ol li:before {
    counter-increment:list;
    content: counters(list, ".") ". ";
}
</style>
```

效果如下

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202006/styling-numbered/10.png)

请注意，我们使用的是 `counters()` 函数，而不是 `counter()` 。

`counters()` 函数的第二个参数是连接字符串。它还可以具有第三个参数来设置格式（例如希腊语或罗马语）。

### 带有标题的嵌套计数器

`<h1>`，`<h2>` 之类的元素未嵌套在文档中。它们显示为不同的元素，但仍代表一种层次结构。以下是将嵌套数字添加到标题之前的方法：

```css
body {
  counter-reset: h1;
}
h1 {
  counter-reset: h2;
}
h1:before {
  counter-increment: h1;
  content: counter(h1) ". ";
}
h2:before {
  counter-increment: h2;
  content: counter(h1) "." counter(h2) ". ";
}
```

每次找到一个 `h1`，`h2` 计数器就会重置。文档中的每一个 `<h2>` 都会得到一个相对于 `<h1>` 的 `x.y.` 的数字。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202006/styling-numbered/11.png)

## 浏览器支持

值得庆幸的是，自从 CSS2 引入 CSS 计数器以来，浏览器就广泛支持它们。虽然在 `content` 以外的属性中使用`counter()` 函数仍是实验性的，但你可以毫不犹豫地做我们在本教程中涉及到的所有练习。

以下是我可以使用的浏览器支持详细信息。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202006/styling-numbered/12.png)

## 一个简单的挑战

你准备好应对涉及 CSS 计数器的简单挑战了吗？

使用 CSS 计数器，在 10 行代码中显示 `1` 到 `1000` 及其罗马字符。

如果你感到困惑，请按照以下步骤操作：

要创建 1,000 个 `div` 元素，请使用以下内容。

```javascript
for (var i = 0; i < 1000; i++) {
  document.body.appendChild(document.createElement("div"));
}
```

CSS counters：

```css
body {
  counter-reset: number;
}
div:before {
  counter-increment: number;
  content: counter(number) " => " counter(
      number,
      lower-roman
    );
}
```

效果如下

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202006/styling-numbered/13.png)

你想出了什么？

## 结论

以下是我们使用的属性的列表。

CSS counters 是 CSS 中中鲜为人知的功能，但你会惊讶于它的使用频率。在本教程中，我们介绍了如何以及何时使用 CSS 计数器，并列举了一些例子。

| 属性                                                   | 使用                                       |
| ------------------------------------------------------ | ------------------------------------------ |
| counter-reset                                          | 将计数器重置（或创建）为给定值（默认为 0） |
| counter-increment                                      | 给定计数器增加给定偏移量（默认为 1）       |
| counter(counter-name, counter-format)                  | 从给定格式获取计数器的值                   |
| counters(counter-name, counter-string, counter-format) | 从给定的格式获取嵌套计数器的值             |

当然，CSS 计数器是很酷。但我担心的一点是，所有的计数器都是全局的。如果你在一个有很多 CSS 文件的大项目中使用了很多，你可能会找不到它们的创建、重置和增量位置。如果可以的话，不要过度使用，如果一定要使用的话，一定要用描述性的名字做计数器，避免冲突。

---

原文：[https://blog.zhangbing.site/2020/12/15/styling-numbered-lists-with-css-counters/](https://blog.zhangbing.site/2020/12/15/styling-numbered-lists-with-css-counters/)  
来源：[https://blog.logrocket.com](https://blog.logrocket.com/styling-numbered-lists-with-css-counters/)  
作者：Supun Kavinda
