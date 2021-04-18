---
title: 【译】改善React应用性能的5个建议
date: 2020-03-21 13:38:40
img: https://bs-uploads.toptal.io/blackfish-uploads/blog/post/seo/og_image_file/og_image/16097/react-context-api-4929b3703a1a7082d99b53eb1bbfc31f.png
categories:
  - 前端框架&工具
tags:
  - React
---

> 你的 React 应用是否感到有些迟缓？你是否害怕在 Chrome DevTools 中打开 “paint flash”？试试这 5 个性能技巧吧!

本文包含有关 React 开发的 5 条性能建议。

<!-- more -->

![](https://bs-uploads.toptal.io/blackfish-uploads/blog/post/seo/og_image_file/og_image/16097/react-context-api-4929b3703a1a7082d99b53eb1bbfc31f.png)

## 1.使用 memo 和 PureComponent

考虑下面这个简单的 React 应用程序，您是否认为当 `props.propA` 更改值时 `<ComponentB>` 会重新渲染？

```javascript
import React from "react";

const MyApp = (props) => {
  return (
    <div>
      <ComponentA propA={props.propA} />
      <ComponentB propB={props.propB} />
    </div>
  );
};

const ComponentA = (props) => {
  return <div>{props.propA}</div>;
};

const ComponentB = (props) => {
  return <div>{props.propB}</div>;
};
```

答案是肯定的！这是因为 `MyApp` 实际上是重新计算运行（或者重新渲染 😏）了，而 `<ComponentB>` 也在里面。所以即使它自己的 props 没有改变，它的父组件也会导致它重新渲染。

这个概念也适用于基于类的 React 组件的渲染方式。

React 的作者意识到这并不是一个理想的结果，在重新渲染前简单地比较新旧 props 可以获得一些简单的性能提升…这就是 [React.memo](https://alligator.io/react/learning-react-memo/) 和 [React.PureComponent](https://alligator.io/react/performance-boost-purecomponent/) 的设计初衷！

### memo

让我们将 `memo` 与 function 组件一起使用（我们将在之后研究基于 Class 的组件）：

```javascript
import React, { memo } from "react";

// 🙅‍♀️
const ComponentB = (props) => {
  return <div>{props.propB}</div>;
};

// 🙆‍♂️
const ComponentB = memo((props) => {
  return <div>{props.propB}</div>;
});
```

就这样！您只需要用 `memo()` 函数包装 `<ComponentB>`。现在，仅在 `propB` 实际更改值时才重新渲染，而不管其父级重新渲染多少次！

### PureComponent

让我们看看 `PureComponent`。它本质上等同于 `memo`，但它是针对基于 Class 的组件的。

```javascript
import React, { Component, PureComponent } from "react";

// 🙅‍♀️
class ComponentB extends Component {
  render() {
    return <div>{this.props.propB}</div>;
  }
}

// 🙆‍♂️
class ComponentB extends PureComponent {
  render() {
    return <div>{this.props.propB}</div>;
  }
}
```

这些性能提升几乎太容易了！您可能想知道为什么 React 组件不会自动包含这些内部保护措施以防止过度渲染。

实际上，`memo` 和 `PureComponent` 有一个隐藏的代价，由于这些义务比较新旧 props，这实际上可能导致其自身的性能瓶颈，例如，如果您的 props 非常大，或者您将 React 组件作为 props 传递，那么比较新旧 props 的成本代价可能很高。

在编程的世界里，银弹是罕见的！且 `memo/PureComponent` 也不例外。您肯定会想以一种审慎周到的方式驾驭它们。在某些情况下，它们可以让您惊讶地发现它们可以节省多少计算时间。

> 对于 React Hooks，可以使用 `useMemo` 作为类似的方法来防止不必要的计算工作

## 2.避免匿名函数

组件主体内部的函数通常是事件处理程序或回调。在许多情况下，您可能会为它们使用匿名函数：

```javascript
import React from "react";

function Foo() {
  return (
    <button onClick={() => console.log("boop")}>
      {" "}
      // 🙅‍♀️ BOOP
    </button>
  );
}
```

由于没有为匿名函数分配标识符（通过 const/let/var），因此每当不可避免地再次渲染此功能组件时，它们就不会被持久化（persistent）。这会导致 JavaScript 在每次重新渲染此组件时重新分配新的内存，而不是在使用“命名函数”时分配的内存：

```javascript
import React, { useCallback } from "react";

// 变体1：在组件外部命名和放置处理程序
const handleClick = () => console.log("boop");
function Foo() {
  return <button onClick={handleClick}> // 🙆‍♂️ BOOP</button>;
}

// 变体2: "useCallback"
function Foo() {
  const handleClick = useCallback(
    () => console.log("boop"),
    []
  );
  return <button onClick={handleClick}> // 🙆‍♂️ BOOP</button>;
}
```

`useCallback` 是另一种避免匿名函数缺陷的方法，但是它也有类似的折衷权衡，这与我们前面介绍的`React.memo` 一样。

使用基于 class 的组件，解决方案非常简单，并且没有任何缺点，这是在 React 中定义处理程序的推荐方法：

```javascript
import React from "react";

class Foo extends Component {
  render() {
    return (
      <button onClick={() => console.log("boop")}>
        {" "}
        {/* 🙅‍♀️ */}
        BOOP
      </button>
    );
  }
}

class Foo extends Component {
  render() {
    return (
      <button onClick={this.handleClick}>
        {" "}
        {/* 🙆‍♂️ */}
        BOOP
      </button>
    );
  }
  handleClick = () => {
    // 这个匿名函数很好用
    console.log("boop");
  };
}
```

## 3.避免对象字面量

此性能提示与上一节有关匿名函数的部分相似。对象字面量没有持久的存储空间，因此只要组件重新渲染，您的组件就需要在内存中分配新的位置：

```javascript
function ComponentA() {
  return (
    <div>
      HELLO WORLD
      <ComponentB style={{  {/* 🙅‍♀️ */}
        color: 'blue',
        background: 'gold'
      }}/>
    </div>
  );
}

function ComponentB(props) {
  return (
    <div style={this.props.style}>
      TOP OF THE MORNING TO YA
    </div>
  )
}
```

每次重新渲染 `<ComponentA>` 时，都必须在内存中“创建”新的对象常量。此外，这还意味着 `<ComponentB>` 实际上正在接收其他样式对象。使用 `memo` 和 `PureComponent` 甚至都无法阻止在此重新渲染 😭。

本技巧不仅适用于样式 props ，而且通常是在 React 组件中不经意使用对象字面量的地方。

可以通过命名对象（当然在组件主体之外！）来轻松解决此问题：

```javascript
const myStyle = {
  // 🙆‍♂️
  color: "blue",
  background: "gold",
};
function ComponentA() {
  return (
    <div>
      HELLO WORLD
      <ComponentB style={myStyle} />
    </div>
  );
}

function ComponentB(props) {
  return (
    <div style={this.props.style}>
      TOP OF THE MORNING TO YA
    </div>
  );
}
```

## 4. 使用 React.lazyand 和 React.Suspense

让 React 应用程序快速运行的一部分可以通过代码拆分来完成。此功能是通过 React v16 引入的[ `React.lazy` 和`React.Suspense`](https://reactjs.org/docs/code-splitting.html#reactlazy)实现。

如果您不知道，代码分割的概念是将 JavaScript 客户端源代码（例如，React 应用程序代码）分成更小的块，并且只以一种惰性的方式加载这些块，如果没有任何代码拆分，单个包可能非常大：

```
- bundle.js (10MB!)
```

使用代码分割，能对 bundle 的初始网络请求大大减少：

```
- bundle-1.js (5MB)
- bundle-2.js (3MB)
- bundle-3.js (2MB)
```

最初的网络请求将“仅”需要下载 5MB，并且可以开始向最终用户显示一些有趣的内容，想象一下一个博客网站，最初只需要页眉和页脚。加载后，它将开始请求包含实际博客文章的第二个 bundle。这是一个简单的示例，可以方便地进行代码分割。 👏👏👏

### 如何在 React 中完成代码分割?

如果您使用的是 `create-react-app`，则已经对其进行了代码拆分配置，因此您可以轻松使用 `React.lazy` 和`React.Suspense` ！如果您是自己配置 Webpack，则应如下所示。

```js
module.exports = {
  entry: {
    main: "./src/app.js",
  },
  output: {
    // `filename` provides a template for naming your bundles (remember to use `[name]`)
    filename: "[name].bundle.js",
    // `chunkFilename` provides a template for naming code-split bundles (optional)
    chunkFilename: "[name].bundle.js",
    // `path` is the folder where Webpack will place your bundles
    path: "./dist",
    // `publicPath` is where Webpack will load your bundles from (optional)
    publicPath: "dist/",
  },
};
```

下面是一个实现 lazy 和 Suspense 的简单示例：

```javascript
import React, { lazy, Suspense } from 'react';
import Header from './Header';
import Footer from './Footer';
const BlogPosts = React.lazy(() => import('./BlogPosts'));

function MyBlog() {
  return (
    <div>
      <Header>
      <Suspense fallback={<div>Loading...</div>}>
        <BlogPosts />
      </Suspense>
      <Footer>
    </div>
  );
}
```

注意 `fallback` prop，加载第二个 bundle 程序块（例如，`<BlogPosts>`）时，将立即向用户显示此内容。

## 5.避免频繁的 Mounting/Unmounting

很多时候，我们习惯于使用三元语句(或类似的语句)使组件消失：

```javascript
import React, { Component } from 'react';
import DropdownItems from './DropdownItems';

class Dropdown extends Component {
  state = {
    isOpen: false
  }
  render() {
    <a onClick={this.toggleDropdown}>
      Our Products
      {
        this.state.isOpen
          ? <DropdownItems>
          : null
      }
    </a>
  }
  toggleDropdown = () => {
    this.setState({isOpen: !this.state.isOpen})
  }
}
```

由于 `<DropdownItems>` 已从 DOM 中删除，因此可能导致浏览器重绘/重排。这些可能很昂贵，尤其是如果它导致其他 HTML 元素移动时。

为了减轻这种情况，建议避免完全卸载组件。相反，您可以使用某些策略，例如将 CSS 不透明度设置为零，或将 CSS 可见性设置为“null”。这将使组件保留在 DOM 中，同时使其有效地消失而不会产生任何性能代价。

---

原文：https://alligator.io/react/keep-react-fast/

作者：[William Le](https://alligator.io/author/william-le)

译者：[Dunizb](https://zhangbing.site)
