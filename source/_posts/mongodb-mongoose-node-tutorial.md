---
title: MongoDB + Mongoose与Node.js结合使用的后端开发的最佳实践
date: 2020-12-07 22:56:59
img: https://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/MongoDB/banner.jpg
categories:
  - 技术
tags:
  - Node.js
  - MongoDB
---

![](https://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/MongoDB/banner.jpg)

**MongoDB 无疑是当今最受欢迎的 NoSQL 数据库选择之一，它有一个很棒的社区和生态系统。**

在本文中，我们将介绍在使用 Node.js 设置 MongoDB 和 Mongoose 时应遵循的一些最佳实践。

<!-- more -->

## 1.为什么需要 Mongoose？

为了理解我们为什么需要 Mongoose，我们先来了解 MongoDB（也是一个数据库）在架构层面的工作原理。

- 你有一个数据库服务器(例如 MongoDB 社区服务器)
- 你正在运行一个 Node.js 脚本（作为一个进程）

MongoDB 服务器在 TCP 套接字上监听（通常），你的 Node.js 进程可以使用 TCP 连接来连接它。

但是在 TCP 之上，MongoDB 也有自己的协议来了解客户端（我们的 Node.js 进程）到底想要数据库做什么。

对于这种通信，我们不需要学习我们在 TCP 层上要发送的消息，而是借助一个“驱动”软件将其抽象掉，在这里称为 MongoDB 驱动。MongoDB 驱动在这里以[npm 包](https://www.npmjs.com/package/mongodb)的形式提供。

现在请记住，MongoDB 驱动程序负责连接和抽象你的低层通信请求/响应——但作为开发者，这只能让你走到这一步。

因为 MongoDB 是一个无模式数据库，它为你提供了比初学者所需的更多的功能。更大的权利意味着更大的出错可能性，你需要减少可在代码中制作的错误和破坏表的可能性。

Mongoose 是对原生 MongoDB 驱动（我上面提到的 npm 包）的一个抽象。

抽象化的一般经验法则（我理解的方式）是，每一个抽象化都会损失一些底层操作能力。但这并不一定意味着它是坏的，有时它能提高生产力 1000 倍以上，因为你根本不需要完全访问底层 API。

一个好的思路是，你在技术上用 C 语言和 Python 创建一个实时聊天应用。作为开发人员，Python 的例子将更容易和更快地实现，生产率更高。C 可能更有效率，但它会在生产力、开发速度、错误、崩溃方面付出巨大的代价。另外，在大多数情况下，你不需要拥有 C 给你的能力来实现 websockets。

同样，使用 Mongoose，你可以限制你的底层 API 访问的范围，但可以释放出很多潜在的收益和良好的 DX。

## 2.如何连接 Mongoose + MongoDB

首先，让我们快速看看在 2020 年应该如何用 Mongoose 连接到你的 MongoDB 数据库。

```javascript
mongoose.connect(DB_CONNECTION_STRING, {
  useNewUrlParser: true,
  useUnifiedTopology: true,
  useCreateIndex: true,
  useFindAndModify: false,
});
```

这种连接格式确保你正在使用 Mongoose 的新 URL 解析器，而且你没有使用任何废弃的做法。

## 3.如何执行 Mongoose 操作

现在我们先来快速讨论一下 Mongoose 的操作，以及你应该如何执行这些操作。

Mongoose 为您提供以下两种选择：

1. 基于游标的(Cursor-based)查询
2. 全程检索(Full fetching)查询

### 3.1 基于游标的(Cursor-based)查询

基于游标的查询是指一次只处理一条记录，同时一次从数据库中获取一条或一批文档。这是一种在有限的内存环境下处理海量数据的有效方式。

想象一下，你要在 1GB/1 核云服务器上解析总大小为 10GB 的文档。你不能获取整个集合，因为那在你的系统中不合适。游标是一个很好的（也是唯一的）选择。

### 3.2 全程检索(Full fetching)查询

这是一种查询类型，你可以一次性获得查询的全部响应。在大多数情况下，这就是你要使用的方法。因此，我们将在这里主要关注这个方法。

## 4.如何使用 Mongoose 模型(Models)

模型是 Mongoose 的超级力量，它们帮助你执行“schema”规则，并为你的 Node 代码提供无缝集成到数据库调用中。

第一步是定义一个好的模型：

```javascript
import mongoose from "mongoose";

const CompletedSchema = new mongoose.Schema(
  {
    type: {
      type: String,
      enum: ["course", "classroom"],
      required: true,
    },
    parentslug: { type: String, required: true },
    slug: { type: String, required: true },
    userid: { type: String, required: true },
  },
  { collection: "completed" }
);

CompletedSchema.index(
  { slug: 1, userid: 1 },
  { unique: true }
);

const model = mongoose.model("Completed", CompletedSchema);
export default model;
```

这是一个直接从 codedamn 的代码库中删减的例子。这里有一些有趣的事情你应该注意。

1. 在所有需要的字段中，尽量保持 `required: true` 。如果你在创建对象时没有使用 TypeScript 这样的静态类型检查系统来协助你正确地检查属性名，那么这可以为你省去很多麻烦。另外，免费的验证也超级酷。

2. 定义索引和唯一字段。 `unique` 属性也可以在模式中添加。索引是一个很广泛的话题，所以我在这里就不深究了。但从大范围来看，它们确实可以帮助你加快很多查询速度。
3. 明确定义一个集合名。虽然 Mongoose 可以根据模型的名称自动给出一个集合名称（例如这里的 `Completed` here），但在我看来这太抽象了。你至少应该知道你的数据库名称和代码库中的集合。
4. 如果可以，请使用枚举来限制值。

## 5.如何执行 CRUD 操作

CRUD 是指创建、读取、更新和删除。这是四个基本选项，有了这四个选项，你就可以在数据库中进行任何形式的数据操作。让我们快速看看这些操作的一些例子。

### 5.1 Create

这简单来说就是在数据库中创建一条新记录。让我们使用我们上面定义的模型来创建一条记录。

```javascript
try {
  const res = await CompletedSchema.create(record);
} catch (error) {
  console.error(error);
  // handle the error
}
```

同样，这里有一些提示：

1. 使用 async-await 而不是回调(看起来不错，但没有突破性的性能优势)
2. 在查询周围使用 try-catch 块，因为你的查询可能会因为一些原因而失败（重复记录、错误的值等）。

## 5.2 Read

这意味着从数据库中读取现有的值。这听起来很简单，但你应该知道 Mongoose 的几个小问题。

```javas
const res = await CompletedSchema.find(info).lean()
```

1. 你能看到那里的 `lean()` 函数调用吗？它对性能超级有用。默认情况下，Mongoose 会处理从数据库中返回的文档，并在其上添加其*神奇*的方法（例如 `.save`）。
2. 当你使用 `.lean()` 时，Mongoose 会返回普通的 JSON 对象，而不是内存和资源沉重的文档。使查询速度更快，对 CPU 的消耗也更小。
3. 然而，如果你确实想要更新数据，你可以省略 `.lean()` (我们接下来会看到)

## 5.3 Update

如果你已经有了一个 Mongoose 文档（没有使用 `.lean()` 触发），你可以简单地去修改对象属性，然后用 `object.save()` 保存它。

```javascript
const doc = await CompletedSchema.findOne(info);
doc.slug = "something-else";
await doc.save();
```

记住，这里有两个数据库调用。第一个是在 `findOne上`，第二个是在 `doc.save` 上。

如果可以，你应该总是减少访问数据库的请求数量(因为如果比较内存、网络和磁盘，网络几乎总是最慢的)。

在另一种情况下，可以使用如下查询：

```javascript
const res = await CompletedSchema.updateOne(<condition>, <query>).lean()
```

并且只会对数据库进行一次调用。

### 5.4 Delete

使用 Mongoose 删除也很简单，让我们看看如何删除单个文档：

```javascript
const res = await CompletedSchema.deleteOne(<condition>)
```

和 `updateOne` 一样 `deleteOne` 也接受第一个参数作为文档的匹配条件。

还有另一个方法叫 `deleteMany`，只有当你知道要删除多个文档时才使用。

在任何其他情况下，总是使用 `deleteOne` 来避免意外的多次删除，特别是当你试图自己执行查询时。

---

原文：[https://blog.zhangbing.site/2020/12/07/mongodb-mongoose-node-tutorial/](https://blog.zhangbing.site/2020/12/07/mongodb-mongoose-node-tutorial/)
来源：[https://www.freecodecamp.org](https://www.freecodecamp.org/news/mongodb-mongoose-node-tutorial/)
