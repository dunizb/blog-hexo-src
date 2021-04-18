---
title: 书写高质量jQuery代码的12条经验
date: 2016-07-15 22:49:00
categories:
  - 前端框架&工具
tags:
  - jQuery
  - 代码质量
---

# 1、正确引用 jQuery

1. 尽量在 body 结束前才引入 jQuery，而不是在 head 中。
2. 借助第三方提供的 CDN 来引入 jQuery，同时注意当使用第三方 CDN 出现问题时，要引入本地的 jQuery 文件。
3. 如果在</body>前引入 script 文件的话，就不用写 document.ready 了，因为这时执行 js 代码时 DOM 已经加载完毕了。
   <!-- more -->

```html
<body>
  <script src="http://lib.sinaapp.com/js/jquery11/1.8/jquery.min.js"></script>
  <script>
    window.jQuery ||
      document.write(
        '<script src="jquery1.8.min.js">\x3C/script>'
      );
  </script>
</body>
```

# 2、优化 jQuery 选择器

高效正确的使用 jQuery 选择器是熟练使用 jQuery 的基础，而掌握 jQuery 选择器需要一定的时间积累，我们开始学习 jQuery 时就应该注意选择器的使用。

```html
<div id="nav" class="nav">
  <a class="home" href="//www.jquery001.com"
    >jQuery学习网</a
  >
  <a class="articles" href="//www.jquery001.com/articles/"
    >jQuery教程</a
  >
</div>
```

如果我们选择 class 为 home 的 a 元素时，可以使用下边代码：

```js
$(".home"); //1
$("#nav a.home"); //2
$("#nav").find("a.home"); //3
```

方法 1，会使 jQuery 在整个 DOM 中查找 class 为 home 的 a 元素，性能可想而知。  
方法 2，为要查找的元素添加了上下文，在这里变为查找 id 为 nav 的子元素，查找性能得到了很大提升。  
方法 3，使用了 find 方法，它的速度更快，所以方法三最好。  
关于 jQuery 选择器的性能优先级，ID 选择器快于元素选择器，元素选择器快于 class 选择器。因为 ID 选择器和元素选择器是原生的 JavaScript 操作，而类选择器不是，大家顺便可以看下 find context 区别，find() children 区别。

### 2.1、一些规则

- CSS 解析引擎将自右向左计算每一条规则，它从关键选择器开始，自右向左计算每一个选择器，直到发现一个匹配的选择器，如果没有找到匹配的选择器则放弃查找。
- 使用较低层的规则通常更有效率。
- 尽可能的具体化的选择器——ID 要比 tag 更好。
- 避免不必要的冗余。

通常请情况下，请保持选择器简单明了（比如充分使用 ID 选择器），尽可能的使用关键选择器更具体，无论对 JavaScript 还是 CSS，这都可以加块网站的速度。到目前为止，无论使用哪一种浏览器，使用 ID 选择器和当个类选择器都是选中元素最快的方式。

### 2.2、避免多个 ID 选择符

Id 选择符应该是唯一的，所以没有必要添加额外的选择符。

```js
// 糟糕
$("div#myid");
$("div#footer a.myLink");
// 建议
$("#myid");
$("#footer .myLink");
```

在此强调，ID 选择符应该是唯一的，不需要添加额外的选择符，更不需要多个后代 ID 选择符。

```js
// 糟糕
$("#outer #inner");
// 建议
$("#inner");
```

### 2.3、避免隐式通用选择符

通用选择符有时是隐式的，不容易发现。

```js
// 糟糕
$(".someclass :radio");
// 建议
$(".someclass input:radio");
```

### 2.4、避免通用选择符

将通用选择符放到后代选择符中，性能非常糟糕。

```js
// 糟糕
$(".container > *");
// 建议
$(".container").children();
```

### 2.5、选择捷径

精简代码的其中一种方式是利用编码捷径。

```js
// 糟糕
if(collection.length > 0){..}
// 建议
if(collection.length){..}
```

### 2.6、为选择器指定上下文

默认情况下，当把一个选择器传递给 jQuery 时，它将遍历整个 DOM，jQuery 方法还具有一个未充分利用的参数，既可以将一个上下文参数传入 jQuery，以限制它只搜索 DOM 中特定的一部分。

```js
//糟糕，会遍历整个DOM
$(".class");
//建议，只搜索#id元素
$(".class", "#id");
```

jQuery 选择器的性能比较：

```js
$(".class", "#id") > $("#id .class") > $(".class");
```

# 3、缓存 jQuery 对象

缓存 jQuery 对象可以减少不必要的 DOM 查找，关于这点大家可以参考下缓存 jQuery 对象来提高性能。

```js
// 糟糕
h = $("#element").height();
$("#element").css("height", h - 20);
// 建议
$element = $("#element");
h = $element.height();
$element.css("height", h - 20);
```

### 3.1、使用子查询缓存的父元素

正如前面所提到的，DOM 遍历是一项昂贵的操作。典型做法是缓存父元素并在选择子元素时重用这些缓存元素。

```js
// 糟糕
var $container = $("#container"),
  $containerLi = $("#container li"),
  $containerLiSpan = $("#container li span");
// 建议 (高效)
var $container = $("#container "),
  $containerLi = $container.find("li"),
  $containerLiSpan = $containerLi.find("span");
```

# 4、变量

### 4.1、避免全局变量

jQuery 与 javascript 一样，一般来说,最好确保你的变量在函数作用域内。

```js
// 糟糕
$element = $("#element");
h = $element.height();
$element.css("height", h - 20);
// 建议
var $element = $("#element");
var h = $element.height();
$element.css("height", h - 20);
```

### 4.2、使用匈牙利命名法

在变量前加\$前缀，便于识别出 jQuery 对象。

```js
// 糟糕
var first = $('#first');
var second = $('#second');
var value = $first.val();
// 建议 - 在jQuery对象前加$前缀
var $first = $('#first');
var $second = $('#second'),
var value = $first.val();
```

### 4.3、使用 Var 链（单 Var 模式）

将多条 var 语句合并为一条语句，我建议将未赋值的变量放到后面。

```js
var $first = $("#first"),
  $second = $("#second"),
  value = $first.val(),
  k = 3,
  cookiestring = "SOMECOOKIESPLEASE",
  i,
  j,
  myArray = {};
```

# 5、正确使用事件委托

在新版 jQuery 中，更短的 `on(“click”)` 用来取代类似 click() 这样的函数。在之前的版本中 `on()` 就是 `bind()`。自从 jQuery 1.7 版本后，on() 附加事件处理程序的首选方法。然而，出于一致性考虑，你可以简单的全部使用 `on()`方法。

```js
<table id='t'>
  <tr>
    <td>我是单元格</td>
  </tr>{" "}
   
</table>
```

比如我们要在上边的单元格上绑定一个单击事件，不注意的朋友可能随手写成下边的样子：

```js
$("#t")
  .find("td")
  .on("click", function() {
    $(this).css({ color: "red", background: "yellow" });
  });
```

这样会为每个 td 绑上事件，在为 100 个单元格绑定 click 事件的测试中，两者性能相差 7 倍之多，好的做法应该是下边写法：

```js
$("#t").on("click", "td", function() {
  $(this).css({ color: "red", background: "yellow" });
});
```

# 6、精简 jQuery 代码

如在上述代码中我们对 jQuery 代码进行了适当的合并，类似的还有.attr()方法等，我们没有写成下边的方式：

```js
$("#t").on("click", "td", function() {
  $(this)
    .css("color", "red")
    .css("background", "yellow");
});
```

### 6.1、合并函数

一般来说,最好尽可能合并函数。

```js
// 糟糕
$first.click(function() {
  $first.css("border", "1px solid red");
  $first.css("color", "blue");
});
// 建议
$first.on("click", function() {
  $first.css({
    border: "1px solid red",
    color: "blue",
  });
});
```

### 6.2、链式操作

jQuery 实现方法的链式操作是非常容易的。下面利用这一点。

```js
// 糟糕
$second.html(value);
$second.on("click", function() {
  alert("hello everybody");
});
$second.fadeIn("slow");
$second.animate({ height: "120px" }, 500);
// 建议
$second.html(value);
$second
  .on("click", function() {
    alert("hello everybody");
  })
  .fadeIn("slow")
  .animate({ height: "120px" }, 500);
```

# 7、减少 DOM 操作

刚开始使用 jQuery 时可能会频繁的操作 DOM，这是相当耗费性能的。如我们要在 body 中动态输出一个表格，一些朋友会这样写：

```js
//糟糕
var $t = $("body");
$t.append("<table>");
$t.append("<tr><td>1</td></tr>");
$t.append("</table>");
//建议
$("body").append("<table><tr><td>1</td></tr></table>");
```

这样在拼接完 table 串后再添加到 body 中，对 DOM 的操作只需一次。群里以前有朋友就因为这个导致在 IE 下输出时出现问题，而关于字符串的拼接可以参考下最快创建字符串的方法。

### 7.1、繁重的操作中分离元素

如果你打算对 DOM 元素做大量操作（连续设置多个属性或 css 样式），建议首先分离元素然后在添加。

```js
// 糟糕
var $container = $("#container"),
  $containerLi = $("#container li"),
  $element = null;
$element = $containerLi.first();
//... 许多复杂的操作
// better
var $container = $("#container"),
  $containerLi = $container.find("li"),
  $element = null;
$element = $containerLi.first().detach();
//... 许多复杂的操作
$container.append($element);
```

### 7.2、最小化 DOM 更新

重布局和重绘是 WEB 页面中最常见的也是最昂贵的两种操作。

- 当改变样式，而不改变页面几何布局时，将会发生重绘。隐藏一个元素或者改变一个元素的背景色时都将导致一次重绘。
- 当对页面结构进行更新时，将导致页面重布局。

```js
//糟糕
for (var i = 0; i < 10000; i++) {
  $("#main table").append("<tr><td>aaaa</td></tr>");
}
//建议
var tablerow = "";
for (var i = 0; i < 10000; i++) {
  tablerow += $("#main table").append(
    "<tr><td>aaaa</td></tr>"
  );
}
$("#main table").append(tablerow);
```

# 8、维持代码的可读性

伴随着精简代码和使用链式的同时，可能带来代码的难以阅读。添加缩紧和换行能起到很好的效果。

```js
// 糟糕
$second.html(value);
$second
  .on("click", function() {
    alert("hello everybody");
  })
  .fadeIn("slow")
  .animate({ height: "120px" }, 500);
// 建议
$second.html(value);
$second
  .on("click", function() {
    alert("hello everybody");
  })
  .fadeIn("slow")
  .animate({ height: "120px" }, 500);
```

# 9、选择短路求值

短路求值是一个从左到右求值的表达式，用 &&（逻辑与）或 ||（逻辑或）操作符。

```js
// 糟糕
function initVar($myVar) {
  if (!$myVar) {
    $myVar = $("#selector");
  }
}
// 建议
function initVar($myVar) {
  $myVar = $myVar || $("#selector");
}
```

# 10、坚持最新版本

新版本通常更好：更轻量级，更高效。显然，你需要考虑你要支持的代码的兼容性。例如，2.0 版本不支持 ie 6/7/8。

摒弃弃用方法

关注每个新版本的废弃方法是非常重要的并尽量避免使用这些方法。

```js
// 糟糕 - live 已经废弃
$("#stuff").live("click", function() {
  console.log("hooray");
});
// 建议
$("#stuff").on("click", function() {
  console.log("hooray");
});
// 注：此处可能不当，应为live能实现实时绑定，delegate或许更合适
```

# 11、最后忠告

最后，我记录这篇文章的目的是提高 jQuery 的性能和其他一些好的建议。如果你想深入的研究对这个话题你会发现很多乐趣。记住，jQuery 并非不可或缺，仅是一种选择。思考为什么要使用它。DOM 操作？ajax？模版？css 动画？还是选择符引擎？或许 javascript 微型框架或 jQuery 的定制版是更好的选择。

虽然都是陈词滥调，但是我发现还不能很好得做到上述所有，记录下来希望自己能够全部做到。

# 12、不使用 jQuery

原生函数总是最快的，这点不难理解，在代码书写中我们不应该忘记原生 JS。
就先总结这几条吧，每条建议并不难理解，但总结全面的话还是要花费不少时间的。如在减少代码段中，如果需要根据条件从数组中得到新数组时，可以使用`$.grep()`方法，如果你在使用 jQuery 时有自己心得的话，欢迎在留言中和大家分享！

---

**参考资料：**
《jQuery 高级编程》[Cesar Otero,Rob Larsen]，（译）施宏斌，清华大学出版社，2013 年 4 月第 1 版
http://mp.weixin.qq.com/s?__biz=MzI5MzIyNzAyNA==&mid=2247483859&idx=1&sn=5c9ddc0018554cf8dc164d312ec41910&scene=1&srcid=0527fUjiz508GaFrXiesQk3n#rd
