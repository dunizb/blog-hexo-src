---
title: 如何在JavaScript中实现队列数据结构
date: 2021-03-28 21:17:25
categories:
  - 翻译
tags:
  - JavaScript
  - 数据结构
summary: "在这篇文章中，我将描述队列数据结构，其具有的操作以及向您展示 JavaScript 中的队列实现"
---

要成为一名优秀的开发人员，需要来自多个学科的知识。

然而，在了解编程语言的基础上，你还必须了解如何组织数据，以便根据任务轻松有效地操作数据。这就是数据结构的作用。

在这篇文章中，我将描述队列数据结构，其具有的操作以及向您展示 JavaScript 中的队列实现。

## 1.队列数据结构

如果你喜欢旅行（像我一样），很可能你在机场通过了办理登机手续。如果有很多旅客愿意办理登机手续，自然就会在值机柜台前排起长龙。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202103/javascript-queue/1.webp)

刚进入机场并想要办理登机手续的旅客将排队进入队列，而刚刚在服务台办理了登机手续的旅客则可以离开队列。

这是队列的真实示例—队列数据结构以相同的方式工作。

队列是一种“先入先出”（FIFO）数据结构的类型。入队（输入）的第一项是要出队（输出）的第一项。

从结构上说，一个队列有 2 个指针。队列中最早的排队项目位于队列的顶部，而最新队列的项目位于队列的末尾。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202103/javascript-queue/2.svg)

## 2.队列中的操作

队列主要支持两种操作：入队列（enqueue）和出队列（dequeue）。此外，您可能会发现使用 peek 和 length 操作非常有用。

### 2.1 入队操作

入队操作在队列尾部插入一个项目。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202103/javascript-queue/3.svg)

上图中的入队操作将项目 `8` 插入尾部，`8` 成为队列的尾部。

```js
queue.enqueue(8);
```

### 2.2 出队操作

出队操作提取队列头部的项，队列中的下一项成为头。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202103/javascript-queue/4.svg)

在上面的图片中，出队操作从队列中返回并删除项目 `7`，在退出队列后，项目 `2` 成为新的头。

```js
queue.dequeue(); // => 7
```

### 2.3 Peek 操作

Peek 操作读取队列的开头，而不会更改队列。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202103/javascript-queue/5.svg)

项目 `7` 是上图中队列的头部，Peek 操作只是返回队列的头部——第 `7` 项，而不修改队列。

```js
queue.peek(); // => 7
```

### 2.4 队列长度

长度操作计算队列包含多少个项目。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202103/javascript-queue/6.svg)

图片中的队列有 4 个项目：`4`、`6`、`2` 和 `7`。因此，队列长度为 `4`。

```js
queue.length; // => 4
```

### 2.5 队列操作时间复杂度

关于所有的队列操作--enqueue、dequeue、peek 和 length——重要的是，所有这些操作必须在恒定的时间内 `O(1)` 执行。

恒定的时间 `O(1)` 意味着无论队列的大小(它可以有 10 个或 100 万个项目)：enqueue、dequeue、peek 和 length 操作必须在相对相同的时间内执行。

## 3.在 JavaScript 中实现队列

让我们看一下队列数据结构的可能实现，同时维持所有操作必须在恒定时间 `O(1)` 中执行的要求。

```js
class Queue {
  constructor() {
    this.items = {};
    this.headIndex = 0;
    this.tailIndex = 0;
  }

  enqueue(item) {
    this.items[this.tailIndex] = item;
    this.tailIndex++;
  }

  dequeue() {
    const item = this.items[this.headIndex];
    delete this.items[this.headIndex];
    this.headIndex++;
    return item;
  }

  peek() {
    return this.items[this.headIndex];
  }

  get length() {
    return this.tailIndex - this.headIndex;
  }
}

const queue = new Queue();

queue.enqueue(7);
queue.enqueue(2);
queue.enqueue(6);
queue.enqueue(4);

queue.dequeue(); // => 7

queue.peek(); // => 2

queue.length; // => 3
```

[Try the demo.](https://jsfiddle.net/dmitri_pavlutin/g6pd4hqb/2/)

`const queue = new Queue()` 是创建队列实例的方式。

调用 `queue.enqueue(7)` 方法会将项目 7 排队到队列中。

`queue.dequeue()` 从队列中去队列一个头部的项目，而 `queue.peek()` 只是 Peek 头部的项目。

最后，`queue.length` 显示队列中还有多少项目。

### 队列方法的复杂性

Queue 类的 `queue()`、`dequeue()`、`peek()` 和 `length()` 方法仅使用：

- 属性访问器（例如 `this.items[this.headIndex]` ），
- 或执行算术操作（例如 `this.headIndex++` ）

因此，这些方法的时间复杂度是恒定时间 `O(1)`。

## 总结

队列数据结构是“先入先出”（FIFO）的一种：最早入队的项是最早出队的项。

队列有 2 个主要操作：入队和出队。另外，队列可以具有辅助操作，例如 Peek 和长度。

所有队列操作必须在恒定时间 `O(1)` 中执行。

---

本文翻译自[dmitripavlutin.com](https://dmitripavlutin.com/javascript-queue/)，作者：Dmitri Pavlutin
