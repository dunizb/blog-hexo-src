---
title: CSS的低权重原则—避免滥用子选择器
date: 2015-05-24 18:49:10
categories:
  - HTML5$CSS3
tags:
  - CSS3
description: "“CSS的低权重原则”既可以的到`font-size:40px`的样式，又可以得到`color： red`的样式。如果两个不同选择符设置的样式有冲突，又会如何？"
---

CSS 设置的样式是可以层叠的，如下面[代码 1]

<!-- more -->

[代码 1]

```html
<style type="text/css">
  span {
    font-size: 40px;
  }
  .test {
    color: red;
  }
</style>

<span class="test">CSS的低权重原则</span>
```

“CSS 的低权重原则”既可以的到`font-size:40px`的样式，又可以得到`color： red`的样式。如果两个不同选择符设置的样式有冲突，又会如何？如下面[代码 2]

[代码：CSS 层叠有冲突的情况]

```html
<style type="text/css">
  span {
    font-size: 40px;
    color: green;
  }
  .test {
    color: red;
  }
</style>

<span class="test">CSS的低权重原则</span>
```

“CSS 的低权重原则”颜色会是什么呢？是对`span`设置的`color:green`呢，还是第`.test`设置的`color:red`呢？这就涉及 CSS 选择符的权重问题了。

CSS 的选择符是有权重的，当不同选择符的样式设置有冲突时，会采用权重高的选择符设置的样式。

权重的规则是这样的：HTML 标签的权重是 1，class 的权重是 10，id 的权重是 100，例如 p 的权重是 1，“div em”的权重是 1+1=2，“strong.demo”的权重是 10+1=11，“#test.red”的权重是 100+10=110.

[代码 2]中，选择符 span 的权重是 1，“.test”的权重是 10，所以“CSS 的低权重原则”的 color 应该是 red。

但是如果是如下[代码 3]时，又会如何呢？

```html
<style type="text/css">
  span {
    font-size: 40px;
  }
  .test {
    color: red;
  }
  .test2 {
    color: green;
  }
</style>

<span class="test test2">CSS的低权重原则</span>
```

span 的标签同时挂了 test 和 test2 两个 class，他们的权重都是 10，那么“CSS 的低权重原则”的 color 又该是哪个呢？**如果 CSS 选择符权重相同，那么样式就遵循就近原则，哪个选择符后定义，就采用哪个选择符的样式。**

[代码 3]中 test2 后定义，所以 color 会采用`.test2`的设置为 green。

如果改变`.test`和`.test2`定义的位置，如[代码 4]

```html
<style type="text/css">
  span {
    font-size: 40px;
  }
  .test2 {
    color: green;
  }
  .test {
    color: red;
  }
</style>

<span class="test test2">CSS的低权重原则</span>
```

那么 “CSS 的低权重原则”的 color 就为 red 了。

需要强调的是就近原则指的是选择符 的先后顺序，而不是挂 class 名的先后顺序，`<span class="test test2">CSS的低权重原则</span>`和`<span class="test2 test">CSS的低权重原则</span>`没有区别。

CSS 选择符权重是个容易被忽略的问题。但事实上如果不太注意选择符的权重，常会出现意向不到的小麻烦。例如下面[代码 5]

```html
<style type="text/css">
  #test {
    font-size: 14px;
  }
</style>

<p id="test">CSS的选择符权重很重要</p>
```

现在需要将“很重要”三个字设置为红色，我们应该怎么做呢？

方案一，利用子选择器，如[代码 6]

```html
<style type="text/css">
  #test {
    font-size: 14px;
  }
  #test span {
    color: red;
  }
</style>

<p id="test">CSS的选择符权重<span>很重要</span></p>
```

方案二，新建 class，如[代码 7]

```html
<style type="text/css">
  #test {
    font-size: 14px;
  }
  .font {
    color: red;
  }
</style>

<p id="test">
  CSS的选择符权重<span class="font">很重要</span>
</p>
```

很多工程师推荐使用方案一，因为使用子选择器可以避免新增 class，让 HTML 代码更简洁，这么考虑是有道理的，但如果这时需求有变化，需要添加新的额文字进来，如[代码 8]

```html
<style type="text/css">
  #test {
    font-size: 14px;
  }
  #test span {
    color: red;
  }
</style>

<p id="test">
  CSS的选择符权重<span>很重要</span>，我们要小心处理
</p>
```

要求我们将“小心处理”设置为绿色，我们可以这么做，如[代码 9]

```html
<style type="text/css">
  #test {
    font-size: 14px;
  }
  #test span {
    color: red;
  }
  .font {
    color: green;
  }
</style>

<p id="test">
  CSS的选择符权重<span>很重要</span>，我们要<span
    class="font"
    >小心处理</span
  >
</p>
```

本以为小心处理会被选择符 font 设置为绿色，但它却被选择符权重更高的“#test span”设置成了红色，子选择器在无意中影响到了我们新添加的代码。如果想要达到我们的预期，我们需要按代码[9]重写

```html
<style type="text/css">
  #test {
    font-size: 14px;
  }
  #test span {
    color: red;
  } /* 选择符权重为100+1=101 */
  #test .font {
    color: green;
  } /* 选择符权重为100+10=110 */
</style>

<p id="test">
  CSS的选择符权重<span>很重要</span>，我们要<span
    class="font"
    >小心处理</span
  >
</p>
```

而如果使用方案二呢？

```html
<style type="text/css">
  #test {
    font-size: 14px;
  }
  .test {
    color: red;
  } /* 选择符权重为100+1=101 */
  .font2 {
    color: green;
  } /* 选择符权重为100+10=110 */
</style>

<p id="test">
  CSS的选择符权重<span class="font">很重要</span
  >，我们要<span class="font2">小心处理</span>
</p>
```

因为没有使用子选择器，所以我们添加新代码挂上新的 class 就可以顺利的完成样式的设置了。

使用子选择器，会增加 CSS 选择符的权重，CSS 的选择符权重越高，样式越不易被覆盖，所以除非确定 HTML 结构非常稳定，一定不会再修改了，否则尽量使用子选择器。为了保证样式容易被覆盖，提高可维护性，CSS 选择符权重尽可能低。

少用子选择器，就需要多添加 class，在 Web 标准盛行的初期，很多工程师认为多添加 class 是不好的，如果能使用子选择器就应该尽量使用，使用大量的 class 的做法叫做“多 class 症”。在进过大量实践后，我并不认为多 class 有太大的坏处，相反，与使用子选择器相比，新添加 class 反而跟利于维护。
