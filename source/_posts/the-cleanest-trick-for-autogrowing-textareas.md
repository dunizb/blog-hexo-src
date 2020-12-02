---
title: 自动增长Textareas的最干净技巧
date: 2020-12-02 17:03:04
img: https://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/autogrowing-textareas/2.png
categories:
  - 技术
tags:
  - JavaScript
  - 翻译
---

想法是使 `<textarea>` 更像 `<div>`，因此它的高度可以扩展以包含当前值。这几乎是奇怪的，没有一个简单的原生解决方案，不是吗？

现在我得到了一个非常好的原生解决方案。

这里是演示，如果你只是想要一个工作的例子

```html
<h1>Auto-Growing <code>&lt;textarea></code></h1>

<form action="#0">
  <label for="text">Text:</label>
  <div class="grow-wrap">
    <textarea
      name="text"
      id="text"
      onInput="this.parentNode.dataset.replicatedValue = this.value"
    ></textarea>
  </div>
</form>
```

```css
.grow-wrap {
  /* 简单的方法将元素叠加在一起，并根据最高者的高度确定它们的大小。 */
  display: grid;
}
.grow-wrap::after {
  /* 注意奇怪的空间！需要预防跳动行为 */
  content: attr(data-replicated-value) " ";

  /* 这就是textarea文本的表现形式 */
  white-space: pre-wrap;

  /* 隐藏在视图，点击和屏幕阅读器中 */
  visibility: hidden;
}
.grow-wrap > textarea {
  /* 您可以保留此设置，但是在用户调整大小后，它将破坏自动调整大小 */
  resize: none;

  /* Firefox显示增长的滚动条，您可以像这样隐藏。 */
  overflow: hidden;
}
.grow-wrap > textarea,
.grow-wrap::after {
  /* 需要相同的样式！ */
  border: 1px solid black;
  padding: 0.5rem;
  font: inherit;

  /* 放在彼此之上 */
  grid-area: 1 / 1 / 2 / 2;
}

body {
  margin: 2rem;
  font: 1rem/1.4 system-ui, sans-serif;
}

label {
  display: block;
}
```

效果

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/autogrowing-textareas/1.gif)

**诀窍是，你要准确地将 `<textarea>` 的内容复制到一个可以自动展开高度的元素中，并匹配它的大小。**

所以你有一个 `<textarea>`，它不能自动展开高度。

相反，您可以在另一个元素中完全复制该元素的外观，内容和位置，再复制的元素隐藏起来。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/autogrowing-textareas/2.png)

现在，这三个元素都是相互联系的。无论哪一个子元素最高，都会把父元素推到那个高度，而另一个子元素也会跟随。这意味着 `<textarea>` 的最小高度将成为“基础”高度，但是如果复制的文本元素碰巧变高了，所有的东西也会随之变高。

好聪明，我太喜欢了。

**您需要确保复制的元素完全相同**

相同的字体，相同的填充，相同的页边距，相同的边框...所有内容。这是一个相同的副本，只是在视觉上隐藏了 `visibility: hidden;`；如果不是完全一样的，那么所有的东西都不会完全正确地生长在一起。

我们还需要在复制的文本上 `white-space: pre-wrap;` ，因为这就是 textareas 的表现。

**这是最奇怪的部分**

在我的演示中，我将 `::after` 用于复制的文本。我不确定这是否是最好的方法。对我来说感觉很干净，但是我想知道使用 `<div aria-hidden =“ true”>` 对于屏幕阅读器是否更安全？

或 `visibility: hidden;` 够了吗？无论如何，那不是奇怪的部分。这是奇怪的部分：

```css
content: attr(data-replicated-value) " ";
```

因为我使用的是伪元素，伪元素是将 `data` 属性从元素中取出并以额外的空间将内容呈现到页面的行(这是奇怪的部分)。如果你不这样做，最终的结果会让人感觉 "跳脱"。我不能说我完全理解它，但它似乎更好地尊重了跨 textarea 和文本元素的换行行为。

如果你不想使用伪元素，嘿嘿，我没意见，只要注意跳动的行为即可。

---

原文：[https://blog.zhangbing.site/2020/12/02/the-cleanest-trick-for-autogrowing-textareas/](https://blog.zhangbing.site/2020/12/02/the-cleanest-trick-for-autogrowing-textareas/)  
来源：[https://css-tricks.com/](https://css-tricks.com/the-cleanest-trick-for-autogrowing-textareas/)
