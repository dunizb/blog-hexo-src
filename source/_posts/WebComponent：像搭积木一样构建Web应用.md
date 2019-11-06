---
title: WebComponent：像搭积木一样构建Web应用
date: 2019-09-22 11:40:46
categories:
- 技术
tags:
- 前端
- 组件化
---

我们站在开发者和项目角度来聊聊 WebComponent，它是一套技术的组合，能提供给开发者组件化开发的能力。

那什么是组件化呢？其实组件化并没有一个明确的定义，不过这里我们可以使用 10 个字来形容什么是组件化，那就是：**对内高内聚，对外低耦合**。对内各个元素彼此紧密结合、相互依赖，对外和其他组件的联系最少且接口简单。
<!-- more -->

## 阻碍前端组件化的因素
在前端虽然 HTML、CSS 和 JavaScript 是强大的开发语言，但是在大型项目中维护起来会比较困难，如果在页面中嵌入第三方内容时，还需要确保第三方的内容样式不会影响到当前内容，同样也要确保当前的 DOM 不会影响到第三方的内容。

CSS 的全局属性会阻碍组件化，DOM 也是阻碍组件化的一个因素，因为在页面中只有一个 DOM，任何地方都可以直接读取和修改 DOM。所以使用 JavaScript 来实现组件化是没有问题的，但是 JavaScript 一旦遇上 CSS 和 DOM，那么就相当难办了。

## WebComponent 组件化开发
现在我们了解了**CSS 和 DOM 是阻碍组件化的两个因素**，那要怎么解决呢？

WebComponent 给出了解决思路，它提供了对局部视图封装能力，可以让 DOM、CSSOM 和 JavaScript 运行在局部环境中，这样就使得局部的 CSS 和 DOM 不会影响到全局。

WebComponent 是一套技术的组合，具体涉及到了**Custom elements（自定义元素）、Shadow DOM（影子 DOM）和HTML templates（HTML 模板）**，详细内容你可以参考 MDN 上的[相关链接](https://developer.mozilla.org/zh-CN/docs/Web/Web_Components)

下面我们就来演示下这 3 个技术是怎么实现数据封装的，如下面代码所示：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <template id="geekbang-t">
        <style>
            p {
                background-color: brown;
                color: cornsilk;
            }
            div {
                width: 200px;
                background-color: bisque;
                border: 3px solid red;
                border-radius: 10px;
            }
        </style>
        <div>
            <p>time.geekbang.org</p>
            <p>time1.geekbang.org</p>
        </div>
        <script>
            function foo() {
                console.log('foo')
            }
        </script>
    </template>

    <script>
        class Geekbang extends HTMLElement {
            constructor() {
                super();
                // 获取组件模板
                const content = document.getElementById('geekbang-t').content
                // 创建影子DOM节点
                const shadowDOM = this.attachShadow({ mode: 'open' })
                // 将模板添加到影子DOM上
                shadowDOM.appendChild(content.cloneNode(true))
            }
        }
        customElements.define('geek-bang', Geekbang)
    </script>

    <geek-bang></geek-bang>
    <div>
        <p>time.geekbang.org</p>
        <p>time1.geekbang.org</p>
    </div>
</body>
</html>
```
详细观察上面这段代码，我们可以得出：要使用 WebComponent，通常要实现下面三个步骤。

**首先，使用 template 属性来创建模板**。利用 DOM 可以查找到模板的内容，但是模板元素是不会被渲染到页面上的，也就是说 DOM 树中的 template 节点不会出现在布局树中，所以我们可以使用 template 来自定义一些基础的元素结构，这些基础的元素结构是可以被重复使用的。一般模板定义好之后，我们还需要在模板的内部定义样式信息。

**其次，我们需要创建一个 GeekBang 的类**。在该类的构造函数中要完成三件事：
- 查找模板内容；
- 创建影子 DOM；
- 再将模板添加到影子 DOM 上。

上面最难理解的是影子 DOM，其实影子 DOM 的作用是将模板中的内容与全局 DOM 和 CSS 进行隔离，这样我们就可以实现元素和样式的私有化了。你可以把影子 DOM 看成是一个作用域，其内部的样式和元素是不会影响到全局的样式和元素的，而在全局环境下，要访问影子 DOM 内部的样式或者元素也是需要通过约定好的接口的。

总之，通过影子 DOM，我们就实现了 CSS 和元素的封装，在创建好封装影子 DOM 的类之后，我们就可以**使用 customElements.define 来自定义元素了**（可参考上述代码定义元素的方式）。

**最后，就很简单了，可以像正常使用 HTML 元素一样使用该元素**，如上述代码中的 `<geek-bang></geek-bang>` ，上述代码最终渲染出来的页面，如下图所示：
![使用影子 DOM 的输出效果](https://static001.geekbang.org/resource/image/57/7c/579c65e2d2221f4e476c7846b842c27c.png)
从图中我们可以看出，影子 DOM 内部的样式是不会影响到全局 CSSOM 的。另外，使用 DOM 接口也是无法直接查询到影子 DOM 内部元素的，比如你可以使用 `document.getElementsByTagName('div')` 来查找所有 div 元素，这时候你会发现影子 DOM 内部的元素都是无法查找的，因为要想查找影子 DOM 内部的元素需要专门的接口，所以通过这种方式又将影子内部的 DOM 和外部的 DOM 进行了隔离。

通过影子 DOM 可以隔离 CSS 和 DOM，不过需要注意一点，影子 DOM 的 JavaScript 脚本是不会被隔离的，比如在影子 DOM 定义的 JavaScript 函数依然可以被外部访问，这是因为 JavaScript 语言本身已经可以很好地实现组件化了。

## 浏览器如何实现影子 DOM
关于 WebComponent 的使用方式我们就介绍到这里。WebComponent 整体知识点不多，内容也不复杂，我认为核心就是影子 DOM。上面我们介绍影子 DOM 的作用主要有以下两点：
1. 影子 DOM 中的元素对于整个网页是不可见的；
2. 影子 DOM 的 CSS 不会影响到整个网页的 CSSOM，影子 DOM 内部的 CSS 只对内部的元素起作用。

那么浏览器是如何实现影子 DOM 的呢？下面我们就来分析下，如下图：
![影子 DOM 示意图](https://static001.geekbang.org/resource/image/5b/22/5bce3d00c8139a7fde9cc90f9d803322.png)

该图是上面那段示例代码对应的 DOM 结构图，从图中可以看出，我们使用了两次 `geek-bang` 属性，那么就会生成两个影子 DOM，并且每个影子 DOM 都有一个 `shadow root` 的根节点，我们可以将要展示的样式或者元素添加到影子 DOM 的根节点上，每个影子 DOM 你都可以看成是一个独立的 DOM，它有自己的样式、自己的属性，内部样式不会影响到外部样式，外部样式也不会影响到内部样式。

浏览器为了实现影子 DOM 的特性，在代码内部做了大量的条件判断，比如当通过 DOM 接口去查找元素时，渲染引擎会去判断 `geek-bang` 属性下面的 `shadow-root` 元素是否是影子 DOM，如果是影子 DOM，那么就直接跳过 `shadow-root` 元素的查询操作。所以这样通过 DOM API 就无法直接查询到影子 DOM 的内部元素了。

另外，当生成布局树的时候，渲染引擎也会判断 `geek-bang` 属性下面的 `shadow-root` 元素是否是影子 DOM，如果是，那么在影子 DOM 内部元素的节点选择 CSS 样式的时候，会直接使用影子 DOM 内部的 CSS 属性。所以这样最终渲染出来的效果就是影子 DOM 内部定义的样式。

*******
本文改编自极客时间《浏览器工作原理与实践》专栏，推荐订阅

<img src="https://i.loli.net/2019/08/19/LRNyftlo5JsMj4p.jpg" alt="本文改编自极客时间《浏览器工作原理与实践》专栏，推荐订阅" style="width:320px" />