---
title: 如何创建与框架无关的JavaScript插件
date: 2020-11-14 16:03:25
img: http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/agnostic-javascript-plugin/banner.png
cover: true
categories:
  - 技术
tags:
  - JavaScript
  - 实战
  - 翻译
---

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/agnostic-javascript-plugin/banner.png)

JavaScript 中的插件使我们能够扩展语言，以实现所需的某些强大（或不够强大）的功能。插件/库本质上是打包的代码，可以使我们免于一遍又一遍地编写相同的东西（功能）。

在 JavaScript 生态系统中，有数百个框架，这些框架中的每一个都为我们提供了一个创建插件的系统，以便为框架添加新的东西。

如果你看一下 NPM 注册表，几乎所有的 JavaScript 插件都是在那里发布的，你会看到有超过一百万个插件以简单库和框架的形式发布。

为每个框架创建插件的方式可能会有很大不同。例如，Vue.js 有自己的插件系统，这与你为 React.js 创建插件的方式不同。然而，这一切都归结为相同的 JavaScript 代码。

因此，使用普通 JavaScript 创建插件，让你有能力创建一个无论在哪个框架下都能使用。

“与框架无关的 JavaScript 插件是无需框架上下文即可工作的插件，您可以在任何框架（甚至没有框架）中使用插件”

<!-- more -->

## 构建库时要记住的事项

- 您应该对插件有一个目标——这是插件要实现的关键
- 您的插件应易于使用以达到预期用途
- 您的插件应在很大程度上可定制
- 您的插件应该有一个文档，指导将要使用该插件的开发人员

现在，让我们着眼于上述几点。

## 我们将创造什么

在本文中，我将向您展示如何创建与框架无关的插件。在本教程中，我们将创建一个旋转木马/幻灯片插件——该插件的目标。

这个插件会暴露一些方法，可以被插件的用户调用 `.next()` 和 `.prev()`

## 起步

让我们创建一个新的文件夹来存放我们的插件代码和任何其他必要的文件，我将把我的文件夹称为 `TooSlidePlugin`。

在这个文件夹中，在你喜欢的编辑器中创建一个新的 JavaScript 文件。这个文件将包含插件的代码，我把我的文件叫做 `tooSlide.js`。

有时，我什至想像在开始创建插件之前（从最终开发人员的角度）如何使用插件。

```javascript
var slider = new ToolSidePlugin({
  slideClass: ".singleSlide",
  container: ".slideContainer",
  nextButton: ".next",
  previousButton: ".prev",
});
```

上面的代码假定有一个名为 `TooSlides` 的构造函数，该构造函数接收具有某些属性的对象作为参数。对象的属性是 `slidesClass`、`container`、`nextButton` 和 `previousButton`，这些是我们希望用户能够自定义的属性。

我们将从将插件创建为单个构造函数开始，以便其本身具有名称空间。

```javascript
function ToolSidePlugin() {}
```

### Options

由于我们的插件，`TooSlides` 需要一个选项参数，所以我们会定义一些默认的属性，这样如果我们的用户没有指定自己的属性，就会使用默认的属性。

```javascript
function ToolSidePlugin(options) {
  let defaultOptions = {
    slideClass: ".singleSlide",
    container: ".slideContainer",
    nextButton: ".nextSlide",
    previousButton: ".previousSlide",
  };

  options = { ...defaultOptions, ...options };

  let _this = this;
  let slides = [];
  let currentSlideIdex = 0;
}
```

我们创建了一个 `defaultOptions` 对象来保存一些属性，我们还使用 JavaScript 扩展操作符将传入的选项与默认选项合并。我们将 `this` 分配给另一个变量，因此我们仍然可以在内部函数中对其进行访问。

我们还创建了两个变量 `slides`，它将保存所有我们想要用作幻灯片的图片，以及 `currentSlideIndex`，它保存当前正在显示的幻灯片的索引。

接下来，由于我们的幻灯片需要有一些控制，可以用来向前和向后移动幻灯片，我们将在构造函数中添加下面的方法。

```javascript
this.prepareControls = function() {
  const nextButton = document.createElement("button");
  const previousButton = document.createElement("button");

  nextButton.setAttribute("class", "next");
  nextButton.InnerHTML = "Next";

  previousButton.setAttribute("class", "prev");
  nextButton.InnerHTML = "Prev";

  let controleContainer = document.createElement("div");

  controleContainer.setAttribute(
    "class",
    "too-slide-control-container"
  );

  controleContainer.appendChild(previousButton);
  controleContainer.appendChild(nextButton);

  document
    .querySelector(options.container)
    .appendChild(controleContainer);

  nextButton.addEventListener("click", function() {
    _this.next();
  });
  previousButton.addEventListener("click", function() {
    _this.previous();
  });
};
```

在 `.prepareControls()` 方法中，我们创建了一个容器 DOM 元素来保存控件按钮，我们自己创建了下一个和上一个按钮，并将它们附加到 `controlContainer`。

然后我们给两个按钮附加事件监听器，分别调用 `.next()` 和 `.previous()` 方法。别担心，我们很快就会创建这些方法。

接下来，我们将添加两个方法：`.goToSlide()` 和 `.hideOtherSlides()`。

```javascript
this.goToSlide = function(index) {
  this.hideOtherSlides();
  if (index > slides.length - 1) {
    index = 0;
  }
  if (index < 0) {
    index = slides.length - 1;
  }
  slides[index].style = "display:block";
  currentSlideIndex = index;
};

this.hideOtherSlides = function() {
  document
    .querySelectorAll(options.slidesClass)
    .forEach((slide, index) => {
      slides[index].style = "display: none";
    });
};
```

`.goToSlide()` 方法采用参数 `index`，这是我们要显示的幻灯片的索引，此方法首先隐藏当前正在显示的任何幻灯片，然后仅显示我们要显示的幻灯片。

接下来，我们将添加 `.next()` 和 `.previous()` 辅助方法，分别帮助我们向前一步，或者向后一步（还记得我们之前附加的事件监听器吗？

```javascript
this.next = function() {
  this.goToSlide(currentSlideIndex + 1);
};
this.previous = function() {
  this.goToSlide(currentSlideIndex - 1);
};
```

这两个方法基本上调用 `.goToSlide()` 方法，并将 `currentSlideIndex` 移动 1。

现在，我们还将创建一个 `.init()` 方法，该方法将在实例化构造函数时帮助我们进行设置。

```javascript
this.init = function() {
  document.querySelectorAll(options.container).className +=
    " too-slide-slider-container";
  document
    .querySelectorAll(options.slidesClass)
    .forEach((slide, index) => {
      slides[index] = index;
      slides[index].style = "display:none";
      slides[index].className =
        " too-slide-single-slide too-slide-fade";
    });

  this.goToSlide(0);
  this.prepareControls();
};
```

如您所见，`.init()` 方法获取所有幻灯片图像并将其存储在我们之前声明的 `slides` 数组中，并默认隐藏所有图像。

然后，它通过调用 `.goToSlide(0)` 方法显示幻灯片中的第一张图像，并且还通过调用 `.prepareControls()` 设置我们的控制按钮。

为了收尾我们的构造函数代码，我们将在构造函数中调用 `.init()` 方法，这样每当构造函数被初始化时，`.init()` 方法总是被调用。

最终代码如下所示：

```javascript
function ToolSidePlugin(options) {
  let defaultOptions = {
    slidesClass: ".singleSlide",
    container: ".slideContainer",
    nextButton: ".nextSlide",
    previousButton: ".previousSlide",
  };

  options = { ...defaultOptions, ...options };

  let _this = this;
  let slides = [];
  let currentSlideIdex = 0;

  this.init = function() {
    document.querySelectorAll(
      options.container
    ).className += " too-slide-slider-container";
    document
      .querySelectorAll(options.slidesClass)
      .forEach((slide, index) => {
        slides[index] = index;
        slides[index].style = "display:none";
        slides[index].className =
          " too-slide-single-slide too-slide-fade";
      });

    this.goToSlide(0);
    this.prepareControls();
  };

  this.prepareControls = function() {
    const nextButton = document.createElement("button");
    const previousButton = document.createElement("button");

    nextButton.setAttribute("class", "next");
    nextButton.InnerHTML = "Next";

    previousButton.setAttribute("class", "prev");
    nextButton.InnerHTML = "Prev";

    let controleContainer = document.createElement("div");

    controleContainer.setAttribute(
      "class",
      "too-slide-control-container"
    );

    controleContainer.appendChild(previousButton);
    controleContainer.appendChild(nextButton);

    document
      .querySelector(options.container)
      .appendChild(controleContainer);

    nextButton.addEventListener("click", function() {
      _this.next();
    });
    previousButton.addEventListener("click", function() {
      _this.previous();
    });
  };

  this.goToSlide = function(index) {
    this.hideOtherSlides();
    if (index > slides.length - 1) {
      index = 0;
    }
    if (index < 0) {
      index = slides.length - 1;
    }
    slides[index].style = "display:block";
    currentSlideIndex = index;
  };

  this.hideOtherSlides = function() {
    document
      .querySelectorAll(options.slidesClass)
      .forEach((slide, index) => {
        slides[index].style = "display: none";
      });
  };

  this.next = function() {
    this.goToSlide(currentSlideIndex + 1);
  };
  this.previous = function() {
    this.goToSlide(currentSlideIndex - 1);
  };

  this.init();
}
```

## 添加 CSS

在存放我们的插件项目的文件夹中，我们将添加一个 CSS 文件，其中包含我们的滑块的基本样式。我将把这个文件称为 `tooSlide.css`。

```css
* {
  box-sizing: border-box;
}

body {
  font-family: Verdana, sans-serif;
  margin: 0;
  padding: 30px;
}

.too-slide-single-slide {
  display: none;
  max-height: 100%;
  width: 100%;
}

.too-slide-single-slide img {
  height: 100%;
  width: 100%;
}
img {
  vertical-align: middle;
}

/* Slideshow container */
.too-slide-slider-container {
  width: 100%;
  max-width: 100%;
  position: relative;
  margin: auto;
  height: 400px;
}

.prev,
.next {
  cursor: pointer;
  position: absolute;
  top: 50%;
  width: auto;
  padding: 10px;
  margin-right: 15px;
  margin-left: 15px;
  margin-top: -22px;
  color: white;
  font-weight: bold;
  font-size: 18px;
  transition: 0.6s ease;
  border-radius: 0 3px 3px 0;
  user-select: none;
  border-style: unset;
  background-color: blue;
}

.next {
  right: 0;
  border-radius: 3px 0 0 3px;
}

.prev:hover,
.next:hover {
  background-color: rgba(0, 0, 0, 0.8);
}

.too-slide-fade {
  -webkit-animation-name: too-slide-fade;
  -webkit-animation-duration: 1.5s;
  animation-name: too-slide-fade;
  animation-duration: 1.5s;
}

@-webkit-keyframes too-slide-fade {
  from {
    opacity: 0.4;
  }
  to {
    opacity: 1;
  }
}

@keyframes too-slide-fade {
  from {
    opacity: 0.4;
  }
  to {
    opacity: 1;
  }
}

/* On smaller screens, decrease text size */
@media only screen and (max-width: 300px) {
  .prev,
  .next,
  .text {
    font-size: 11px;
  }
}
```

## 测试我们的插件

为了测试我们的插件，我们将创建一个 HTML 文件，我将其命名为 `index.html`。我们还将添加 4 张图片用作幻灯片，所有图片都与我们的插件 JavaScript 代码位于同一目录中。

我的 HTML 文件如下所示：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta
      name="viewport"
      content="width=device-width, initial-scale=1.0"
    />
    <title>测试幻灯片</title>
    <link rel="stylesheet" href="tooSlide.css" />
  </head>
  <body>
    <div class="contaoner">
      <div class="slideContainer">
        <div class="singleSlide">
          <img
            src="slide1.png"
            alt="slide1"
            class="slideImage"
          />
        </div>
        <div class="singleSlide">
          <img
            src="slide2.png"
            alt="slide2"
            class="slideImage"
          />
        </div>
        <div class="singleSlide">
          <img
            src="slide3.png"
            alt="slide3"
            class="slideImage"
          />
        </div>
        <div class="singleSlide">
          <img
            src="slide4.png"
            alt="slide4"
            class="slideImage"
          />
        </div>
      </div>
    </div>

    <script src="tooSlide.js"></script>
    <script>
      var slider = new ToolSidePlugin({
        slideClass: ".singleSlide",
        container: ".slideContainer",
        nextButton: ".next",
        previousButton: ".prev",
      });
    </script>
  </body>
</html>
```

在 HTML 文件的头部分，我调用了 `tooSlide.css` 文件，而在文件的末尾，我调用了 `tooSlide.js` 文件。

完成此操作后，我们将能够实例化我们的插件构造函数：

```javascript
var slider = new ToolSidePlugin({
  slideClass: ".singleSlide",
  container: ".slideContainer",
  nextButton: ".next",
  previousButton: ".prev",
});
```

最后的效果：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/agnostic-javascript-plugin/1.gif)

## 为您的 JavaScript 项目编写文档

文档是你教人们如何使用你的插件。因此，它需要你花一些心思。

对于我们新创建的插件，我将从在项目目录中创建 README.md 文件开始。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/agnostic-javascript-plugin/2.png)

## 发布你的插件

在编写了您的插件之后，您很可能希望其他开发人员从您的新创建中受益，因此我将向您展示如何做到这一点。

你可以通过两种方式让你的插件对其他人可用：

- 在 Github 上托管它，当您这样做时，任何人都可以下载仓库，包括文件(.js 和.css)，并立即使用您的插件。
- 发布在 npm 上，请查看官方的 npm 文档来指导您。

就是这样。

## 结束

在本文中，我们构建了一个插件来完成一件事：幻灯片图像。

它也是零依赖的，现在我们可以开始用我们的代码来帮助别人，就像我们也得到了帮助一样。

该插件教程的代码可在我的 github 上找到：https://github.com/dunizb/CodeTest/tree/master/JavaScript/TooSlidePlugin

---

原文：[https://blog.logrocket.com/](https://blog.logrocket.com/how-to-create-a-framework-agnostic-javascript-plugin/)

作者：Sodeeq Elusoji
