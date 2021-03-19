---
title: 在React应用中使用Dexie.js进行离线数据存储
img: http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202103/dexie-js-indexeddb-react/banner.png
date: 2021-03-16 20:22:11
categories:
  - 翻译
tags:
  - React
summary: "了解 IndexedDB 的全部含义，以及如何使用 Dexie.js（用于 IndexedDB 的简约包装）处理 Web 应用程序中的离线数据存储"
---

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202103/dexie-js-indexeddb-react/banner.png)

> 翻译自[blog.logrocket.com](https://blog.logrocket.com/dexie-js-indexeddb-react-apps-offline-data-storage/)，作者：Ebenezer Don

离线存储应用程序数据已成为现代 Web 开发中的必要条件。内置的浏览器 `localStorage` 可以用作简单轻量数据的数据存储，但是在结构化数据或存储大量数据方面却不足。

最重要的是，我们只能将字符串数据存储在受 XSS 攻击的 `localStorage` 中，并且它没有提供很多查询数据的功能。

这就是 IndexedDB 的亮点。使用 IndexedDB，我们可以在浏览器中创建结构化的数据库，将几乎所有内容存储在这些数据库中，并对数据执行各种类型的查询。

在本文中，我们将了解 IndexedDB 的全部含义，以及如何使用 Dexie.js（用于 IndexedDB 的简约包装）处理 Web 应用程序中的离线数据存储。

## IndexedDB 如何工作

IndexedDB 是用于浏览器的内置非关系数据库。它使开发人员能够将数据持久存储在浏览器中，即使在脱机时也可以无缝使用 Web 应用程序。使用 IndexedDB 时，您会经常看到两个术语：数据库存储和对象存储。让我们在下面进行探讨。

### 使用 IndexedDB 创建数据库

IndexedDB 数据库对每个 Web 应用程序来说都是唯一的。这意味着一个应用程序只能从与自己运行在同一域或子域的 IndexedDB 数据库中访问数据。数据库是容纳对象存储的地方，而对象存储又包含存储的数据。要使用 IndexedDB 数据库，我们需要打开（或连接到）它们：

```js
const initializeDb = indexedDB.open(
  "name_of_database",
  version
);
```

`indexedDb.open()` 方法中的 `name_of_database` 参数将用作正在创建的数据库的名称，而 `version` 参数是一个代表数据库版本的数字。

在 IndexedDB 中，我们使用对象存储来构建数据库的结构，并且每当要更新数据库结构时，都需要将版本升级到更高的值。这意味着，如果我们从版本 1 开始，则下次要更新数据库的结构时，我们需要将 `indexedDb.open()` 方法中的版本更改为 2 或更高版本。

### 使用 IndexedDB 创建对象存储

对象存储类似于关系数据库（如 PostgreSQL）中的表和文档数据库（如 MongoDB）中的集合。要在 IndexedDB 中创建对象存储，我们需要从之前声明的 `initializeDb` 变量中调用 `onupgradeneeded()` 方法：

```js
initializeDb.onupgradeneeded = () => {
  const database = initializeDb.result;
  database.createObjectStore("name_of_object_store", {
    autoIncrement: true,
  });
};
```

在上面的代码块中，我们从 `initializeDb.result` 属性获取数据库，然后使用其 `createObjectStore()` 方法创建对象存储。第二个参数 `{autoIncrement：true}` 告诉 IndexedDB 自动提供/增加对象存储中项目的 ID。

我省略了诸如事务和游标之类的其他术语，因为使用低级 IndexedDB API 需要进行大量工作。这就是为什么我们需要 Dexie.js，它是 IndexedDB 的简约包装。让我们看看 Dexie 如何简化创建数据库，对象存储，存储数据以及从数据库查询数据的整个过程。

## 使用 Dexie 离线存储数据

使用 Dexie，创建 IndexedDB 数据库和对象存储非常容易：

```js
const db = new Dexie("exampleDatabase");
db.version(1).stores({
  name_of_object_store: "++id, name, price",
  name_of_another_object_store: "++id, title",
});
```

在上面的代码块中，我们创建了一个名为 `exampleDatabase` 的新数据库，并将其作为值分配给 `db` 变量。我们使用 `db.version(version_number).stores()` 方法为数据库创建对象存储。每个对象存储的值代表了它的结构。例如，当在第一个对象存储中存储数据时，我们需要提供一个具有属性 `name` 和 `price` 的对象。`++id` 选项的作用就像我们在创建对象存储区时使用的 `{autoIncrement：true}` 参数一样。

请注意，在我们的应用程序中使用 dexie 包之前，我们需要安装并导入它。当我们开始构建我们的演示项目时，我们将看到如何做到这一点。

## 我们将要建立的

对于我们的演示项目，我们将使用 Dexie.js 和 React 构建一个市场列表应用程序。我们的用户将能够在市场列表中添加他们打算购买的商品，删除这些商品或将其标记为已购买。

我们将看到如何使用 Dexie `useLiveQuery` hook 来监视 IndexedDB 数据库中的更改以及在数据库更新时重新呈现 React 组件。这是我们的应用程序的外观：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202103/dexie-js-indexeddb-react/1.gif)

### 设置我们的应用

首先，我们将使用为应用程序的结构和设计创建的 GitHub 模板。这里有一个[模板的链接](https://github.com/ebenezerdon/market-list-template)。点击**Use this template（使用此模板）**按钮，就会用现有的模板为你创建一个新的资源库，然后你就可以克隆和使用这个模板。

或者，在计算机上安装了[GitHub CLI](https://github.com/cli/cli)的情况下，您可以运行以下命令从市场列表 GitHub 模板创建名为 `market-list-app` 的存储库：

```shell
gh repo create market-list-app --template ebenezerdon/market-list-template
```

完成此操作后，您可以继续在代码编辑器中克隆并打开您的新应用程序。使用终端在应用程序目录中运行以下命令应安装 npm 依赖项并启动新应用程序：

```shell
npm install && npm start
```

导航到成功消息中的本地 URL（通常为http://localhost:3000）时，您应该能够看到新的React应用程序。您的新应用应如下所示：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202103/dexie-js-indexeddb-react/2.png)

当您打开 `./src/App.js` 文件时，您会注意到我们的应用程序组件仅包含市场列表应用程序的 JSX 代码。我们正在使用[Materialize 框架](https://blog.logrocket.com/bootstrap-materialize-tailwind-css-which-is-best/)中的类进行样式设置，并将其 CDN 链接包含在 `./public/index.html` 文件中。接下来，我们将看到如何使用 Dexie 创建和管理数据。

## 用 Dexie 创建我们的数据库

要在我们的 React 应用程序中使用 Dexie.js 进行离线存储，我们将从在终端中运行以下命令开始，以安装 `dexie` 和 `dexie-react-hooks` 软件包：

```shell
npm i -s dexie dexie-react-hooks
```

我们将使用 `dexie-react-hooks` 包中的 `useLiveQuery` hook 来监视更改，并在对 IndexedDB 数据库进行更新时重新渲染我们的 React 组件。

让我们将以下导入语句添加到我们的 `./src/App.js` 文件中。这将导入 `Dexie` 和 `useLiveQuery` hook：

```js
import Dexie from "dexie";
import { useLiveQuery } from "dexie-react-hooks";
```

接下来，我们将创建一个名为 `MarketList` 的新数据库，然后声明我们的对象存储 `items`：

```js
const db = new Dexie("MarketList");
db.version(1).stores({
  items: "++id,name,price,itemHasBeenPurchased",
});
```

我们的 `items` 对象存储将期待一个具有属性`name`、`price`和 `itemHasBeenPurchased` 的对象，而 `id` 将由 Dexie 提供。在将新数据添加到对象存储中时，我们将为 `itemHasBeenPurchased` 属性使用默认布尔值 `false`，然后在我们从市场清单中购买商品时将其更新为 `true`。

## Dexie React hook

让我们创建一个变量来存储我们所有的项目。我们将使用 `useLiveQuery` 钩子从 `items` 对象存储中获取数据，并观察其中的变化，这样当 `items` 对象存储有更新时，我们的 `allItems` 变量将被更新，我们的组件将用新的数据重新渲染。我们将在 `App` 组件内部进行：

```js
const App = () => {
  const allItems = useLiveQuery(() => db.items.toArray(), []);
  if (!allItems) return null
  ...
}
```

在上面的代码块中，我们创建了一个名为 `allItems` 的变量，并将 `useLiveQuery` 钩子作为其值。`useLiveQuery` 钩子的语法类似于 React 的 useEffect 钩子，它期望一个函数及其依赖项数组作为参数。我们的函数参数返回数据库查询。

在这里，我们以数组格式获取 `items` 对象存储中的所有数据。在下一行中，我们使用一个条件来告诉我们的组件，如果 `allItems` 变量是 undefined，则意味着查询仍在加载中。

## 将 items 添加到我们的数据库

仍在 App 组件中，让我们创建一个名为 `addItemToDb` 的函数，我们将使用该函数向数据库中添加项目。每当我们点击“**ADD ITEM(添加项目)**”按钮时，我们都会调用此函数。请记住，每次更新数据库时，我们的组件都会重新渲染。

```js
...
const addItemToDb = async event => {
    event.preventDefault()
    const name = document.querySelector('.item-name').value
    const price = document.querySelector('.item-price').value
    await db.items.add({
      name,
      price: Number(price),
      itemHasBeenPurchased: false
    })
  }
...
```

在 `addItemToDb` 函数中，我们从表单输入字段中获取商品名称和价格值，然后使用 `db.[name_of_object_store].add` 方法将新商品数据添加到商品对象存储中。我们还将 `itemHasBeenPurchased` 属性的默认值设置为 `false`。

## 从我们的数据库中删除 items

现在我们有了 `addItemToDb` 函数，让我们创建一个名为 `removeItemFromDb` 的函数以从我们的商品对象存储中删除数据：

```js
...
const removeItemFromDb = async id => {
  await db.items.delete(id)
}
...
```

## 更新我们数据库中的项目

接下来，我们将创建一个名为 `markAsPurchased` 的函数，用于将商品标记为已购买。我们的函数在调用时，会将物品的主键作为第一个参数——在本例中是 `id`，它将使用这个主键来查询我们想要标记为购买的物品的数据库。取得商品后，它将其 `markAsPurchased` 属性更新为 `true`：

```js
...
const markAsPurchased = async (id, event) => {
  if (event.target.checked) {
    await db.items.update(id, {itemHasBeenPurchased: true})
  }
  else {
    await db.items.update(id, {itemHasBeenPurchased: false})
  }
}
...
```

在 `markAsPurchased` 函数中，我们使用 `event` 参数来获取用户单击的特定输入元素。如果选中其值，我们将`itemHasBeenPurchased` 属性更新为 `true`，否则更新为 `false`。`db.[name_of_object_store] .update()` 方法期望该项目的主键作为其第一个参数，而新对象数据作为其第二个参数。

下面是我们的 `App` 组件在这个阶段应该是什么样子。

```js
...
const App = () => {
  const allItems = useLiveQuery(() => db.items.toArray(), []);
  if (!allItems) return null

  const addItemToDb = async event => {
    event.preventDefault()
    const name = document.querySelector('.item-name').value
    const price = document.querySelector('.item-price').value
    await db.items.add({ name, price, itemHasBeenPurchased: false })
  }

  const removeItemFromDb = async id => {
    await db.items.delete(id)
  }

  const markAsPurchased = async (id, event) => {
    if (event.target.checked) {
      await db.items.update(id, {itemHasBeenPurchased: true})
    }
    else {
      await db.items.update(id, {itemHasBeenPurchased: false})
    }
  }
  ...
}
```

现在，我们创建一个名为 `itemData` 的变量，以容纳我们所有商品数据的 JSX 代码：

```js
...
const itemData = allItems.map(({ id, name, price, itemHasBeenPurchased }) => (
  <div className="row" key={id}>
    <p className="col s5">
      <label>
        <input
          type="checkbox"
          checked={itemHasBeenPurchased}
          onChange={event => markAsPurchased(id, event)}
        />
        <span className="black-text">{name}</span>
      </label>
    </p>
    <p className="col s5">${price}</p>
    <i onClick={() => removeItemFromDb(id)} className="col s2 material-icons delete-button">
      delete
    </i>
  </div>
))
...
```

在 `itemData` 变量中，我们映射了 `allItems` 数据数组中的所有项目，然后从每个 `item` 对象获取属性 `id`、`name`、`price` 和 `itemHasBeenPurchased`。然后，我们继续进行操作，并用数据库中的新动态值替换了以前的静态数据。

注意，我们还使用了 `markAsPurchased` 和 `removeItemFromDb` 方法作为相应按钮的单击事件侦听器。我们将在下一个代码块中将 `addItemToDb` 方法添加到表单的 `onSubmit` 事件中。

准备好 `itemData` 后，让我们将 `App` 组件的 return 语句更新为以下 JSX 代码：

```js
...
return (
  <div className="container">
    <h3 className="green-text center-align">Market List App</h3>
    <form className="add-item-form" onSubmit={event => addItemToDb(event)} >
      <input type="text" className="item-name" placeholder="Name of item" required/>
      <input type="number" step=".01" className="item-price" placeholder="Price in USD" required/>
      <button type="submit" className="waves-effect waves-light btn right">Add item</button>
    </form>
    {allItems.length > 0 &&
      <div className="card white darken-1">
        <div className="card-content">
          <form action="#">
            { itemData }
          </form>
        </div>
      </div>
    }
  </div>
)
...
```

在 return 语句中，我们已将 `itemData` 变量添加到我们的项目列表中(items list)。我们还使用 `addItemToDb` 方法作为 `add-item-form` 的 `onsubmit` 值。

为了测试我们的应用程序，我们可以返回到我们先前打开的 React 网页。请记住，您的 React 应用必须正在运行，如果不是，请在终端上运行命令 `npm start`。您的应用应该能够像下面的演示一样运行：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202103/dexie-js-indexeddb-react/3.gif)

我们还可以使用条件用 Dexie 查询我们的 IndexedDB 数据库。例如，如果我们要获取价格高于 10 美元的所有商品，则可以执行以下操作：

```js
const items = await db.friends
  .where("price")
  .above(10)
  .toArray();
```

您可以在[Dexie 文档](<https://dexie.org/docs/Table/Table.where()>)中查看其他查询方法。

## 结束语和源码

在本文中，我们学习了如何使用 IndexedDB 进行离线存储以及 Dexie.js 如何简化该过程。我们还了解了如何使用 Dexie `useLiveQuery` 钩子来监视更改并在每次更新数据库时重新渲染 React 组件。

由于 IndexedDB 是浏览器原生的，从数据库中查询和检索数据比每次需要在应用中处理数据时都要发送服务器端 API 请求要快得多，而且我们几乎可以在 IndexedDB 数据库中存储任何东西。

过去使用 IndexedDB 可能对浏览器的支持是一个大问题，但是现在所有主流浏览器都支持它。在 Web 应用中使用 IndexedDB 进行离线存储的诸多优势大于劣势，将 Dexie.js 与 IndexedDB 一起使用，使得 Web 开发变得前所未有的有趣。

这是我们的演示应用程序的[GitHub](https://github.com/ebenezerdon/market-list-app)库的链接。
