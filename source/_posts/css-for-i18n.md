---
title: 译|你不知道的CSS国际化
date: 2020-05-07 16:48:39
img: https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/css-for-i18n/banner.jpg
categories:
  - 技术
tags:
  - 前端
  - CSS
  - 翻译
---

我遇到过一些人，他们根本不认为 CSS 与国际化有关，但如果你仔细想想，国际化不仅仅是把你网站上的内容翻译成多种语言，然后就收工了。该内容的呈现方式有各种细微的差别，这些细微的差别会影响到母语人士使用您的网站的体验。

<!-- more -->

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/css-for-i18n/banner.jpg)

对于国际化，没有统一的规范定义，但是 W3C 提供以下指导：

> 国际化是指在设计和开发产品、应用或文档时，为不同文化、地区或语言的目标受众提供方便的本地化服务。

这涉及的内容很多，从 Unicode 和字符编码的使用，到服务于翻译内容的技术实现，以及上述内容的呈现方式，都有很多内容要涉及。今天，我只讨论与多语言支持有关的 CSS 相关方面。

CSS 通过告诉浏览器应该如何设置样式和布局来描述网页的表示。我们可以使用多种方法在具有 CSS 的多语言页面上将不同的样式应用于不同的语言。

此外，还有一些 CSS 属性为文字和书写系统提供了布局和排版功能，这些功能超出了目前在 web 上常见的基于拉丁语的水平自顶向下的功能。

因此，请系好安全带，因为这可能最终是一篇冗长的文章。

## 语言相关样式

你有没有想过，Chrome 浏览器是怎么知道问你要不要翻译网页内容的？这是因为 `<html>` 元素上的 `lang` 属性。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/css-for-i18n/1.png)

`lang` 属性是一个非常重要的属性，因为它标识 web 上文本内容的语言，而且这种信息在许多地方都被使用。上面提到的 Chrome 的内置翻译，针对特定语言的内容的搜索引擎以及屏幕阅读器。

也许你没有想到屏幕阅读器，但如果你不是屏幕阅读器的用户，或者你不认识屏幕阅读器的用户，你可能不会想到屏幕阅读器。屏幕阅读器使用语言信息，因此可以以适当的口音和正确的发音读出内容。

语言相关的风格设计的关键在于在页面标记中正确使用 `lang` 属性,`lang`属性可以识别[ISO 639 language codes](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes)作为值。

在大多数情况下，你会使用像 `zh` 这样的两个字母代码来表示中文，但中文（在其他语言中，如阿拉伯语）被认为是由许多语言组成的大语言，其中有更多的主语子标记。

有关如何构造语言标签的详细说明，请参考[HTML 和 XML 中的语言标签](https://www.w3.org/International/articles/language-tags/)。

一般指导原则是 `html` 元素必须始终具有 `lang` 属性集，然后该属性将被所有其他元素继承。

```html
<html lang="zh"></html>
```

在同一页面上看到不同语言的内容并不罕见。在这种情况下，您可以使用 `<span>` 或 `<div>` 包装该内容，并将正确的 `lang` 属性应用于该包装元素。

```html
<p>
  The fourth animal in the Chinese Zodiac is Rabbit (<span
    lang="zh"
    >兔子</span
  >).
</p>
```

现在我们已经弄清楚了这一点，下面的技术将假定 `lang` 属性已经被负责任地实现。

### :lang() 伪类选择器

结果发现 `:lang()` 伪类选择器并不那么出名。但是，此伪类选择器非常酷，因为即使在元素外部声明了语言，它也可以识别内容的语言。

例如，一行标记有两种语言：

```html
<p>
  We use <em>italics</em>
  to emphasise words in English,
  <span lang="zh">但是中文则是用<em>着重号</em></span
  >.
</p>
```

可以使用以下样式：

```css
em:lang(zh) {
  font-style: normal;
  text-emphasis: dot;
}
```

如果你的浏览器支持 `text-emphasis` CSS 属性，你应该可以看到在 `<em>` 中的每一个中文字符上添加强调符号（传统上用于强调东亚文字的排版符号），Chrome 浏览器需要 `-webkit-` 前缀。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/css-for-i18n/2.png)

但问题是，`lang` 属性不是应用在 `<em>` 元素上，而是应用在它的父类上。伪类仍然可以使用，如果我们使用更常见的属性选择器，例如 `[lang="zh]`，那么这个属性必须在 `<em>` 元素上才能生效。

### 使用属性选择器

这就引出了我们的下一个技术，使用属性选择器。这让我们可以选择具有特定属性的元素或具有特定值的属性。

匹配属性选择器的方法有七种，但是我只讨论那些我认为与 `lang` 属性更相关的方法。我所有的示例都使用中文作为目标语言，因此使用 `zh` 及其变体。

首先，我们可以使用以下语法完全匹配 `lang` 属性值：

```css
[lang="zh"]
/* 只匹配zh */
```

我在前面提到过，中文被认为是一种宏语言，这意味着它的语言标签可以用额外的特殊性来组成，比如说文字子标签 `Hans` 或 `Hant`（W3C 说只有在必要时才用文字子标签来区分你所需要的，否则不要用），地区子标签 `HK` 或`TW` 等等。

重点是，语言标签可以不只是两个字母，而是可以长一些。但最广义的类别永远是第一位的，因此，要以特定字符串开头的属性值为目标，我们使用这个 `^` 语法开头。

```css
[lang^="zh"]
/* 将匹配 zh, zh-HK, zh-Hans, zhong, zh123… 前两个字 */
```

还有另一种涉及到 `|` 的语法，它将与选择器中的确切值匹配，或者与紧跟在 `-` 后面的值开始匹配。好像是为了语言子代码匹配而设计的，不是吗？

```css
[lang|="zh"]
/* 将匹配 zh, zh-HK, zh-Hans, zh-amazing, zh-123 */
```

请记住，对于属性选择器，该属性必须位于要设置样式的元素上，如果该属性在父项或祖先项上将不起作用。

### 普通的类或 ID 呢？

是的，你可以使用普通的类或 id，虽然你将不再利用已经在你的元素上的便利。但是，可以肯定的是，如果确实愿意，为你的元素提供用于应用特定语言相关样式的类名，没有人会阻止你。

## CSS 属性

好了，选择器已经涵盖了。让我们来谈谈我们希望应用到与这些选择器相匹配的元素的样式。

### Writing mode

`writing-mode` 的默认值为 `horizontal-tb`，完全合乎逻辑，因为网络诞生于 CERN，官方语言为英语和法语。而且，无论如何，大多数网络技术都是在英语国家开创的。

但是人类的奇妙给了我们 3000 多种书写语言，它们的文字和书写方向超越了从上到下的水平方向。

传统的蒙古文字是从左至右垂直运行的，而东亚语言（如日语，中文和韩语）在垂直书写时，则是从右至左运行的。允许你这样做的`writing-mode` 属性分别是 `vertical-lr` 和 `vertical-rl`。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/css-for-i18n/3.png)

还有 `sideways-lr` 和 `sideways-rl` 的值，它们使字形向侧面旋转。每个 Unicode 字符都有一个垂直方向属性，该属性会通知渲染引擎默认情况下字形的方向。

我们可以使用 `text-orientation` 属性更改字符的方向。当您在垂直排版的东亚文本中插入基于拉丁语的字词或字符时，通常会起作用。对于缩略语，您可以选择使用 `text-combine-upright` 的方式将字母压缩到一个字符空间。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/css-for-i18n/4.png)

有些人可能想知道从右到左的语言，如阿拉伯语、希伯来语或波斯语(仅举几例)，以及 CSS 是否也适用于这些文字。

简而言之，CSS 不应该用于双向风格设计。W3C 的指南如下:

> 由于方向性是文档结构的一个组成部分，因此应使用标记来设置文档或信息块的方向性，或确定文本中仅靠 Unicode 双向算法不足以实现所需方向性的地方。

通过 CSS 应用此样式可能会被关闭，被覆盖，无法识别或在不同的上下文中被更改/替换。相反，建议使用 `dir` 属性来设置文字的基本显示方向。

### 逻辑属性

网页上的所有内容都是一个盒子，CSS 始终使用`top`、`bottom`、`left` 和 `right` 的物理方向来指示我们要定位盒子的哪一侧。但是，当 `writing-mode` 的方向不是默认的从上到下的水平方向时，这些值会引起混淆。

盒子的物理侧和定位用的逻辑侧的书写方向矩阵及其对应值如下（从撰写本文时起，表格已从规格中删除）：

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/css-for-i18n/5.png)

容器的逻辑顶部使用 `inset-before`，而容器的逻辑底部使用`inset-after`。容器的逻辑左使用 `inset-start`，而容器的逻辑右使用 `inset-end`。

也有相应的 border、margin 和 padding 的映射，分别是:

- `top` to `block-start`
- `right` to `inline-end`
- `bottom` to `block-end`
- `left` to `inline-start`

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/css-for-i18n/6.png)

而在尺寸上的映射如下：`width` 与 `inline-size` 和 `height` 与 `block-size` 的映射。

### 列表和计数器

数字系统是用来表达数字的书写系统，即使最常用的数字系统是印度教阿拉伯数字系统(0、1、2、3 等等)，CSS 也允许我们用其他数字系统来显示有序列表。

预定义的计数器样式可以使用 `list-style-type` 属性，它涵盖了从 `afar` 到 `urdu` 的 174 个数字系统。你可以在[MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/list-style-type)中查看完整的列表。

如果您对 CSS 计数器感兴趣，我在去年的某个时候写了[关于它们](https://www.chenhuijing.com/blog/the-wondrous-world-of-css-counters)的文章，其中探讨了在繁体中文上下文中使用的“ Heavenly-stem”和“ Earthly-branch”数字系统（以及 CSS 中的 Fizzbuzz 实现，因为为什么不）。

### 文本装饰

如前所述，东亚语言没有斜体的概念。相反，我们有着重点，可以将它们放置在字符上方或下方以强调文字，增强语气或避免歧义。

在以水平书写模式书写中文时，这些点位于字符上方，而在以垂直书写模式书写时，这些点位于字符左侧。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/css-for-i18n/7.png)

为了使 CSS 属性更具通用性，在[CSS 文本装饰模块 Level 3]([CSS Text Decoration Module Level 3](https://drafts.csswg.org/css-text-decor-3/#emphasis-marks))中引入了文本强调样式，文本强调位置和文本强调颜色。

您可以使用除点以外的其他符号，例如 `circle`, `triangle`或单个字符作为字符串，位置和颜色也可以根据其各自的属性进行调整。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/css-for-i18n/8.png)

同一规范中还涵盖了行装饰，并为开发人员提供了对下划线和上划线的更精细控制（在规范的 level 4 中）。但是这对于那些有上升线或下行线的文字来说特别有用，因为它们经常会溢出基线。

[CSS 文本修饰模块第 4 级](https://drafts.csswg.org/css-text-decor-4/#text-decoration-skipping)介绍了 `text-decoration-skip`，该控件控制跨过字形的上划线和下划线的绘制方式。再有，某些事情在英语等语言中发生的频率较低，但是在很大程度上影响了诸如缅甸语这样的文字的美观性。

### 字体变化

有两类用于访问 OpenType 功能的 CSS 属性，即高级属性和低级属性。规范建议尽可能使用高级属性。这主要取决于浏览器的支持。

例如，`font-variant-east-asian` 允许控制具有变体的字符的字形形式，例如简体中文字形与繁体中文字形。它是同一字符，但写法可能不同。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/css-for-i18n/9.gif)

还有一种 `font-variant-ligatures`（变体连字），它提供了许多预设的字型和上下文形式的选项，如自由 `discretionary-ligatures` 或 `historical-ligatures` 或 `contextual`。

可通过 `font-feature-settings` 访问低级属性，你可以在其中使用 4 个字母的 OpenType 标记来切换所需的功能（这取决于你的字体是否具有这些功能开头，但我们假设它具有这些功能） 。

有 141 个特征标签，从可选的分数到对齐，从可选的 Ruby 表示法到割零。这些 CSS 属性与字体文件本身的功能密切相关，因此，外部依赖性取决于你选择的字体。

## 结束

这文章子真的很长，所以我将有第二部分来详细介绍我们如何使用我们所涉及的选择器来建立一个布局，以确保我们的布局即使在语言变化的情况下也能保持稳健。像 Flexbox 和 Grid 这样的现代布局属性很适合这样的用例。

关于 CSS，我觉得最有趣的一点是，我们可以通过不同的方式将它们结合起来，以达到无数种结果，而目前有 500 多种 CSS 属性，这就有了很多的可能性。我并不是说什么都可以，因为很多时候，有无数种方法可以达到同样的结果，而且有些方法比其他方法更合适。

然而，通过了解每种技术背后的机制、它的优点和缺点，并了解我们为什么选择某种方式来做事情，我们需要做出明智的决定，以确定哪种方法最适合我们的环境。

我仍然相信，在 30 多年后，网络仍然是信息媒介，内容是关键。因此，无论使用何种语言或文字，内容的表现形式都应该得到优化。我很高兴的是，CSS 正在不断发展，为开发者提供了实现这一目标的方法。

无论如何，请继续关注第 2 部分。

---

原文：[https://www.chenhuijing.com/blog/css-for-i18n/#%F0%9F%91%BE](https://www.chenhuijing.com/blog/css-for-i18n/#👾)

作者：[Chen Hui Jing](https://www.chenhuijing.com/)
