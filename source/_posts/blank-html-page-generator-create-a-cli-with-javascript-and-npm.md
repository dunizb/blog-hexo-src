---
title: HTML页面生成器：使用JavaScript和Node创建CLI
date: 2020-05-06 13:27:06
img: https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202005/d5c5446dc2f813cd69bece27345d4ee8.jpg
categories:
  - 技术
tags:
  - 前端
  - Node
  - JavaScript
  - 实战
---

在上一篇文章：[【实战】从零开始使用 JavaScript 制作自己的命令行(CLI 工具)](https://blog.zhangbing.site/2020/05/05/make-your-own-terminal-cli-tool-with-javascript/) 中我介绍了如何从零开始制作 CLI，那么现在我们更进一步。在这篇文章中，我们将构建一个简单的 CLI，允许用户生成 HTML 页面。我们首先要生成一个标准的空白页面，然后让用户输入参数，比如文件名和标题，先通过选项，然后通过提示问题让用户输入参数。

<!-- more -->

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202005/d5c5446dc2f813cd69bece27345d4ee8.jpg)

## 创建 Hello World CLI

创建用于编写 CLI 的文件夹。我将其命名为 **html-generator-cli**。打开一个终端，然后在此文件夹中运行：

```shell
npm init
```

该命令会有几个问题要问你，顺便说一下，这正是我们最终希望在空白 HTML 页面生成器中包含的内容。这将在文件夹中生成 `package.json` 文件：

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202005/html-g-cli/1.png)

我们需要创建包的 `index.js` 文件作为入口在 package.json 中引入。在这个文件中，写入下面代码：

```javascript
console.log("Hello World!");
```

现在我们需要创建运行这段代码的命令。

```json
{
  "name": "html-generator-cli",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "bin": "index.js"
}
```

将最后一行添加到 package.json 中。现在，我们可以测试我们非常简单的 CLI。在项目文件夹中局安装我们新创建的包到本机：

```shell
npm install -g ../html-generator-cli
```

打开一个新终端并运行：

```shell
html-generator-cli
```

如果您使用 Windows，现在应该会看到“Hello World！”。在您的终端中。如果您使用的是基于 UNIX 的操作系统，则应该得到一个错误，可能与语法错误和意外的 token 有关。我本人用的是 Mac，结果人如下

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202005/html-g-cli/2.jpg)

这是因为与 Windows 不同，基于 UNIX 的系统不关心文件的扩展名（此处为“.js”），因此不知道使用哪种语言。我们必须告诉系统使用 Node 运行脚本。为此，我们在文件的开头添加一条注释行：

```javascript
#!/usr/bin/env node

console.log("Hello World!");
```

## 创建一个空白的 HTML 页面

我们要创建一个 CLI 来生成 HTML 文件，为此，我们将使用 Node.js 文件系统模块。该模块是 Node 内置模块，提供与文件系统交互的 API，也就是说可以创建、读取、修改和删除文件。我们只需要使用文件系统模块的 `writeFile` 方法即可，该方法允许你创建文件。

```javascript
#!/usr/bin/env node

const fs = require("fs");

const html = `<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Title</title>
  </head>
  <body>
  </body>
</html>`;

fs.writeFile("index.html", html, (error) => {
  if (error) {
    console.log(error);
  }
});
```

如果您再次在终端中保存并运行 `html-generator-cli`，现在应该在文件夹中看到一个 `index.html` 文件。

## 将参数传递给代码

现在我们生产的文件名和 HTML 中的 `title` 标签内容是写死的，我们应该可以将文件名和标题作为参数传递给 CLI。要传递参数，你只需在命令之后写上参数，然后这些参数就可以在一个名为 `argv` 的变量中提供给进程。

在代码中编写如下代码：

```javascript
const args = process.argv;
console.log(args);
```

并在终端中运行它：

```javascript
html-generator-cli hello haha
```

然后，你应该在控制台中看到一个包含参数作为字符串的数组：

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202005/html-g-cli/3.png)

传递的参数在数组的最后两项，我们只需要使用数组的 `slice(2)` 方法即可拿到。我们决定第一个输入参数是文件名（不带 HTML 扩展名），第二个参数将是 HTML 页面的标题。这些参数都不是必需的，如果没有提供名称和标题，则我们将文件称为 index.html，标题为“Title”。

```javascript
#!/usr/bin/env node

const fs = require("fs");

const args = process.argv.slice(2);

let fileName = args[0] ? `${args[0]}.html` : "index.html";
let title = args[1] || "Title";

const html = `<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>${title}</title>
  </head>
  <body>
  </body>
</html>`;

fs.writeFile(fileName, html, (error) => {
  if (error) {
    console.log(error);
  }
});
```

我们保持简单，不验证用户输入的情况，用户可能会给该文件指定了无效的名称，这是你在实际工作中必须验证的内容。

现在，你可以在终端中尝试以下操作：

```shell
html-generator-cli page "new generator"
```

结果

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202005/html-g-cli/4.png)

## 使用参数选项

先前的方法易于实现，但有一些缺点：用户必须知道期望哪些参数以及以什么顺序。如果他不想给出文件名，他也没有办法给出标题，我们可以通过创建选项来改善这一点。

与其一个接一个地写参数，我们可以构建我们的 CLI，让用户输入类似于这样的文件名和/或标题。

```shell
html-generator-cli --file-name page --html-title "new generator"
```

写起来有点长，但是用户更清楚他给出的参数是什么，顺序不再起作用，你可以给出一个标题，即使你没有给出任何文件名。

```javascript
const args = process.argv;

const FILE_NAME_OPTION = "--file-name";
const HTML_TITLE_OPTION = "--html-title";

const fileNameOptionIndex = args.findIndex(
  (arg) => arg === FILE_NAME_OPTION
);
const htmlTitleOptionIndex = args.findIndex(
  (arg) => arg === HTML_TITLE_OPTION
);

const fileNameOption =
  fileNameOptionIndex > -1 && args[fileNameOptionIndex + 1];
const titleOption =
  htmlTitleOptionIndex > -1 &&
  args[htmlTitleOptionIndex + 1];

let fileName = fileNameOption
  ? `${fileNameOption}.html`
  : "index.html";
let title = titleOption || "Title";
```

现在，我们在参数数组 `args` 中获得选项 `--file-name` 或 `--html-title` 的索引。如果存在一个选项，那么要给文件名或标题的值就是参数数组中 `--file-name` 或 `--html-file` 之后的元素。如果不存在选项，则其索引将为 `-1`。如果此索引为 `-1` 或参数数组中该选项之后没有任何值，我们分别为文件名或标题提供默认值。其余代码未更改。

你可以运行新的 CLI，如果没有选择，它将创建标题为“Title”的 index.html 文件。如果你编写一个选项但忘记提供一个值，它将也提供默认值。如果你正确地使用给定的选项编写命令，那么它应该创建一个具有正确名称和正确 HTML 标题的文件。

```shell
html-generator-cli --file-name hello --html-title "CLI helloworld！"
```

效果

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202005/html-g-cli/5.png)

同样，在实际的 CLI 中，你会希望多检查一些输入，首先要确保用户输入的值是有效的，但也要在缺失值或选项出现两次的情况下警告他们。

## 向用户询问参数

使用选项已经是一种改进了，但是它仍然需要用户知道他可以传递什么参数以及使用哪个标记。当你初始化你的 npm 项目时，你可以通过很多东西作为选项。CLI 会直接问您一些问题，因此您无需阅读文档即可了解如何提供项目名称，版本等信息。

要从控制台读取用户输入，我们需要 Node（自版本 7）提供的模块 `readline`。你可以使用以下代码在终端中对其进行测试：

```javascript
const readline = require("readline");

const interface = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
});

interface.question("你叫什么名字？", (answer) => {
  console.log(`Hello ${answer}`);
  interface.close();
});
```

效果如下

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202005/html-g-cli/6.png)

为了生成我们的 HTML 页面，我们首先要询问文件名，然后询问标题。如果用户没有输入任何内容，我们将获得默认值。我们向用户显示默认值是什么，以便在默认值正确的情况下可以跳过该问题。

```javascript
#!/usr/bin/env node

const fs = require("fs");
const readline = require("readline");

let fileName = "index.html";
let title = "Title";

const interface = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
});

interface.question(
  `File name (${fileName}): `,
  (answer) => {
    if (answer && answer.length) {
      fileName = `${answer}.html`;
    }

    interface.question(
      `HTML title (${title}): `,
      (answer) => {
        if (answer && answer.length) {
          title = answer;
        }
        interface.close();

        const html = `<!DOCTYPE html>
      <html>
        <head>
          <meta charset="utf-8">
          <title>${title}</title>
        </head>
        <body>
        </body>
      </html>`;

        fs.writeFile(fileName, html, (error) => {
          if (error) {
            console.log(error);
          }
        });
      }
    );
  }
);
```

如果你在终端中运行它，将会询问两个问题。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202005/html-g-cli/7.png)

用户不必了解您的 CLI 选项，所有重要的事情都可以直接询问。但是，你应该只以这种方式询问主要配置问题，并让用户阅读文档以了解不太常见的选项。

## 结束

我们使用 Node 和 npm 创建了一个简单的 CLI，允许用户生成一个空白的 HTML 文件，是不是非常简单？你可以通过添加新选项并验证用户输入来改进此示例。
