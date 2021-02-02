---
title: 用一些简单的CSS和JavaScript创建一个标签云
date: 2021-02-02 14:51:31
img: http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202102/Tag-Cloud/2.gif
categories:
  - 技术
tags:
  - 实战
  - CSS
  - JavaScript
---

> 翻译自：[css-tricks.com](https://css-tricks.com/create-a-tag-cloud-with-some-simple-css-and-even-simpler-javascript/)，作者：Mark Conroy

我一直很喜欢标签云，我喜欢通过看标签的相对字体大小来了解网站上最受欢迎的标签是哪些，受欢迎的标签更大。

制作标签云有多困难？一点也不难。让我们来看看！

<!-- more -->

## 让我们从 HTML 开始

对于我们的 HTML，我们将每个标签放入`<ul class=“tags”><ul>` 列表中，我们将使用 JavaScript 进行注入。

我模拟出了一些[JSON](https://gist.github.com/markconroy/536228ed416a551de8852b74615e55dd)，每个属性都有一定数量的文章标签。让我们写一些 JavaScript 来获取 JSON feed 并做三件事。

首先，我们将从列表的每个条目中创建一个 `<li>`。想象一下，到目前为止，HTML 是这样的：

```html
<ul class="tags">
  <li>align-content</li>
  <li>align-items</li>
  <li>align-self</li>
  <li>animation</li>
  <li>...</li>
  <li>z-index</li>
</ul>
```

其次，我们将每个属性的文章数放在每个列表项旁边的括号中。所以现在，标记是这样的：

```html
<ul class="tags">
  <li>align-content (2)</li>
  <li>align-items (2)</li>
  <li>align-self (2)</li>
  <li>animation (9)</li>
  <li>...</li>
  <li>z-index (4)</li>
</ul>
```

第三，也是最后，我们将在每个标记周围创建一个链接，该链接将到达正确的位置。在这里，我们可以为每个项目设置 `font-size` 属性，这取决于该属性标记了多少篇文章。

```html
<li class="tag">
  <a
    class="tag__link"
    href="https://example.com/tags/animation"
    style="font-size: 5em"
  >
    animation (9)
  </a>
</li>
```

## JavasScript 部分

让我们看一下执行此操作的 JavaScript。

```js
const dataURL =
  "https://gist.githubusercontent.com/markconroy/536228ed416a551de8852b74615e55dd/raw/9b96c9049b10e7e18ee922b4caf9167acb4efdd6/tags.json";
const tags = document.querySelector(".tags");
const fragment = document.createDocumentFragment();
const maxFontSizeForTag = 6;

fetch(dataURL)
  .then(function(res) {
    return res.json();
  })
  .then(function(data) {
    // 1. 从数据创建新数组
    let orderedData = data.map((x) => x);
    // 2. 按每个标签具有的文章数进行排序
    orderedData.sort(function(a, b) {
      return (
        a.tagged_articles.length - b.tagged_articles.length
      );
    });
    orderedData = orderedData.reverse();
    // 3. 获取包含最多文章的标签的值
    const highestValue =
      orderedData[0].tagged_articles.length;
    // 4. 为数据中的每个结果创建一个列表项
    data.forEach((result) =>
      handleResult(result, highestValue)
    );
    // 5. 将标签的完整列表追加到tags元素
    tags.appendChild(tag);
  });
```

上面的 JavaScript 使用 `Fetch` API 来获取 `tags.json` 所在的 URL。在这里，我们插入一个名为 `orderedData` 的新数组（这样我们就不会更改原始数组），找到包含最多文章的标签。我们将在稍后的 `font-size` 中使用这个值，这样所有其他标签都将有一个相对于它的 `font-size`。然后，`forEach` 结果在响应中，我们调用一个名为 `handleResult()` 的函数，并将 `result` 和 `highestValue` 作为参数传递给该函数。它还会创建：

- 一个称为 `tags` 的变量，这是我们将用来注入从结果中创建的每个列表项的变量
- 一个 `fragment` 的变量，用于保存循环的每次迭代的结果，我们稍后将其附加到 `tags`，以及
- 一个最大字体大小的变量，稍后将在字体比例中使用。

接下来，`handleResult(result)` 函数：

```js
function handleResult(result, highestValue) {
  const tag = document.createElement("li");
  tag.classList.add("tag");
  tag.innerHTML = `<a class="tag__link" href="${
    result.href
  }" style="font-size: ${result.tagged_articles.length *
    1.25}em">${result.title} (${
    result.tagged_articles.length
  })</a>`;

  // 将每个标签附加到片段 fragment
  fragment.appendChild(tag);
}
```

这是一个非常简单的函数，它可以创建一个列表元素，并将其设置为名为 `tag` 的变量，然后为这个列表元素添加一个 `.tag` 类。创建完毕后，它将列表项的 `innerHTML` 设置为链接，并使用 JSON Feed 中的值（例如，指向标记的链接的 `result.href`）填充该链接的值。当每个 `li` 被创建后，它将作为一个字符串添加到 `fragment` 中，我们稍后将把它追加到 `tags` 变量中。这里最重要的一项是内联样式标签，它使用文章数量--`result.tagged_articles.length`--为这个列表项设置一个使用 `em` 单位的相对字体大小。稍后，我们将把这个值改成一个公式来使用基本的字体比例。

我发现此 JavaScript 有点难看且难以理解，因此让我们为我们的每个属性创建一些变量和一个简单的字体比例公式，以整理并使其更易于阅读。

```js
function handleResult(result, highestValue) {
  // 设置我们的变量
  const name = result.title;
  const link = result.href;
  const numberOfArticles = result.tagged_articles.length;
  let fontSize =
    (numberOfArticles / highestValue) * maxFontSizeForTag;
  fontSize = +fontSize.toFixed(2);
  const fontSizeProperty = `${fontSize}em`;

  // 为每个标签创建一个列表元素，并内联字体大小
  const tag = document.createElement("li");
  tag.classList.add("tag");
  tag.innerHTML = `<a class="tag__link" href="${link}" style="font-size: ${fontSizeProperty}">${name} (${numberOfArticles})</a>`;

  // 将每个标签附加到fragment
  fragment.appendChild(tag);
}
```

通过在创建 HTML 之前设置一些变量，可以使代码更容易阅读。而且这也让我们的代码更 DRY 一些，因为我们可以在多个地方使用 `numberOfArticles` 变量。

在此 `.forEach` 循环中返回了每个标签之后，它们将一起收集在 `fragment` 中。之后，我们使用 `appendChild()` 将它们添加到 `tags` 元素中。这意味着 DOM 只被操作一次，而不是每次循环运行时都被操作，如果我们碰巧有大量的 `tags`，这是一个不错的性能提升。

## 字体缩放

我们现在拥有的就可以很好地工作了，我们可以开始编写 CSS 了。然而，我们的 `fontSize` 变量公式意味着拥有最多文章的标签（也就是 25 篇文章的 "flex"）将是 6em (25 / 25 _ 6 = 6)，但只有一篇文章的标签将是其大小的 1/25 (1 / 25 _ 6 = 0.24)，使得内容无法阅读。如果我们有一个有 100 篇文章的标签，那么较小的标签就会更糟糕（1 / 100 \* 6 = 0.06）。

为了解决这个问题，我添加了一个简单的 `if` 语句，如果返回的 `fontSize` 小于 1，则将 fontSize 设置为 1。现在，所有标签的字体范围为 1em 至 6em，四舍五入到小数点后两位。要增加最大标签的大小，只需更改 `maxFontSizeForTag` 的值即可。你可以根据你处理的内容数量来决定什么最适合你。

```js
function handleResult(result, highestValue) {
  // 设置我们的变量
  const numberOfArticles = result.tagged_articles.length;
  const name = result.title;
  const link = result.href;
  let fontSize =
    (numberOfArticles / highestValue) * maxFontSizeForTag;
  fontSize = +fontSize.toFixed(2);

  // 确保我们的字体大小至少为1em
  if (fontSize <= 1) {
    fontSize = 1;
  } else {
    fontSize = fontSize;
  }
  const fontSizeProperty = `${fontSize}em`;

  // 然后，为每个标签创建一个列表元素，并内联字体大小。
  tag = document.createElement("li");
  tag.classList.add("tag");
  tag.innerHTML = `<a class="tag__link" href="${link}" style="font-size: ${fontSizeProperty}">${name} (${numberOfArticles})</a>`;

  // 将每个标签附加到fragment
  fragment.appendChild(tag);
}
```

## 编写 CSS！

我们将 `flexbox` 用于布局，因为每个标签的宽度都可以变化。然后，我们将它们与 `justify-content: center`，并删除列表项目符号。

```css
.tags {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  max-width: 960px;
  margin: auto;
  padding: 2rem 0 1rem;
  list-style: none;
  border: 2px solid white;
  border-radius: 5px;
}
```

我们还将为单个标签使用 flexbox。这样我们就可以用 `align-items: center` 来垂直对齐，因为它们会根据字体大小而有不同的高度。

```css
.tag {
  display: flex;
  align-items: center;
  margin: 0.25rem 1rem;
}
```

标签云中的每一个链接都有一点点的填充，只是为了让它在严格的尺寸之外可以稍微点击。

```css
.tag__link {
  padding: 5px 5px 0;
  transition: 0.3s;
  text-decoration: none;
}
```

我发现这在小屏幕上非常方便，特别是对于那些可能很难点击链接的人。最初的 `text-decoration` 被删除了，因为我认为我们可以假设标签云中的每一条文字都是一个链接，所以不需要对它们进行特殊的装饰。

我将添加一些颜色，以使样式更加美观：

```css
.tag:nth-of-type(4n + 1) .tag__link {
  color: #ffd560;
}
.tag:nth-of-type(4n + 2) .tag__link {
  color: #ee4266;
}
.tag:nth-of-type(4n + 3) .tag__link {
  color: #9e88f7;
}
.tag:nth-of-type(4n + 4) .tag__link {
  color: #54d0ff;
}
```

这个配色方案是直接从[Chris’ blogroll](https://chriscoyier.net/)偷来的，从标签 1 开始的每四个标签是黄色的，从标签 2 开始的每四个标签是红色的，从标签 3 开始的每四个标签是紫色的。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202102/Tag-Cloud/1.png)

然后，我们为每个链接设置焦点和悬停状态：

```css
.tag:nth-of-type(4n + 1) .tag__link:focus,
.tag:nth-of-type(4n + 1) .tag__link:hover {
  box-shadow: inset 0 -1.3em 0 0 #ffd560;
}
.tag:nth-of-type(4n + 2) .tag__link:focus,
.tag:nth-of-type(4n + 2) .tag__link:hover {
  box-shadow: inset 0 -1.3em 0 0 #ee4266;
}
.tag:nth-of-type(4n + 3) .tag__link:focus,
.tag:nth-of-type(4n + 3) .tag__link:hover {
  box-shadow: inset 0 -1.3em 0 0 #9e88f7;
}
.tag:nth-of-type(4n + 4) .tag__link:focus,
.tag:nth-of-type(4n + 4) .tag__link:hover {
  box-shadow: inset 0 -1.3em 0 0 #54d0ff;
}
```

我也许可以在这个阶段为颜色创建一个自定义变量，比如 `--yellow: #ffd560` 等，但为了支持 IE 11，我还是决定采用手写的方法。我喜欢 `box-shadow` 悬停效果，它只需要很少量的代码就能实现比标准的下划线或底边框更有视觉效果的东西。在这里使用 `em` 单位意味着我们可以很好地控制阴影相对于需要覆盖的文本的大小。

首先，将每个标记链接设置为悬停时为黑色：

```css
.tag:nth-of-type(4n + 1) .tag__link:focus,
.tag:nth-of-type(4n + 1) .tag__link:hover,
.tag:nth-of-type(4n + 2) .tag__link:focus,
.tag:nth-of-type(4n + 2) .tag__link:hover,
.tag:nth-of-type(4n + 3) .tag__link:focus,
.tag:nth-of-type(4n + 3) .tag__link:hover,
.tag:nth-of-type(4n + 4) .tag__link:focus,
.tag:nth-of-type(4n + 4) .tag__link:hover {
  color: black;
}
```

我们完成了！这是最终结果：

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202102/Tag-Cloud/2.gif)

## 最后

最近整理了一份优质视频教程资源，想要的可以关注公众号即可免费领取哦！
![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/%E4%BC%98%E8%B4%A8%E8%AF%BE%E7%A8%8B.png)
