---
title: 实战|从零开始使用JavaScript制作自己的命令行(CLI工具)
date: 2020-05-06 13:30:03
categories:
  - 技术
tags:
  - 前端
  - Node
  - JavaScript
---

我们每天都使用CLI程序（例如Terminal，cmd，Powershell等）进行软件开发。你使用的每个工具本质上都是其他软件工程师的产品，我们也可以制作自己的CLI工具。
<!-- more -->

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202005/7581033c-3e38-4f07-2b0b-bb1fe0b57cf6.jpg)

从零开始的简单CLI，让我们开始吧！

## 首先，让我们制作一个简单的CLI工具，该工具会打印“ HelloWord”

要制作CLI，您需要制作两个文件
- package.json：将设置和配置指定入口
- index.js：根据CLI命令的可执行文件

**添加Package.json 文件**

```json
// package.json
{
  "name": "my-cli",
  "version": "0.0.1",
  "description": "nodejs cli program",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Dunizb",
  "license": "ISC"
}
```

在package.json中，指定有关当前CLI程序的元数据, `name`，`version`，`description`、`author`等。

**创建 index.js 可执行文件**

```javascript
// index.js
#! /usr/bin/env node
console.log('Hello CLI');
```

那么，第一句话是什么意思?

在**Linux**和基于**Unix**的操作系统（例如Mac）中，`＃！ / usr / bin / env node`  不仅仅是一个注释。它使用在 `/usr/bin/env` 中注册的node命令来运行文件。

但是，在**Windows**中，这只是一个注释。

**添加bin属性**

我们添加 `bin` 属性来实际运行 **index.js** 文件

```json
// package.json
{
  "name": "my-cli",
  "version": "0.0.1",
  ...
  ...
  "license": "ISC",
  "bin": {
    "cli": "./index.js"
  }
}
```

`bin` 属性具有可执行文件，`cli` 命令要求运行 `index.js` 文件。

**运行CLI**

最后，让我们运行CLI在控制台上打印**Hello CLI**。通过运行 `npm i -g` 在package.json中安装配置。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/from-zero-terminal/4.png)

> 下次你在控制台上运行 `npm i -g`，您获得了 `updated 1 package...`，而不是 `added 1 package ...`。

然后运行 `cli` 命令。终于， **Hello CLI！**

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/from-zero-terminal/5.png)

你可能需要在Mac和Linux环境中附加 `sudo` 命令（即 `sudo cli`）,没有 `node_modules` 文件夹，因为你没有安装依赖项。

**CLI中的Process.argv**

```javascript
// index.js
#! /usr/bin/env node
console.log('Hello CLI', process.argv);
```

你可以使用 `process.argv` 在命令中找到选项，选项以数组形式显示。

你不需要在每次更新 `index.js` 代码时再次运行 `npm i -g`，因为你已经将 `package.json` 的 `bin` 属性连接到 `cli` 命令和 `index.js` 文件。因此，每次调用 `cli` 命令时，都可以执行 `index.js` 文件（它不是来自缓存的，因此您可以运行新的更新内容）。

在终端中运行命令：

```shell
cli one two three four
```

结果

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/from-zero-terminal/6.png)

数组中的**前两个元素**是 `node` 和 `cli` 命令的路径。（对于Windows系统，它可能会打印出不同的输出）输出可能会因您的计算机设置和环境而异（这取决于您在计算机上安装**node**和**cli**命令的位置）。

此外，`one two three four` 表示为数组类型

## 其次，通过“用户输入”与用户交互的简单CLI工具

使用称为**readline**的本机Node模块从用户那里获取输入。

```javascript
// index.js
#! /usr/bin/env node
const readline = require('readline');

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
});

rl.question("你今天好吗(快乐, 悲伤)？", (answer) => {
  if(answer === "快乐") {
    console.log("听你这么说我很高兴");
  } else if (answer === "悲伤") {
    console.log("希望你明天感觉好些")
  } else {
    console.log("你是快乐还是悲伤？");
  }
	rl.close();
});
```

你可以使用 `readline` 模块中的 `createInterface` 方法创建 `rl` 对象。

`process.stdin` 和 `process.stdout` 是控制台输入和输出流。

`readline` 模块接受来自用户的输入，`rl` 对象提问法是向用户提问的一种方法，回调函数具有一个 `answer` 参数（来自用户的输入），如果所有 I/O（输入和输出）完成，则关闭 `rl` 对象。

**我们是否可以通过再次询问用户在这种情况下是否既不回答“高兴”也不回答“悲伤”来进一步提高CLI ?**

## 再次询问用户时，是否回答错误

```javascript
#! /usr/bin/env node
const readline = require('readline');

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
});

console.clear();

const answerCallback = (answer) => {
  if (answer === "快乐") {
    console.log("听你这么说我很高兴");
    rl.close();
  } else if (answer === "悲伤") {
    console.log("希望你明天感觉好些");
    rl.close();
  } else {
    console.log("你是快乐还是悲伤？");
    rl.question("你今天好吗(快乐，悲伤)？ ", answerCallback);
  }
};

rl.question("你今天好吗(快乐，悲伤)?", answerCallback);
```

当程序开始使用 `console. Clear` 时清除控制台，然后使用 `rl.question` 方法询问用户输入并使用`answerCallback` 函数获得答案。

如果答案既不是悲伤也不是快乐，请清除控制台，然后递归再次提问，如果答案是悲伤或快乐，关闭输入控制台。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/from-zero-terminal/7.gif)

## 总结

在本文中，我们练习了一种制作简单的CLI工具（要求用户输入）的方法。希望你喜欢阅读。

更多的高级和实用的例子可以从[博客](http://www.softxml.com/3012/CLI-program-from-scratch)上找到进阶版CLI。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/from-zero-terminal/8.gif)

