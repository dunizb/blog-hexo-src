---
title: 使用React Hook创建一个可排序表格组件
date: 2020-03-23 00:36:55
img: https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/React-Hook-create-sortable/1.gif
categories:
  - 前端框架&工具
tags:
  - React
---

**我花了一些精力来创作本文，以及熬夜编写本文的示例程序，以便您能在阅读之后可以实践参考，阅读后如果觉得对您有帮助，可以关注作者、收藏和点赞本文，这是对作者写出优质文章最大的鼓励了。**

在本文中，我将创建一种**可重用**的方法来对 React 中的表格数据进行排序功能，并且使用**React Hook**的方式编写。我将详细介绍每个步骤，并在此过程中学习一系列有用的技术，如 `useState`、`useMemo`、`自定义Hook` 的使用。

<!-- more -->

本文不会介绍基本的 React 或 JavaScript 语法，但你不必是 React 方面的专家也能跟上，最终我们的效果如下。

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/React-Hook-create-sortable/1.gif)

## 第一步，用 React 创建表格

首先，让我们创建一个表格组件，它将接受一个产品（product）数组，并输出一个非常基本的表，每个产品列出一行。

```javascript
function ProductTable(props) {
  const { products } = props;
  return (
    <table>
      <caption>Our products</caption>
      <thead>
        <tr>
          <th>名称</th>
          <th>价格</th>
          <th>库存数量</th>
        </tr>
      </thead>
      <tbody>
        {products.map((product) => (
          <tr key={product.id}>
            <td>{product.name}</td>
            <td>{product.price}</td>
            <td>{product.stock}</td>
          </tr>
        ))}
      </tbody>
    </table>
  );
}
```

在这里，我们接受一个产品数组，并将它们循环到表中，它是静态的。

## 第二步，对数据进行排序

得益于内置的数组函数 `sort()`， JavaScript 中的数据排序非常简单。它将对数字和字符串数组进行排序，而无需额外的参数：

```javascript
const array = ["mozzarella", "gouda", "cheddar"];
array.sort();
console.log(array); // ['cheddar', 'gouda', 'mozzarella']
```

首先，按照名称的字母顺序对数据进行排序。

```javascript
function ProductTable(props) {
  const { products } = props;
  let sortedProducts = [...products];
  sortedProducts.sort((a, b) => {
    if (a.name < b.name) {
      return -1;
    }
    if (a.name > b.name) {
      return 1;
    }
    return 0;
  });
  return <Table>{/* as before */}</Table>;
}
```

这里首先创建了一个 products 的副本，我们可以根据需要更改和更改。我们需要这样做，因为 `Array.prototype.sort` 函数会更改原始数组，而不是返回新的排序后的副本。

接下来，我们调用 `sortedProducts.sort`，并将其传递给排序函数。我们检查第一个参数 `a` 的 `name` 属性是否在第二个参数`b` 之前，如果是，则返回负值，这表示列表中 `a` 应该在 `b` 之前。如果第一个参数的名称在第二个参数的名称之后，我们将返回一个正数，表示应将 `b` 放在 `a` 之前。如果两者相等（即名称相同），我们将返回 `0` 以保留顺序。

## 第三步，使我们的表格可排序

所以现在我们可以确保表是按名称排序的——但是我们如何改变排序顺序呢？要更改排序依据的字段，我们需要记住当前排序的字段。我们将使用 **useState Hook**。

一开始我们什么都不排序。接下来，让我们更改表标题，以包含一种方法来更改我们想要排序的字段。

```javascript
import React, { useState } from "react";
const ProductsTable = (props) => {
  const { products } = props;
  const [sortedField, setSortedField] = useState(null);
  return (
    <table>
      <thead>
        <tr>
          <th>
            <button
              type='button'
              onClick={() => setSortedField("name")}
            >
              名称
            </button>
          </th>
          <th>
            <button
              type='button'
              onClick={() => setSortedField("price")}
            >
              价格
            </button>
          </th>
          <th>
            <button
              type='button'
              onClick={() => setSortedField("stock")}
            >
              库存数量
            </button>
          </th>
        </tr>
      </thead>
      {/* 像之前一样 */}
    </table>
  );
};
```

现在，每当我们单击一个表标题时，我们都会更新要排序的字段。

我们还没有做任何实际的排序，我们继续。还记得之前的排序算法吗？这里只是稍微修改了一下，以便与我们的字段名一起使用。

```javascript
import React, { useState } from "react";
const ProductsTable = (props) => {
  const { products } = props;
  const [sortedField, setSortedField] = useState(null);
  let sortedProducts = [...products];
  if (sortedField !== null) {
    sortedProducts.sort((a, b) => {
      if (a[sortedField] < b[sortedField]) {
        return -1;
      }
      if (a[sortedField] > b[sortedField]) {
        return 1;
      }
      return 0;
    });
  }
  return (
    <table>
```

首先，我们要确定我们选择了一个字段来排序，之后我们将根据该字段对产品排序。

## 第四步，升序和降序操作

我们要看到的下一个功能，是一种在升序和降序之间切换的方法，通过再次单击表的标题项在升序和降序之间切换。

为此，我们需要引入第二种状态：排序顺序。我们将重构当前的 `sortedField` 状态变量，以保留字段名及其排序方向。该状态变量将不包含字符串，而是包含一个带有键（字段名称）和排序方向的对象。我们将其重命名为 `sortConfig`，以使其更加清晰。

这是新的排序函数：

```javascript
sortedProducts.sort((a, b) => {
  if (a[sortConfig.key] < b[sortConfig.key]) {
    return sortConfig.direction === "ascending" ? -1 : 1;
  }
  if (a[sortConfig.key] > b[sortConfig.key]) {
    return sortConfig.direction === "ascending" ? 1 : -1;
  }
  return 0;
});
```

现在，如果方向是“ascending(升序)”，我们将像以前一样进行。如果不是，我们将采取相反的操作，以降序排列。

接下来，我们将创建一个新函数 `requestSort`，它将接受字段名称并相应地更新状态。

```javascript
const requestSort = (key) => {
  let direction = "ascending";
  if (
    sortConfig.key === key &&
    sortConfig.direction === "ascending"
  ) {
    direction = "descending";
  }
  setSortConfig({ key, direction });
};
```

我们还必须更改我们的点击事件处理函数才能使用此新功能！

```javascript
return (
  <table>
    <thead>
      <tr>
        <th>
          <button
            type='button'
            onClick={() => requestSort("name")}
          >
            Name
          </button>
        </th>
        <th>
          <button
            type='button'
            onClick={() => requestSort("price")}
          >
            Price
          </button>
        </th>
        <th>
          <button
            type='button'
            onClick={() => requestSort("stock")}
          >
            In Stock
          </button>
        </th>
      </tr>
    </thead>
    {/* 像之前一样 */}
  </table>
);
```

现在我们看起来功能已经很完整了，但是还有一件重要的事情要做。我们需要确保只在需要时才对数据进行排序。目前，我们正在对每个渲染中的所有数据进行排序，这将导致各种各样的性能问题。相反，让我们使用内置的 `useMemo` Hook 来记忆会导致缓慢的部分！

```javascript
import React, { useState, useMemo } from "react";
const ProductsTable = (props) => {
  const { products } = props;
  const [sortConfig, setSortConfig] = useState(null);

  useMemo(() => {
    let sortedProducts = [...products];
    if (sortedField !== null) {
      sortedProducts.sort((a, b) => {
        if (a[sortConfig.key] < b[sortConfig.key]) {
          return sortConfig.direction === 'ascending' ? -1 : 1;
        }
        if (a[sortConfig.key] > b[sortConfig.key]) {
          return sortConfig.direction === 'ascending' ? 1 : -1;
        }
        return 0;
      });
    }
    return sortedProducts;
  }, [products, sortConfig]);
```

`useMemo` 是一种缓存或记忆昂贵计算的方法。给定相同的输入，如果我们出于某种原因重新渲染组件，它不必对产品进行两次排序。请注意，每当我们的产品发生变化，或者根据变化对字段或排序方向进行排序时，我们都希望触发一个新的排序。

在这个函数中包装我们的代码将对我们的表排序产生巨大的性能影响！

## 优化，让代码可复用

对于 hooks 最好的作用就是使代码复用变得很容易，React 具有称为自定义 Hook 的功能。它们听起来很花哨，但它们都是常规函数，在其中使用了其他 Hook。让我们将代码重构为包含在自定义 Hook 中，这样我们就可以到处使用它了！

```javascript
import React, { useState, useMemo } from "react";

const useSortableData = (items, config = null) => {
  const [sortConfig, setSortConfig] = useState(config);

  const sortedItems = useMemo(() => {
    let sortableItems = [...items];
    if (sortConfig !== null) {
      sortableItems.sort((a, b) => {
        if (a[sortConfig.key] < b[sortConfig.key]) {
          return sortConfig.direction === "ascending"
            ? -1
            : 1;
        }
        if (a[sortConfig.key] > b[sortConfig.key]) {
          return sortConfig.direction === "ascending"
            ? 1
            : -1;
        }
        return 0;
      });
    }
    return sortableItems;
  }, [items, sortConfig]);

  const requestSort = (key) => {
    let direction = "ascending";
    if (
      sortConfig.key === key &&
      sortConfig.direction === "ascending"
    ) {
      direction = "descending";
    }
    setSortConfig({ key, direction });
  };

  return { items, requestSort };
};
```

这几乎是我们先前代码的复制和粘贴，并引入了一些重命名。`useSortableData` 接受 items 和一个可选的初始排序状态。它返回一个带有已排序 items 的对象和一个用于重新排序 items 的函数。

我们的表代码现在看起来像这样：

```javascript
const ProductsTable = (props) => {
  const { products } = props;
  const { items, requestSort } = useSortableData(products);
  return <table>{/* ... */}</table>;
};
```

## 最后一点

缺少一小部分，一种指示表格如何排序的方法。为了表明这一点，在我们的设计中，我们还需要返回内部状态 `sortConfig`。让我们返回它，并使用它来生成样式以应用到我们的表格标题！

```javascript
const ProductTable = (props) => {
  const {
    items,
    requestSort,
    sortConfig,
  } = useSortableData(props.products);
  const getClassNamesFor = (name) => {
    if (!sortConfig) {
      return;
    }
    return sortConfig.key === name
      ? sortConfig.direction
      : undefined;
  };
  return (
    <table>
      <caption>Products</caption>
      <thead>
        <tr>
          <th>
            <button
              type='button'
              onClick={() => requestSort("name")}
              className={getClassNamesFor("name")}
            >
              Name
            </button>
          </th>
          {/* … */}
        </tr>
      </thead>
      {/* … */}
    </table>
  );
};
```

感谢您的阅读。获取本文源码和在线体验，请点击[这里](https://coding.zhangbing.site)。

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/React-Hook-create-sortable/2.png)
