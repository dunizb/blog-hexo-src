---
title: 如何使用CSS Paint API动态创建与分辨率无关的可变背景
date: 2020-07-08 20:44:41
categories:
  - 技术
tags:
  - 前端
  - CSS
---

现代 Web 应用对图像的需求量很大，它们占据网络下载的大部分字节。通过优化它们，你可以更好地利用它们的性能。如果你碰巧使用几何图形作为背景图像，有一个替代方案：你可以使用[CSS Paint API](https://developer.mozilla.org/en-US/docs/Web/API/CSS_Painting_API)以编程方式生成背景。

在本教程中，我们将探讨其功能，并探讨如何使用它来动态创建与分辨率无关的动态背景。这将是本教程的输出：

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202007/create-dynamic-backgrounds/1.gif)

## 设置项目

首先，创建一个新的 `index.html` 文件，并编写如下代码：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>🎨 CSS Paint API</title>
    <link rel="stylesheet" href="styles.css" />
  </head>
  <body>
    <textarea class="pattern"></textarea>
    <script>
      if ("paintWorklet" in CSS) {
        CSS.paintWorklet.addModule("pattern.js");
      }
    </script>
  </body>
</html>
```

有几件事要注意：

- 在第 13 行，我们加载了一个新的 Paint worklet。目前，全球支持率约为 63％。因此，我们必须首先检查是否支持 `paintWorklet`。
- 我正在使用 `textarea` 进行演示，因此我们可以看到调整画布的大小将如何重绘图案。
- 最后，你需要创建一个 `pattern.js`（用于注册绘画工作区）以及一个 `styles.css`，我们可以在其中定义几个样式。

### 什么是 worklet？

Paint worklet 是一个定义了应该画在画布上的内容的类。它们的工作原理与 `canvas` 元素类似。如果你以前有这方面的知识，代码会看起来很熟悉。然而，它们并不是 100%相同的。例如，在 worklet 中还不支持文本渲染方法。

在这里，我们还要定义 CSS 样式。这是我们要使用 worklet 的地方：

```css
.pattern {
  width: 250px;
  height: 250px;
  border: 1px solid #000;

  background-image: paint(pattern);
}
```

我添加了一个黑色的边框，这样我们可以更好地看到 `textarea`。要引用一个 paint worklet，你需要把 `paint(worklet-name)` 作为一个值传递给 `background-image` 属性。但是 `pattern` 是从哪里来的呢？我们还没有定义它，所以让我们把它作为我们的下一步。

## 定义 Worklet

打开 `pattern.js` 并添加以下内容：

```javascript
class Pattern {
  paint(context, canvas, properties) {}
}

registerPaint("pattern", Pattern);
```

在这里可以使用 `registerPaint` 方法注册`paintWorklet`。你可以在此处定义的 CSS 中引用第一个参数。第二个参数是定义应在 canvas 上绘画的类。它具有一个包含三个参数的 `paint` 方法：

- `context`：这将返回一个 `PaintRenderingContext2D` 对象，该对象实现 `CanvasRenderingContext2D API` 的子集。
- `canvas`：这是我们的 canvas，一个 `PaintSize` 对象，只有两个属性：width 和 height。
- `properties`：这将返回一个 `StylePropertyMapReadOnly` 对象，我们可以使用该对象通过 JavaScript 读取 CSS 属性及其值。

### 绘制矩形

下一步是显示一些东西，所以让我们绘制矩形。将以下内容添加到 `paint` 方法中：

```javascript
paint(context, canvas, properties) {
  for (let x = 0; x < canvas.height / 20; x++) {
    for (let y = 0; y < canvas.width / 20; y++) {
      const bgColor = (x + y) % 2 === 0 ? '#FFF' : '#FFCC00';

      context.shadowColor = '#212121';
      context.shadowBlur = 10;
      context.shadowOffsetX = 10;
      context.shadowOffsetY = 1;

      context.beginPath();
      context.fillStyle = bgColor;
      context.rect(x * 20, y * 20, 20, 20);
      context.fill();
    }
  }
}
```

我们在这里所做的就是创建一个嵌套循环，以循环遍历画布的宽度和高度。由于矩形的大小为 20，因此我们要将矩形的高度和宽度除以 20。

在第 4 行，我们可以使用模数运算符在两种颜色之间切换。我还为深度添加了一些阴影。最后，我们在画布上绘制矩形。如果在浏览器中打开它，则应具有以下内容：

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202007/create-dynamic-backgrounds/2.png)

## 使背景动态化

遗憾的是，除了调整 `textarea` 的大小和一窥 Paint API 是如何重绘一切的，这大部分还是静态的。所以，让我们通过添加我们可以改变的自定义 CSS 属性来让事情变得更加动态。

打开你的 `styles.css` 并在其中添加以下几行：

```css
.pattern {
     width: 250px;
     height: 250px;
     border: 1px solid #000;

     background-image: paint(pattern);
+    --pattern-color: #FFCC00;
+    --pattern-size: 23;
+    --pattern-spacing: 0;
+    --pattern-shadow-blur: 10;
+    --pattern-shadow-x: 10;
+    --pattern-shadow-y: 1;
 }
```

你可以通过在 CSS 属性前加上 `—` 来定义自定义 CSS 属性。这些属性可以被 `var()` 函数使用。但在我们的案例中，我们将在我们的 paint worklet 中使用它。

### 在 CSS 中检查支持

为确保支持 Paint API，我们还可以检查 CSS 中的支持。为此，我们有两个选择：

- 使用 `@supports` 规则守护规则。
- 使用后备背景图片。

```css
/* 第一种选择 */
@supports (background: paint(pattern)) {
  /**
   * 如果该部分得到评估，则意味着Paint API 支持
   **/
}

/**
 * 第二种选择
 * 如果支持Paint API，后一条规则将覆盖第一条规则。
 * 如果不支持，CSS将使其无效，并将应用url()的规则。
 **/
.pattern {
  background-image: url(pattern.png);
  background-image: paint(pattern);
}
```

### 访问绘画 worklet 中的参数

要读取 `pattern.js` 中的这些参数，您需要向定义 paint worklet 的类中添加一个新方法：

```javascript
class Pattern {
  // `inputProperties`方法返回的任何东西，paint worklet都可以访问。
  static get inputProperties() {
    return [
      "--pattern-color",
      "--pattern-size",
      "--pattern-spacing",
      "--pattern-shadow-blur",
      "--pattern-shadow-x",
      "--pattern-shadow-y",
    ];
  }
}
```

要在 `paint` 方法内部访问这些属性，可以使用 `properties.get`：

```javascript
paint(context, canvas, properties) {
  const props = {
    color: properties.get('--pattern-color').toString().trim(),
    size: parseInt(properties.get('--pattern-size').toString()),
    spacing: parseInt(properties.get('--pattern-spacing').toString()),
    shadow: {
      blur: parseInt(properties.get('--pattern-shadow-blur').toString()),
      x: parseInt(properties.get('--pattern-shadow-x').toString()),
      y: parseInt(properties.get('--pattern-shadow-y').toString())
    }
  };
}
```

对于颜色，我们需要将其转换为字符串。其他所有内容都需要转换为数字。这是因为 `properties.get` 返回 `CSSUnparsedValue`。

![properties.get的返回值](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202007/create-dynamic-backgrounds/3.png)

为了使内容更具可读性，我创建了两个新函数来为我们处理解析：

```javascript
paint(context, canvas, properties) {
  const getPropertyAsString = property => properties.get(property).toString().trim();
  const getPropertyAsNumber = property => parseInt(properties.get(property).toString());

  const props = {
    color: getPropertyAsString('--pattern-color'),
    size: getPropertyAsNumber('--pattern-size'),
    spacing: getPropertyAsNumber('--pattern-spacing'),
    shadow: {
      blur: getPropertyAsNumber('--pattern-shadow-blur'),
      x: getPropertyAsNumber('--pattern-shadow-x'),
      y: getPropertyAsNumber('--pattern-shadow-y')
    }
  };
}
```

现在我们要做的就是用相应的 `prop` 值替换 for 循环中的所有内容：

```javascript
for (let x = 0; x < canvas.height / props.size; x++) {
  for (let y = 0; y < canvas.width / props.size; y++) {
    const bgColor = (x + y) % 2 === 0 ? "#FFF" : props.color;

    context.shadowColor = "#212121";
    context.shadowBlur = props.shadow.blur;
    context.shadowOffsetX = props.shadow.x;
    context.shadowOffsetY = props.shadow.y;

    context.beginPath();
    context.fillStyle = bgColor;
    context.rect(
      x * (props.size + props.spacing),
      y * (props.size + props.spacing),
      props.size,
      props.size
    );
    context.fill();
  }
}
```

现在回到你的浏览器，试着改变一下。

![在DevTools中编辑背景](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202007/create-dynamic-backgrounds/4.gif)

## 总结

为什么 CSS Paint API 对我们有用？有哪些用例？

最明显的是，它减小了响应的大小。通过消除图像的使用，你可以节省一个网络请求和几千字节。这样可以提高性能。

对于使用 DOM 元素的复杂 CSS 效果，你还可以减少页面上的节点数量。因为你可以用 Paint API 创建复杂的动画，所以不需要额外的空节点。

在我看来，最大的好处是它的可定制性远高于静态背景图片。API 还可以创建与分辨率无关的图像，所以你不用担心错过单一屏幕尺寸。

如果你今天选择使用 CSS Paint API，请确保你提供 polyfill，因为它仍然没有被广泛采用。如果你想调整完成的项目，你可以从这个[GitHub 仓库](https://github.com/flowforfrank/css-paint)中克隆它。

---

来源：[https://medium.com/better-programming](https://medium.com/better-programming/how-to-create-dynamic-backgrounds-with-the-css-paint-api-ebd733254014)，作者：Ferenc Almasi，翻译：公众号《前端全栈开发者》
