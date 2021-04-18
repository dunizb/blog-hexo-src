---
title: 【译】编写高质量JavaScript模块的4个最佳实践
date: 2020-03-04 13:56:11
categories:
  - JavaScript
---

使用 ES2015 模块，您可以将应用程序代码分成可重用的、封装的、专注于单一任务的模块。

这很好，但是如何构造模块呢?一个模块应该有多少个函数和类?

这篇文章介绍了有关如何更好地组织 JavaScript 模块的 4 种最佳实践。

<!-- more -->

## 1.优先使用命名导出

当我开始使用 JavaScript 模块时，我使用默认的语法来导出模块定义的单个块，不管是类还是函数。

例如，这是一个将模块 `Greeter` 导出为默认值的模块程序：

```js
// greeter.js
export default class Greeter {
  constructor(name) {
    this.name = name;
  }

  greet() {
    return `Hello, ${this.name}!`;
  }
}
```

随着时间的推移，我注意到了重构默认导出的类(或函数)的困难。在重命名原始类时，使用者模块中的类名没有改变。

更糟糕的是，编辑器没有提供有关要导入的类名的自动完成建议。

我的结论是，默认的导出并没有带来明显的好处。然后我转向了**命名导出**。

让我们将 `Greeter` 命名为出口，然后看看好处：

```js
// greeter.js
export class Greeter {
  constructor(name) {
    this.name = name;
  }

  greet() {
    return `Hello, ${this.name}!`;
  }
}
```

使用命名导出，编辑器可以更好地进行重命名：每次更改原始类名时，所有使用者模块也会更改类名。

自动完成功能还会建议导入的类：
![JavaScript Named Import Autocomplete](https://dmitripavlutin.com/static/50f5c31a9862daaaae1478c3875cd12c/ff59c/autocomplete-4.png)
所以，这是我的建议：

> "支持命名模块导出，以受益于重命名重构和代码自动完成功能。"

注意：使用 React，Lodash 等第三方模块时，默认导入通常是可以的。默认的导入名称是一个不变的常量：`React`，`_`。

## 2.导入期间不进行繁重的计算工作

模块级别范围定义了函数、类、对象和变量。该模块可以导出其中一些组件。就这样。

```js
// Module-level scope

export function myFunction() {
  // myFunction Scope
}
```

模块级范围不应该进行繁重的计算，比如解析 JSON、发出 HTTP 请求、读取本地存储等等。

例如，下面的模块配置解析来自全局变量`bigJsonString`的配置：

```js
// configuration.js
export const configuration = {
  // Bad
  data: JSON.parse(bigJsonString),
};
```

这是一个问题，因为`bigJsonString`的解析是在模块级范围内完成的。`bigJsonString`的解析实际上是在导入配置模块时发生的：

```js
// Bad: 导入模块时进行解析
import { configuration } from "configuration";

export function AboutUs() {
  return <p>{configuration.data.siteName}</p>;
}
```

在更高的级别上，模块级范围的作用是定义模块组件、导入依赖项和导出公共组件：这是依赖项解析过程。它应该与运行时分离：解析 JSON、发出请求、处理事件。

让我们重构配置模块来执行延迟解析：

```js
// configuration.js
let parsedData = null;

export const configuration = {
  // Good
  get data() {
    if (parsedData === null) {
      parsedData = JSON.parse(bigJsonString);
    }
    return parsedData;
  },
};
```

因为`data`属性被定义为一个`getter`，所以只有在使用者访问`configuration.data`时才解析`bigJsonString`。

```js
// Good: 导入模块时不进行JSON解析
import { configuration } from "configuration";

export function AboutUs() {
  // 调用时才进行JSON解析
  return <p>{configuration.data.companyDescription}</p>;
}
```

消费者更清楚什么时候进行大的操作，使用者可能决定在浏览器空闲时执行该操作。或者，使用者可能会导入模块，但是出于某种原因不使用它。

这为更深层的性能优化提供了机会：减少交互时间，最大程度地减少主线程工作。

> 导入时，模块不应该执行任何繁重的工作。相反，使用者应该决定何时执行运行时操作。

## 3.尽可能的使用高内聚模块

**内聚性**描述了模块内部各个组件在一起的程度。

高内聚模块的函数、类或变量是密切相关的。他们专注于单个任务。

`formatDate`模块具有很高的内聚性，因为它的功能密切相关，并且侧重于日期格式化:

```js
// formatDate.js
const MONTHS = [
  'January', 'February', 'March','April', 'May',
  'June', 'July', 'August', 'September', 'October',
  'November', 'December'
];

function ensureDateInstance(date) {
  if (typeof date === 'string') {
    return new Date(date);
  }
  return date;
}

export function formatDate(date) {
  date = ensureDateInstance(date);
  const monthName = MONTHS[date.getMonth())];
  return `${monthName} ${date.getDate()}, ${date.getFullYear()}`;
}
```

`formatDate()`，`ensureDateInstance()`和`MONTHS`彼此密切相关。

删除`MONTHS`或`ensureDateInstance()`会破坏`formatDate()`：这是高内聚的标志。

## 4.避免较长的相对路径

我发现很难理解一个模块的路径包含一个，甚至更多的父文件夹：

```js
import { compareDates } from "../../date/compare";
import { formatDate } from "../../date/format";

// Use compareDates and formatDate
```

而有一个父选择器`../`通常不是问题，拥有 2 个或更多通常很难掌握。

这就是为什么我建议避免使用父文件夹，而使用绝对路径:

```js
import { compareDates } from "utils/date/compare";
import { formatDate } from "utils/date/format";

// Use compareDates and formatDate
```

尽管有时写入绝对路径的时间更长，但是使用绝对路径可以使导入的模块的位置清晰明了。

为了减少冗长的绝对路径，可以引入新的根目录。例如，这可以使用[babel-plugin-module-resolver](https://github.com/tleunen/babel-plugin-module-resolver#readme)实现。

> 使用绝对路径而不是较长的相对路径。

## 5.结论

JavaScript 模块非常适合将您的应用程序逻辑拆分为多个独立的小块。

通过使用命名的导出而不是默认的导出，可以在导入命名组件时更轻松地重命名重构和编辑器自动完成帮助。

使用 `import {myFunc} from 'myModule'` 的唯一目的就是导入 myFunc 组件，仅此而已。`myModule`的模块级范围应该只定义包含少量内容的类、函数或变量。

一个组件应该有多少个函数或类，这些组件应该如何与每个组件相关联？支持高内聚的模块：它的组件应该紧密相关并执行一个共同的任务。

包含许多父文件夹`../`的长相对路径很难理解。将它们重构为绝对路径。

**你使用哪些 JavaScript 模块最佳做法？**

---

原文：https://dmitripavlutin.com/javascript-modules-best-practices/  
作者：Dmitri Pavlutin  
译者：做工程师不做码农

全文完。
