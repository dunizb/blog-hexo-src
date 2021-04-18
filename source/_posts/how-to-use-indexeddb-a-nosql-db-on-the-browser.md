---
title: 如何使用IndexedDB —浏览器上的NoSQL数据库
date: 2021-02-19 16:35:24
img: http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202102/indexeddb/banner.jpeg
categories:
  - Web技术
tags:
  - indexedDB
summary: "深入研究 IndexedDB API 及其在实践中的用法。"
---

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202102/indexeddb/banner.jpeg)

**深入研究 IndexedDB API 及其在实践中的用法。**

你是否听说过浏览器上的 NoSQL 数据库？

> IndexedDB 是大型 NoSQL 存储系统。它使你几乎可以将任何内容存储在用户的浏览器中。除了通常的搜索，获取和放置操作外，IndexedDB 还支持事务。

你可以在下面找到 IndexedDB 的示例。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202102/indexeddb/1.png)

在本文中，我们将重点介绍以下内容。

- 为什么我们需要 IndexedDB？
- 我们如何在我们的应用程序中使用 IndexedDB？
- IndexedDB 的功能
- IndexedDB 的局限性
- IndexedDB 是否适合你的应用程序？

## 为什么我们需要 IndexedDB？

**IndexedDB 被认为比 localStorage 更强大！**

你知道背后的原因吗？让我们找出答案。

**可以存储比 localStorage 大得多的数据量**

没有像 `localStorage` 这样的特殊限制（介于 2.5MB 和 10MB 之间）。最大限制取决于浏览器和磁盘空间。例如，基于 Chrome 和 Chromium 的浏览器最多允许 80％的磁盘空间。如果你有 100GB，则 IndexedDB 最多可以使用 80GB 的空间，单个 origin 最多可以使用 60GB。Firefox 允许每个 origin 最多 2GB，而 Safari 允许每个来源最多 1GB。

**可以存储基于{ key: value }对的任何类型的值**

存储不同数据类型的灵活性更高。这不仅意味着字符串，而且还意味着二进制数据（ArrayBuffer 对象，Blob 对象等）。它使用对象存储在内部保存数据。

**提供查找界面**

这在其他浏览器存储选项（例如 `localStorage` 和 `sessionStorage`）中不可用。

**对于不需要持续互联网连接的 Web 应用程序很有用**

IndexedDB 对于联机和脱机工作的应用程序都非常有用，例如，它可以用于渐进式 Web 应用程序（PWA）中的客户端存储。

**应用状态可以存储**

通过为经常使用的用户存储应用程序状态，可以大大提高应用程序的性能。稍后，应用程序可以与后端服务器同步，并通过延迟加载来更新应用程序。

我们来看看 IndexedDB 的结构，它可以存储多个数据库。

## IndexedDB 的结构

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202102/indexeddb/2.png)

## 我们如何在我们的应用程序中使用 IndexedDB？

在以下部分中，我们将研究如何使用 IndexedDB 引导应用程序。

### 1. 使用“window.indexedDB”打开数据库连接

```js
const openingRequest = indexedDB.open("UserDB", 1);
```

在这里 `UserDB` 是数据库名称，`1` 是数据库的版本。这将返回一个对象，该对象是 `IDBOpenDBRequest` 接口的实例。

### 2. 创建对象存储

打开数据库连接后，将触发 `onupgradeneeded` 事件，可用于创建对象存储。

```js
// 创建UserDetails对象存储库和索引
request.onupgradeneeded = (event) => {
  let db = event.target.result;

  // 创建UserDetails对象存储
  // 具有自动递增ID
  let store = db.createObjectStore("UserDetails", {
    autoIncrement: true,
  });

  // 在NIC属性上创建索引
  let index = store.createIndex("nic", "nic", {
    unique: true,
  });
};
```

### 3. 将数据插入对象存储

一旦打开了与数据库的连接，就可以在 `onsuccess` 事件处理程序中管理数据。插入数据发生在 4 个步骤中。

```js
function insertUser(db, user) {
  // 创建一个新事物
  const txn = db.transaction("User", "readwrite");

  // 获取UserDetails对象存储
  const store = txn.objectStore("UserDetails");
  // 插入新记录
  let query = store.put(user);

  // 处理成功用例
  query.onsuccess = function(event) {
    console.log(event);
  };

  // 处理失败用例
  query.onerror = function(event) {
    console.log(event.target.errorCode);
  };

  // 事务完成后关闭数据库
  txn.oncomplete = function() {
    db.close();
  };
}
```

一旦创建了插入函数，请求的 `onsuccess` 事件处理程序就可以用来插入更多的记录。

```js
request.onsuccess = (event) => {
  const db = event.target.result;
  insertUser(db, {
    email: "john.doe@outlook.com",
    firstName: "John",
    lastName: "Doe",
  });
  insertUser(db, {
    email: "ann.doe@gmail.com",
    firstName: "Ann",
    lastName: "Doe",
  });
};
```

在 IndexedDB 上可以执行许多操作，其中一些如下：

- 通过 key 从对象存储中读取/搜索数据
- 按 index 从对象存储中读取/搜索数据
- 更新记录数据
- 删除记录
- 从数据库的先前版本等进行迁移

## IndexedDB 的功能

IndexedDB 提供了许多特殊的功能，这是其他浏览器存储无法实现的，下面简要说明一些功能。

**具有异步 API**

这使执行昂贵的操作而不会阻塞 UI 线程，并为用户提供了更好的体验。

**支持事务以确保可靠性**

如果一个步骤失败，则事务将被取消，数据库将回滚到先前的状态。

**支持版本控制**

你可以在创建数据库时对其进行版本控制，并在需要时对其进行升级。在 IndexedDB 中也可以从旧版本迁移到新版本。

**私有域**

数据库是一个域的私有数据库，因此任何其他网站都不能访问其他网站的 IndexedDB 存储。这也就是所谓的**同源策略**。

## IndexedDB 的局限性

到目前为止，IndexedDB 似乎很有希望用于客户端存储。然而，有一些值得注意的限制。

- 即使有现代浏览器的支持，但 IE 等浏览器并没有完全支持。
  ![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202102/indexeddb/3.png)
- Firefox 在私人浏览模式下完全禁用 IndexedDB - 这可能导致你的应用程序在通过隐身窗口访问时发生故障。

## IndexedDB 是否适合你的应用程序？

基于 IndexedDB 提供的许多功能，这个百万美元问题的答案可能是 Yes！然而，在下结论之前，请问自己以下问题。

- 你的应用程序需要脱机访问吗？
- 你是否需要在客户端存储大量数据？
- 你是否需要快速查找/搜索大量数据中的数据？
- 你的应用程序是否使用 IndexedDB 支持的浏览器访问客户端存储？
- 你是否需要存储各种类型的数据，包括 JavaScript 对象？
- 从客户端存储进行写入/读取是否需要非阻塞？

如果对上述所有问题的回答均为“是”，则 IndexedDB 是你的最佳选择。但如果不需要这样的功能，你不妨选择像 `localStorage` 这样的存储方法，因为它提供了广泛的浏览器应用，并且具有易于使用的 API。

## 总结

当我们考虑所有的客户端存储机制时，IndexedDB 是一个明显的赢家。我们来看看不同客户端存储方式的总结比较。

|              | Cookies                        | Web Storage                | IndexedDB                                  |
| ------------ | ------------------------------ | -------------------------- | ------------------------------------------ |
| 存储         | 具有键值、数据值对的小型查询表 | 只有字符<br />串键、值存储 | 对象存储，可以存储任何类型的数据，包括对象 |
| 容量         | 4KB                            | 5MB-25MB                   | 50MB 以上                                  |
| 索引         | 不支持                         | 不支持                     | 支持                                       |
| API 调用类型 | 同步                           | 同步                       | 异步                                       |
| 操作性能     | 直接执行                       | 直接执行                   | 事务                                       |
| 学习曲线     | 低                             | 低                         | 高                                         |

希望你对 IndexedDB 及其好处有一个清晰的认识。

---

翻译自：[bitsrc.io](https://blog.bitsrc.io/how-to-use-indexeddb-a-nosql-db-on-the-browser-f845da3caf35)，作者：Viduni Wickramarachchi
