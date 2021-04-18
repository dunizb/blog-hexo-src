---
title: 如何让HTML5数字输入仅接受整数？
date: 2021-03-03 21:16:39
img: http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202103/Inputs-Only-Accept-Integers/banner.jpeg
categories:
  - HTML5&CSS3
tags:
  - HTML5
summary: "这两年我看到很多关于这方面的文章和帖子，这的确是一个非常方便的东西。但是，太多的实现还是有漏洞，残缺不全的实现，等等。"
---

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202103/Inputs-Only-Accept-Integers/banner.jpeg)

这两年我看到很多关于这方面的文章和帖子，这的确是一个非常方便的东西。但是，太多的实现还是有漏洞，残缺不全的实现，等等。

整体概念是合理的：使用 HTML 5 属性来限制可以发送到服务器的内容，然后使用 Javascript 增强它，以限制用户可以在第一个地方输入的内容。

所以让我们来看看这些问题，并更好地实现它。

## 问题 1，不好的脚本

最常见的缺陷是缺乏适当的降级功能。 如果您要在“electron”或“nw.js”中构建完整的堆栈应用程序，那很好，但是这种形式的东西通常在面向公众的网站中没有位置。

就像我经常说的那样，高质量的脚本应该增强已经在工作的页面，而不是用户使用它的唯一方法。

**解决办法？**

使用 `pattern` 和 `step` 属性来限制有效内容。

## 问题 2，正则表达式模式错误或不完整

人们最常用的模式是 `[-/ d] *`，它的问题是允许在任何地方都减号。 虽然肯定可以使用 `type = “number”` 来解决问题，但这不是一个好选择。 截取按键时更是如此，因为减号只能是第一个字符。

它还可能出现问题，因为某些实现“不是”正则表达式，这会导致误报。

**解决办法？**

对于 HTML，使用更好的表达式：`^[- d]\d*$` 更加健壮和准确。减号可以是匹配开始的第一个字符，然后是零个或多个小数，直到字符串结束。

对于 JavaScript，只使用正则表达式来测试数字，并应用一些更实用的逻辑来检测其他值。

简单易行！

## 问题 3，在标记中使用事件属性

我知道在 JSX 垃圾中，有大量的用嘴呼吸的人在鼓励这一点，但如果你在写 vanilla 或其他系统，请出于对圣诞节的爱，从 1997 年的直肠中取出你的头颅。

将 “onkeypress” 或 “onchange” 放在标记中意味着错失了一次缓存机会，也违反了分离关注的原则。以这种方式将 JavaScript 放进标记中，就像在 HTML 4 Strict 中被废弃的所有东西一样，愚蠢得令人发指。就像如果你要用 “text-white box-shadow col-4-s” 这样的属性在你的 HTML 上撒尿一样，请你承认失败，然后回到写 HTML 3.2 的时候，用所有那些 FONT / CENTER 标签、COLOR、BGCOLOR、SIZE、BORDER 和 ALIGN 属性，以及 "用于布局的表格 "来写，你们似乎都很清楚地、很珍视地错过了。

这也意味着您没有完整/适当的事件处理程序访问权限。

**解决办法？**

`Element.addEventListener`，请使用它！

## 问题 4，必须对每个输入进行硬编码

无论是通过问题 3 将事件属性放到标记中，还是通过手动获取唯一 ID 来捕获它们，我几乎没有发现可以实际使用即插即用的标记应用程序的代码库！

**解决办法？**

`document.querySelectorAll('input[type="number”][step="1"]')` 给我们提供所有我们想要的整数输入，所以我们可以增强它。

## 问题 5，一些脚本阻止使用导航控制和正常编辑！

通过拦截并只允许减号和 0...9，它们可以防止退格键、回车键、制表键、箭头、删除、插入等等等等。并不是所有的浏览器都会把这些作为 `event.key` 发送，这要看你钩住的是什么事件。比如 “keypress” 事件在 Firefox 和 Chrome 中会过滤掉一些，以免破坏正常的表单使用，但 “老 Edge” 和 Safari 不会，“keydown” 则什么也不过滤。

**解决办法？**

因为 “keypress” 事件跨浏览器不一致，所以用 keydown 代替。那么我们就可以利用所有控制键在 `Event.key` 值中返回多个字符的事实，我们只需要检查 `Event.key.length>1` 就可以说 “继续允许这些”。

---

正如前面所提到的，我们所需要的只是一个简单的输入，在不使用 JavaScript 的情况下，它首先具有尽可能多的功能！

**HTML：**

```html
<input type="number" step="1" pattern="^[-/d]/d*$" />
```

只接受整数，如果你想只接受正数，可以换成 `pattern="/d+”`

**JavaScript：**

然后我们可以使用 JavaScript 来限制用户的输入，这样人们甚至不允许输入无效值。

```js
(function() {
  var integers = document.querySelectorAll(
      'input[type="number"][step="1"]'
    ),
    intRx = /\d/;

  for (var input of integers) {
    input.addEventListener("keydown", integerChange, false);
  }

  function integerChange(event) {
    if (
      event.key.length > 1 ||
      (event.key === "-" &&
        event.currentTarget.value.length === 0) ||
      intRx.test(event.key)
    )
      return;
    event.preventDefault();
  }
})();
```

我们首先将其包装在 IIFE 中以隔离范围。 然后，抓取我们要在页面上挂接的所有输入，并创建我们的正则表达式。

我在事件开始处而不是在事件内部创建正则表达式，这样我们就不用浪费时间在每个该死的按键上创建它。这就是匿名函数可能带来开销的地方，也是需要告诉那些“函数式程序员”和他们的“副作用”废话的地方。

循环遍历所有输入，并为它们分配事件处理程序。

上述处理程序只是检查我们的返回情况。像箭头、退格键、回车键等控制键都会返回完整的文字来描述它们，所以如果 event.key 的长度>1，我们就不要阻止这些。

如果它是一个负号和第一个字符，通过 return 允许它。

如果它是一个数字，通过 return 允许它。

如果都不是，请阻止该事件。

## Live Demo

这是一个代码本，它包括几个文本字段和多个整数和非整数的数字字段，所以你可以看到它确实只钩住了我们想要的字段。

https://codepen.io/jason-knight/pen/QWGyrwq

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202103/Inputs-Only-Accept-Integers/1.png)

## 悬而未决的问题

我可能会考虑添加的一件事是挂钩“change”事件来拦截粘贴，但由于“pattern”属性不允许提交无效的值，所以应该没有问题。

## 结论

多想想用户可能会输入错误的东西，以及如何处理，但也要记住用户可能输入的所有其他东西，而这些东西与值本身没有关系。

请始终注意，许多用户会主动阻止脚本编写，或者出于安全或可访问性的原因让脚本在 UA 中不可用。增强而不是取代你的基本功能！

计划将这些东西应用到所有这些字段，而不是从一开始就只使用单个元素。

留意低效率，例如如何多次生成用作回调的匿名函数……或如何将脚本放入标记中会错失跨页面或重新访问的缓存机会。

做了这些事情之后，实现这样简单的功能就不会再来困扰您或惹恼用户了。

---

翻译自[levelup.gitconnected.com](https://levelup.gitconnected.com/making-html-5-numeric-inputs-only-accept-integers-d3d117973d56)，作者：Jason Knight
