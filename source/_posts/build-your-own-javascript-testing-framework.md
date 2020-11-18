---
title: 译|通过构建自己的JavaScript测试框架来了解JS测试
date: 2020-09-01 10:49:08
categories:
  - 技术
tags:
  - JavaScript
  - 翻译
  - 实战
---

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202009/build-your-own-javascript-testing-framework/banner.jpeg)

测试（单元或集成）是编程中非常重要的一部分。在当今的软件开发中，单元/功能测试已成为软件开发的组成部分。随着 Nodejs 的出现，我们已经看到了许多超级 JS 测试框架的发布：Jasmine，Jest 等。

<!-- more -->

## 单元测试框架

这有时也称为隔离测试，它是测试独立的小段代码的实践。如果你的测试使用某些外部资源（例如网络或数据库），则不是单元测试。

单元测试框架试图以人类可读的格式描述测试，以便非技术人员可以理解所测试的内容。然而，即使你是技术人员，BDD 格式的阅读测试也会使你更容易理解所发生的事情。

例如，如果我们要测试此功能：

```javascript
function helloWorld() {
  return "Hello world!";
}
```

我们会像这样写一个 jasmine 测试规范：

```javascript
describe('Hello world', () => { ①
  it('says hello', () => { ②
  	expect(helloWorld())③.toEqual('Hello world!'); ④
  });
});
```

说明：

- ① `describe(string, function)` 函数定义了我们所谓的测试套件，它是各个测试规范的集合。
- ② `it(string, function)` 函数定义了一个单独的测试规范，其中包含一个或多个测试期望。
- ③ 预计(实际)表达式就是我们所说的一个期望。它与匹配器一起描述应用程序中预期的行为片段。
- ④ matcher（预期）表达式就是我们所说的 Matcher。如果传入的期望值与传递给 Expect 函数的实际值不符，则将布尔值与规范进行布尔比较。

## 安装和拆卸

有时候为了测试一个功能，我们需要进行一些设置，也许是创建一些测试对象。另外，完成测试后，我们可能需要执行一些清理活动，也许我们需要从硬盘驱动器中删除一些文件。

这些活动称为“设置和拆卸”（用于清理），Jasmine 有一些功能可用来简化此工作：

- `beforeAll` 这个函数在 describe 测试套件中的所有规范运行之前被调用一次。
- `afterAll` 在测试套件中的所有规范完成后，该函数将被调用一次。
- `beforeEach` 这个函数在每个测试规范之前被调用，`it` 函数已经运行。
- `afterEach` 在运行每个测试规范之后调用此函数。

## 在 Node 中的使用

在 Node 项目中，我们在与 `src` 文件夹相同目录的 `test` 文件夹中定义单元测试文件：

```
node_prj
    src/
        one.js
        two.js
    test/
        one.spec.js
        two.spec.js
    package.json
```

该测试包含规格文件，这些规格文件是 src 文件夹中文件的单元测试，`package.json` 在 `script` 部分进行了 `test`。

```json
{
  ...,
  "script": {
  	"test": "jest" // or "jasmine"
	}
}
```

如果 `npm run test` 在命令行上运行，则 jest 测试框架将运行 `test` 文件夹中的所有规范文件，并在命令行上显示结果。

现在，我们知道了期望和构建的内容，我们继续创建自己的测试框架。我们的这个框架将基于 Node，也就是说，它将在 Node 上运行测试，稍后将添加对浏览器的支持。

我们的测试框架将包含一个 CLI 部分，该部分将从命令行运行。第二部分将是测试框架的源代码，它将位于 lib 文件夹中，这是框架的核心。

首先，我们首先创建一个 Node 项目。

```bash
mkdir kwuo
cd kwuo
npm init -y
```

安装 chalk 依赖项，我们将需要它来为测试结果上色：`npm i chalk`。

创建一个 lib 文件夹，其中将存放我们的文件。

```bash
mkdir lib
```

我们创建一个 bin 文件夹是因为我们的框架将用作 Node CLI 工具。

```bash
mkdir bin
```

首先创建 CLI 文件。

在 bin 文件夹中创建 kwuo 文件，并添加以下内容：

```bash
#!/usr/bin/env node

process.title = 'kwuo'
require('../lib/cli/cli')
```

我们将 hashbang 设置为指向 /usr/bin/env node，这样就可以在不使用 node 命令的情况下运行该文件。

我们将 process 的标题设置为“kwuo”，并要求文件“lib/cli/cli”，这样就会调用文件 cli.js，从而启动整个测试过程。

现在，我们创建“lib/cli/cli.js”并填充它。

```bash
mkdir lib/cli
touch lib/cli/cli.js
```

该文件将搜索测试文件夹，在“test”文件夹中获取所有测试文件，然后运行测试文件。

在实现“lib/cli/cli.js”之前，我们需要设置全局变量。

测试文件中使用了 describe，beforeEach，beforeEach，afterAll，beforeAll 函数：

```javascript
describe("Hello world", () => {
  it("says hello", () => {
    expect(helloWorld()).toEqual("Hello world!");
  });
});
```

但是在测试文件中都没有定义。没有 ReferenceError 的情况下文件和函数如何运行？因为测试框架在运行测试文件之前，会先实现这些函数，并将其设置为 globals，所以测试文件调用测试框架已经设置好的函数不会出错。而且，这使测试框架能够收集测试结果并显示失败或通过的结果。

让我们在 lib 文件夹中创建一个 `index.js` 文件：

```bash
touch lib/index.js
```

在这里，我们将设置全局变量并实现`describe`，`it`，`expectEach`，`beforeEach`，`afterAll`，`beforeAll` 函数。

```javascript
// lib/index.js

const chalk = require("chalk");
const log = console.log;
var beforeEachs = [];
var afterEachs = [];
var afterAlls = [];
var beforeAlls = [];
var Totaltests = 0;
var passedTests = 0;
var failedTests = 0;
var stats = [];
var currDesc = {
  it: [],
};

var currIt = {};

function beforeEach(fn) {
  beforeEachs.push(fn);
}

function afterEach(fn) {
  afterEachs.push(fn);
}

function beforeAll(fn) {
  beforeAlls.push(fn);
}

function afterAll(fn) {
  afterAlls.push(fn);
}

function expect(value) {
  return {
    // Match or Asserts that expected and actual objects are same.
    toBe: function(expected) {
      if (value === expected) {
        currIt.expects.push({
          name: `expect ${value} toBe ${expected}`,
          status: true,
        });
        passedTests++;
      } else {
        currIt.expects.push({
          name: `expect ${value} toBe ${expected}`,
          status: false,
        });
        failedTests++;
      }
    },

    // Match the expected and actual result of the test.
    toEqual: function(expected) {
      if (value == expected) {
        currIt.expects.push({
          name: `expect ${value} toEqual ${expected}`,
          status: true,
        });
        passedTests++;
      } else {
        currIt.expects.push({
          name: `expect ${value} toEqual ${expected}`,
          status: false,
        });
        failedTests++;
      }
    },
  };
}

function it(desc, fn) {
  Totaltests++;
  if (beforeEachs) {
    for (var index = 0; index < beforeEachs.length; index++) {
      beforeEachs[index].apply(this);
    }
  }
  //var f = stats[stats.length - 1]
  currIt = {
    name: desc,
    expects: [],
  };
  //f.push(desc)
  fn.apply(this);
  for (var index = 0; index < afterEachs.length; index++) {
    afterEachs[index].apply(this);
  }
  currDesc.it.push(currIt);
}

function describe(desc, fn) {
  currDesc = {
    it: [],
  };
  for (var index = 0; index < beforeAlls.length; index++) {
    beforeAlls[index].apply(this);
  }
  currDesc.name = desc;
  fn.apply(this);
  for (var index = 0; index < afterAlls.length; index++) {
    afterAlls[index].apply(this);
  }
  stats.push(currDesc);
}

exports.showTestsResults = function showTestsResults() {
  console.log(`Total Test: ${Totaltests}    
Test Suites: passed, total
Tests: ${passedTests} passed, ${Totaltests} total
`);
  const logTitle = failedTests > 0 ? chalk.bgRed : chalk.bgGreen;
  log(logTitle("Test Suites"));
  for (var index = 0; index < stats.length; index++) {
    var e = stats[index];
    const descName = e.name;
    const its = e.it;
    log(descName);
    for (var i = 0; i < its.length; i++) {
      var _e = its[i];
      log(`   ${_e.name}`);
      for (var ii = 0; ii < _e.expects.length; ii++) {
        const expect = _e.expects[ii];
        log(
          `      ${
            expect.status === true ? chalk.green("√") : chalk.red("X")
          } ${expect.name}`
        );
      }
    }
    log();
  }
};

global.describe = describe;
global.it = it;
global.expect = expect;
global.afterEach = afterEach;
global.beforeEach = beforeEach;
global.beforeAll = beforeAll;
global.afterAll = afterAll;
```

在开始的时候，我们需要使用 chalk 库，因为我们要用它来把失败的测试写成红色，把通过的测试写成绿色。我们将 console.log 缩短为 log。

接下来，我们设置 beforeEachs，afterEachs，afterAlls，beforeAlls 的数组。beforeEachs 将保存在它所附加的 `it` 函数开始时调用的函数；afterEachs 将在它所附加的 `it` 函数的末尾调用；beforeEachs 和 afterEachs 分别在 `describe` 函数的开始和结尾处调用。

我们设置了 `Totaltests` 来保存运行的测试数量，`passTests` 保存已通过的测试数，`failedTests` 保存失败的测试数。

`stats` 收集每个 describe 函数的 stats，`curDesc` 指定当前运行的 describe 函数来帮助收集测试数据，`currIt` 保留当前正在执行的 `it` 函数，以帮助收集测试数据。

我们设置了 beforeEach、afterEach、beforeAll 和 afterAll 函数，它们将函数参数推入相应的数组，afterAll 推入 afterAlls 数组，beforeEach 推入 beforeEachs 数组，等等。

接下来是 expect 函数，此函数进行测试：

```javascript
expect(56).toBe(56); // 经过测试56预期会是56
expect(func()).toEqual("nnamdi"); // 该函数将返回一个等于“nnamdi”的字符串
```

`expect` 函数接受一个要测试的参数，并返回一个包含匹配器函数的对象。在这里，它返回一个具有 `toBe` 和 `toEqual` 函数的对象，它们具有期望参数，用于与 expect 函数提供的 value 参数匹配。`toBe` 使用 `===` 将 value 参数与期望参数匹配，`toEqual` 使用 `==` 测试期望值。如果测试通过或失败，则这些函数将递增 `passedTests` 和 `failedTests` 变量，并且还将统计信息记录在 currIt 变量中。

我们目前只有两个 matcher 函数，还有很多：

- toThrow
- toBeNull
- toBeFalsy
- etc

你可以搜索它们并实现它们。

接下来，我们有 `it` 函数，`desc` 参数保存测试的描述名称，而 `fn` 保存函数。它先对 beforeEachs 进行 fun，设置统计，调用 `fn` 函数，再调用 afterEachs。

`describe` 函数的作用和 `it` 一样，但在开始和结束时调用 `beforeAlls` 和 `afterAlls`。

`showTestsResults` 函数通过 `stats` 数组进行解析，并在终端上打印通过和失败的测试。

我们实现了这里的所有函数，并将它们都设置为全局对象，这样才使得测试文件调用它们时不会出错。

回到“lib/cli/cli.js”：

```javascript
// lib/cli/cli.js
const path = require("path");
const fs = require("fs");
const { showTestsResults } = require("./../");
```

首先，它从“lib/index”导入函数 `showTestsResult`，该函数将在终端显示运行测试文件的结果。另外，导入此文件将设置全局变量。

让我们继续：

`run` 函数是这里的主要函数，这里调用它，可以引导整个过程。它搜索 `test` 文件夹 `searchTestFolder`，然后在数组`getTestFiles` 中获取测试文件，它循环遍历测试文件数组并运行它们 `runTestFiles`。

- `searchTestFolder`：使用 `fs＃existSync` 方法检查项目中是否存在“test/”文件夹。
- `getTestFiles`：此函数使用 `fs#readdirSync` 方法读取“test”文件夹的内容并返回它们。
- `runTestFiles`：它接受数组中的文件，使用 `forEach` 方法循环遍历它们，并使用 `require` 方法运行每个文件。

kwuo 文件夹结构如下所示：

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202009/build-your-own-javascript-testing-framework/1.png)

## 测试我们的框架

我们已经完成了我们的测试框架，让我们通过一个真实的 Node 项目对其进行测试。

我们创建一个 Node 项目：

```shell
mkdir examples
mkdir examples/math
cd examples/math
npm init -y
```

创建一个 src 文件夹并添加 add.js 和 sub.js

```shell
mkdir src
touch src/add.js src/sub.js
```

add.js 和 sub.js 将包含以下内容：

```javascript
// src/add.js
function add(a, b) {
  return a + b;
}

module.exports = add;

// src/sub.js
function sub(a, b) {
  return a - b;
}

module.exports = sub;
```

我们创建一个测试文件夹和测试文件：

```javascript
mkdir test
touch test/add.spec.js test/sub.spec.js
```

规范文件将分别测试 add.js 和 sub.js 中的 add 和 sub 函数

```javascript
// test/sub.spec.js
const sub = require("../src/sub");
describe("Subtract numbers", () => {
  it("should subtract 1 from 2", () => {
    expect(sub(2, 1)).toEqual(1);
  });

  it("should subtract 2 from 3", () => {
    expect(sub(3, 2)).toEqual(1);
  });
});

// test/add.spec.js
const add = require("../src/add");
describe("Add numbers", () => {
  it("should add 1 to 2", () => {
    expect(add(1, 2)).toEqual(3);
  });

  it("should add 2 to 3", () => {
    expect(add(2, 3)).toEqual(5);
  });
});
describe("Concat Strings", () => {
  let expected;
  beforeEach(() => {
    expected = "Hello";
  });

  afterEach(() => {
    expected = "";
  });

  it("add Hello + World", () => {
    expect(add("Hello", "World")).toEqual(expected);
  });
});
```

现在，我们将在 package.json 的“script”部分中运行“test”以运行我们的测试框架：

```json
{
  "name": "math",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "kwuo"
  },
  "keywords": [],
  "author": "Chidume Nnamdi <kurtwanger40@gmail.com>",
  "license": "ISC"
}
```

我们在命令行上运行 `npm run test`，结果将是这样的：

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202009/build-your-own-javascript-testing-framework/2.png)

看，它给我们展示了统计数据，通过测试的总数，以及带有“失败”或“通过”标记的测试套件列表。看到通过的测试期望“add Hello + World”，它将返回“HelloWorld”，但我们期望返回“Hello”。如果我们纠正它并重新运行测试，所有测试都将通过。

```javascript
// test/add.spec.js
...
describe('Concat Strings', () => {
  let expected;
  beforeEach(() => {
    expected = "Hello";
  });

  afterEach(() => {
    expected = "";
  });

  it('add Hello + World', () => {
    expect(add("Hello", ""))
      .toEqual(expected);
  });
});
```

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202009/build-your-own-javascript-testing-framework/3.png)

看，我们的测试框架像 Jest 和 Jasmine 一样工作。它仅在 Node 上运行，在下一篇文章中，我们将使其在浏览器上运行。

## 代码在 Github 上

Github 仓库地址：[philipszdavido/kwuoKwuo](https://github.com/philipszdavido/kwuo)

你可以使用来自 NPM 的框架：

```shell
cd IN_YOUR_NODE_PROJECT
npm install kwuo -D
```

将 package.json 中的“test”更改为此：

```json
{
  ...
  "scripts": {
    "test": "kwuo"
    ...
  }
}
```

## 总结

我们建立了我们的测试框架，在这个过程中，我们学会了如何使用全局来设置函数和属性在运行时任何地方可见。

我们看到了如何在项目中使用 `describe`、`it`、`expect` 和各种匹配函数来运行测试。下一次，你使用 Jest 或 Jasmine，你会更有信心，因为现在你知道它们是如何工作的。

---

> 来源：[https://blog.bitsrc.io](https://blog.bitsrc.io/build-your-own-javascript-testing-framework-377e6583c870)  
> 作者：Chidume Nnamdi 🔥💻🎵🎮  
> 翻译：公众号《前端全栈开发者》
