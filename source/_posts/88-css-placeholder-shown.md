---
title: CSS :placeholder-shown 是什么？
date: 2021-04-16 21:15:42
img: http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/css-placeholder-shown/banner.png
categories:
  - HTML5$CSS3
tags:
  - CSS3
summary: "使用此伪类来设置当前显示占位符文本的输入的样式，换句话说，用户未在文本框中键入任何内容"
---

使用此伪类来设置当前显示占位符文本的输入的样式，换句话说，用户未在文本框中键入任何内容 📭

根据您的输入是否为空，应用一些动态样式非常好 👏

<!-- more -->

```css
input:placeholder-shown {
  border-color: pink;
}
```

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/css-placeholder-shown/1.png)

## 它是如何工作的？

`:placeholder-show` 是 CSS 伪类，可让您将样式应用于具有占位符文本的 `<input>` 或 `<textarea>`。

```html
<input placeholder="placeholder text" />
<textarea placeholder="placeholder text"></textarea>
```

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/css-placeholder-shown/2.png)

结果：

- 如果显示占位符，则为粉红色，表示用户未输入任何内容
- 如果未显示任何占位符，则为黑色，表示用户已键入内容

### `:placeholder-showd`必须具有占位符

如果元素没有占位符文本，则此选择器将不起作用。

```html
<input /><!-- 没有占位符 -->

<!-- 这也被视为没有占位符文本 -->
<input placeholder="" />
```

```css
input:placeholder-shown {
  border-color: pink;
}
```

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/css-placeholder-shown/3.png)

### `:placeholder-shown` vs `::placeholder`

因此，我们可以使用 `:placeholder-shown` 设置输入元素的样式。

```css
input:placeholder-shown {
  border: 1px solid pink;
  background: yellow;
  color: green;
}
```

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/css-placeholder-shown/4.gif)

☝️ 嗯...注意到有些奇怪 🤔——我们将颜色设置为：绿色，但没有用。好吧，这是因为 `:placeholder-shown` 只针对输入本身。但是对于实际的占位符文本，您必须使用伪元素 `::placeholder`。

```css
input::placeholder {
  color: green;
}
```

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/css-placeholder-shown/5.gif)

但是！当我在处理这个问题时，我注意到还有一些其他属性，如果在 `:placeholder-shown` 级别应用，将会影响到占位符文本。

```css
input:placeholder-shown {
  font-style: italic;
  text-transform: uppercase;
  letter-spacing: 5px;
}
```

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/css-placeholder-shown/6.png)

现在，我真的不知道为什么会发生这种情况 🤷‍♀️ 也许是因为这些属性被占位符继承了。

### `:placeholder-shown` vs `:empty`

尽管 `:placeholder-shown` 是专门用于确定元素是否显示占位符的。实际上，我们可以使用它来检查输入是否为空（当然，假设所有输入都有一个占位符）。因此，也许您的下一个问题是，我们不能使用[CSS empty](https://www.samanthaming.com/tidbits/72-css-empty-selector/)吗？好吧，让我们检查一下 👩‍🔬

```html
<input value="not empty" /> <input /><!-- empty -->
```

```css
input:empty {
  border: 1px solid pink;
}

input {
  border: 1px solid black;
}
```

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/css-placeholder-shown/7.png)

期待：

- 如果为空则为粉红色
- 如果不为空为黑色

嗯...从这里开始，您可能会认为 `:empty` 似乎在起作用，因为我们看到的是粉红色边框。但这实际上不起作用 😔

粉红色显示的原因是因为伪类增加了特异性，类似于类选择器（即 `.form-input`）比类型选择器（即 `input`）具有更高的特异性。高特异性选择器将始终覆盖低特异性设置的样式。

这是判决！不要使用 `:empty` 检查输入元素是否为空 🙅‍♀️

### 如何在没有占位符的情况下检查输入是否为空？

好了，所以我们检查输入是否为空的唯一方法是使用 `:placeholder-shown`。但是，如果我们的输入元素没有占位符，会发生什么情况？好吧，这是一个聪明的方法！传入一个空字符串 `" "`。

```html
<input placeholder=" " /><!-- 👈 传递空字符串 -->
```

```css
input:placeholder-shown {
  border-color: pink;
}
```

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/css-placeholder-shown/8.gif)

## 与其他选择器组合

所以，我们可以针对显示占位符文字的输入元素，这很酷。换句话说，如果显示了占位符文本，那么一定意味着该元素是空的。利用这些知识，我们可以将这个伪类与其他选择器结合起来，做一些非常整洁的事情！让我们来看看 🤩。

### 反向 `:placeholder-shown` 为 `:not`

我们可以使用 `:not` 伪类来做一些反向的事情。在这里，我们可以在输入不是空的时候进行目标操作。

```html
<input placeholder="placeholder" value="not empty" />
```

```css
input:not(:placeholder) {
  border-color: green;
}
```

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/css-placeholder-shown/9.gif)

结果：

- 绿色，如果不为空，则表示用户已经输入了一些内容。
- 如果为空，则为黑色

### 浮动标签

使用占位符而不使用标签的问题之一就是无障碍，因为一旦你在打字的时候，占位符文字就没有了，这可能会导致用户的困惑。一个真正好的解决方案是浮动标签。最初，占位符文本显示时没有标签，而一旦用户开始输入，标签就会出现。这样一来，你仍然可以在不影响用户体验和可访问性的前提下，保持表单的简洁性。双赢 🥳

而这是可以用纯 CSS 实现的，我们只需要将 `placeholder-shown` 与 `:not` 和 `+` 结合起来就可以了。这是一个超级简化版的浮动标签。

```html
<input name="name" placeholder="Type name..." />
<label for="name">NAME</label>
```

```css
label {
  display: none;
  position: absolute;
  top: 0;
}

input:not(:placeholder-shown) + label {
  display: block;
}
```

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/css-placeholder-shown/10.gif)

## 浏览器支持

对 `:placeholder-shown` 的支持非常好！这包括 Internet Explorer（是的，我和你一样惊讶 😆）。但是，对于 IE，你需要使用非标准名称 `:-ms-input-placeholder`。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/css-placeholder-shown/11.png)

---

原文：https://www.samanthaming.com/tidbits/88-css-placeholder-shown/
