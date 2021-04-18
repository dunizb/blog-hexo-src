---
title: 使用Typescript和ES模块发布Node模块
date: 2020-05-26 14:08:11
img: https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202005/typescript-es-modules/0.jpeg
categories:
  - Node.js
tags:
  - TypeScript
  - 实战
---

TypeScript 已经成为一种非常流行的 JavaScript 语言，这是有原因的。它的类型系统和编译器能够在您的软件运行之前的编译时捕获各种 bug，并且附加的代码编辑器功能使它成为一个非常适合开发人员的高效环境。

但是，当你想用 TypeScript 编写一个库或包，同时又想用 JavaScript 来发布，这样你的最终用户就不必手动编译你的代码，会发生什么？我们如何使用现代的 JavaScript 功能（如 ES 模块）来编写，同时又能获得 TypeScript 的所有好处？

本文旨在解决所有这些问题，并为你提供一个设置，使你可以放心地编写和共享 TypeScript 库，并为包装的使用者提供轻松的体验。

<!-- more -->

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202005/typescript-es-modules/0.jpeg)

> 本文完整代码：https://github.com/dunizb/CodeTest/tree/master/Typescript/typescript-es-modules

---

## 入门

我们要做的第一件事是建立一个新项目。在本教程中，我们将创建一个基本的数学程序包——不是一个服务于任何实际目的的程序包——因为它将让我们演示所有我们需要的 TypeScript，而不会偏离程序包的实际功能。

首先，创建一个空目录并运行 `npm init -y` 创建一个新项目。这将创建你的 `package.json` 并为你提供一个空项目以供处理：

```shell
$ mkdir maths-package
$ cd maths-package
$ npm init -y
```

现在，我们可以添加第一个也是最重要的依赖项：TypeScript！

```shell
$ npm install --save-dev typescript
```

安装 TypeScript 后，可以通过运行 `tsc --init` 初始化 TypeScript 项目。`tsc` 是“ TypeScript 编译器”的缩写，是 TypeScript 的命令行工具。

为确保你运行我们刚刚在本地安装的 TypeScript 编译器，应在命令前加上 `npx`。npx 是个很棒的工具，它将在`node_modules` 文件夹中查找你提供的命令，因此，通过在命令前面加上前缀，可以确保我们使用的是本地版本，而不是你可能已安装的 TypeScript 的任何其他全局版本。

```shell
$ npx tsc --init
```

这将创建一个 `tsconfig.json` 文件，该文件负责配置我们的 TypeScript 项目。您会看到该文件具有数百个选项，其中大多数选项已被注释掉（TypeScript 支持 `tsconfig.json` 文件中的注释）。我已将文件缩减为仅启用的设置，如下所示：

```json
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "strict": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

我们需要对此配置进行一些更改，以使我们能够使用 ES 模块发布程序包，因此，让我们现在来看一下这些选项。

## 配置`tsconfig.json` 选项

> 如果您正在寻找所有可能的 `tsconfig` 选项的完整列表，可以在 TypeScript 网站上找到此方便的[参考](https://www.typescriptlang.org/v2/en/tsconfig)。

让我们从 `target` 开始，这定义了你将在浏览器中提供代码的 JavaScript 支持级别。如果您必须使用一组较旧的浏览器，这些浏览器可能不具有所有最新和最强大的功能，则可以将其设置为 `ES2015`。如果您确实需要最大的浏览器覆盖范围，TypeScript 甚至将支持 `ES3`。

我们将在此处针对该模块使用 `ES2015`，但可以随时进行相应更改。例如，如果我为自己建立一个快速的辅助项目，并且只关心尖端的浏览器，那么我很高兴将其设置为 `ES2020`。

## 选择模块系统

接下来，我们必须决定将用于该项目的模块系统。请注意，这不是我们要编写的模块系统，而是 TypeScript 的编译器在输出代码时将使用的模块系统。

发布模块时我喜欢做的事情是发布两个版本：

- 带有 ES 模块的现代版本，以便捆绑工具可以巧妙地将未使用的代码[tree](https://webpack.js.org/guides/tree-shaking/)[–](https://webpack.js.org/guides/tree-shaking/)[shake](https://webpack.js.org/guides/tree-shaking/) ，因此支持 ES 模块的浏览器只需导入文件
- 使用 CommonJS 模块的版本（如果在 Node 中工作，你将习惯使用 `require` 代码），因此较早的构建工具和 Node.js 环境可以轻松运行该代码

稍后我们将介绍如何使用不同的选项捆绑两次，但是现在，让我们将 TypeScript 配置为输出 ES 模块。我们可以通过将 `module` 设置设置为 `ES2020` 来实现。

现在，你的 `tsconfig.json` 文件应如下所示：

```json
{
  "compilerOptions": {
    "target": "ES2015",
    "module": "ES2020",
    "strict": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

## 编写一些代码

在讨论捆绑代码之前，我们需要写一些代码！让我们创建两个小模块，它们既导出函数，又为导出所有代码的模块提供一个主 entry 文件。

我喜欢将所有 TypeScript 代码放在 `src` 目录中，因为这意味着我们可以直接将 TypeScript 编译器指向它，因此，我将使用以下代码创建 `src/add.ts`：

```typescript
export const add = (x: number, y: number): number => {
  return x + y;
};
```

我也将创建 `src/subtract.ts`：

```typescript
export const subtract = (x: number, y: number): number => {
  return x - y;
};
```

最后，`src/index.ts` 将导入我们所有的 API 方法并再次导出它们：

```typescript
import { add } from "./add.js";
import { subtract } from "./subtract.js";
export { add, subtract };
```

这意味着，用户可以通过导入只需要的东西来获取我们的功能，也可以通过获取所有的东西来获取。

```javascript
import { add } from "maths-package";

import * as MathsPackage from "maths-package";
```

请注意，在 `src/index.ts` 中，我的导入包含文件扩展名。如果只想支持 Node.js 和构建工具（例如 webpack），则不需要这样做，但是如果要支持支持 ES 模块的浏览器，则需要文件扩展名。

## 使用 TypeScript 进行编译

让我们看看是否可以让 TypeScript 编译我们的代码。我们需要先对 `tsconfig.json` 文件进行一些调整，然后才能执行以下操作：

```json
{
  "compilerOptions": {
    "target": "ES2015",
    "module": "ES2020",
    "strict": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "outDir": "./lib"
  },
  "include": ["./src"]
}
```

我们进行了两项更改：

- `compilerOptions.outDir` ——这告诉 TypeScript 将我们的代码编译到一个目录中。在这种情况下，我已经告诉它命名该目录 `lib`，但是您可以根据需要命名它。
- `include` ——告诉 TypeScript 我们希望在编译过程中包含哪些文件。在我们的例子中，我们所有的代码都位于`src` 目录中，因此我将其传入。这就是为什么我喜欢将所有 TS 源文件保存在一个文件夹中的原因，这使配置变得非常容易

让我们来试一试，看看会发生什么吧! 我发现在调整我的 TypeScript 配置时，最适合我的方法是调整、编译、检查输出，然后再调整。不要害怕尝试这些设置，看看它们如何影响最终结果。

要编译 TypeScript，我们将运行 `tsc` 并使用 `-p` 标志（“project”的缩写）告诉它 `tsconfig.json` 的位置：

```shell
npx tsc -p tsconfig.json
```

如果你有任何类型错误或配置问题，将在此处显示。如果没有，您应该什么也看不到——但是请注意，你有一个新的 `lib` 目录，其中有文件！TypeScript 编译时不会将任何文件合并在一起，而是将每个模块转换成对应的 JavaScript。

让我们看一下输出的三个文件：

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202005/typescript-es-modules/1.png)

```javascript
// lib/add.js
export const add = (x, y) => {
  return x + y;
};

// lib/subtract.js
export const subtract = (x, y) => {
  return x - y;
};

// lib/index.js
import { add } from "./add.js";
import { subtract } from "./subtract.js";
export { add, subtract };
```

它们看起来和我们的输入非常相似，但没有我们添加的类型注释。这是可以预期的：我们在 ES 模块中编写了我们的代码，并告诉 TypeScript 也要以这种形式输出。如果我们使用了比 ES2015 更新的任何 JavaScript 功能，TypeScript 会将它们转换为 ES2015 友好的语法，但是在我们的案例中，我们没有使用它，因此 TypeScript 在很大程度上仅保留了所有内容。

该模块现在可以发布到 npm 上供其他用户使用，但是我们有两个问题需要解决：

- 我们不会在代码中发布任何类型信息。这不会对我们的用户造成破坏，但这是一个错过的机会：如果我们也发布我们的类型信息，那么使用支持 TypeScript 的编辑器的人或用 TypeScript 编写应用程序的人将获得更好的体验。
- Node 还不支持开箱即用的 ES 模块。发布 CommonJS 版本也很好，所以 Node 不需要额外的工作。ES 模块支持将出现在 Node 13 和更高的版本中，但是要赶上生态系统还需要一段时间。

## 发布类型定义

我们可以通过要求 TypeScript 在写代码的同时发出一个声明文件来解决类型信息问题。这个文件的结尾是 `.d.ts`，它将包含关于我们代码的类型信息。将它看作源代码，除了不包含类型和实现之外，它只包含类型。

让我们在 `tsconfig.json` 中添加 `"declaration": true`（在 `"compilerOptions"` 部分中），然后再次运行 `npx tsc -p tsconfig.json`。

提示：我想在我的 `package.json` 文件中添加一个脚本来进行编译，因此无需输入以下内容：

```javascript
 "scripts": {
    "tsc": "tsc -p tsconfig.json"
  }
```

然后我可以运行 `npm run tsc` 来编译我的代码。

现在，您将看到每个 JavaScript 文件（例如 `add.js` ）旁边都有一个等效的 `add.d.ts` 文件，如下所示：

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202005/typescript-es-modules/2.png)

```javascript
// lib/add.d.ts
export declare const add: (x: number, y: number) => number;
```

因此，现在当用户使用我们的模块时，TypeScript 编译器将能够选择所有这些类型。

## 发布到 CommonJS

难题的最后一部分是还将 TypeScript 配置为输出使用 CommonJS 的代码版本。为此，我们可以制作两个 `tsconfig.json` 文件，一个针对 ES 模块，另一个针对 CommonJS。不过，我们可以让 CommonJS 配置扩展我们的默认设置并覆盖 `modules` 设置，而不是复制所有配置。

让我们创建 `tsconfig-cjs.json`：

```json
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "module": "CommonJS",
    "outDir": "./lib/cjs"
  }
}
```

重要的是第一行，这意味着此配置默认情况下会继承 `tsconfig.json` 的所有设置。这很重要，因为你不需要在多个 JSON 文件之间同步设置。

然后覆盖需要更改的设置。我相应地更新模块，然后将 `outDir` 设置更新到 `lib/cjs` ，这样我们就可以输出到`lib` 中的子文件夹。

此时，我还更新了 `package.json` 中的 `tsc` 脚本：

```javascript
"scripts": {
  "tsc": "tsc -p tsconfig.json && tsc -p tsconfig-cjs.json"
}
```

现在，当我们运行 `npm run tsc` 时，我们将编译两次，并且我们的 lib 目录将如下所示：

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202005/typescript-es-modules/3.png)

这个有点乱，让我们通过更新 `tsconfig` 中的 `outDir` 选项来将 ESM 输出更新到 `lib/esm` 中

接下来，我们将设置 `module` 属性。这是应该链接到我们软件包的 ES 模块版本的属性。支持此功能的工具将能够使用此版本的软件包。因此，应将其设置为 `./lib/esm/index.js` 。

接下来，我们将 `files` entry 添加到 `package.json` 中。在这里，我们定义了发布模块时应包括的所有文件。我喜欢使用这种方法来明确定义要在最终模块中推送到 npm 的文件。

这样我们就可以减小模块的大小。例如，我们不会发布 `src` 文件，而是发布 `lib` 目录。如果你在 `files` entry 中提供目录，则默认情况下会包含其所有文件和子目录，因此你不必全部列出。

> 提示：如果要查看模块中将包含哪些文件，请运行 `npx pkgfiles` 以获得列表。

现在，我们的 `package.json` 中包含以下三个附加字段：

```json
"main": "./lib/cjs/index.js",
  "module": "./lib/esm/index.js",
  "files": [
    "lib/"
  ],
```

还有最后一步。因为我们要发布 `lib` 目录，所以需要确保在运行 `npm publish` 时 `lib` 目录是最新的。npm 文档中有一节是关于如何做到这一点的——我们可以使用 `prepublishOnly` 脚本。当我们运行 `npm publish` 时，该脚本将自动为我们运行：

```json
"scripts": {
  "tsc": "tsc -p tsconfig.json && tsc -p tsconfig-cjs.json",
  "prepublish": "npm run tsc"
},
```

注意，还有一个名为 `prepublish` 的脚本，这使选择哪个稍微有些混乱。npm 文档提到了这一点：不推荐使用`prepublish` ，如果只想在发布时运行代码，则应使用`prepublishOnly`。

这样，运行 `npm publish` 将运行我们的 TypeScript 编译器并在线发布模块！我将该软件包发布在 `@ jackfranklin/maths-package-for-blog-post` 下，虽然我不建议你使用它，但是你可以浏览文件并查看。

## 结束

就是这样！我希望这篇教程已经告诉你，使用 TypeScript 上手和运行 TypeScript 并不像最初看起来那么困难，只要稍加调整，就可以让 TypeScript 输出你可能需要的多种格式，而不需要太多麻烦。

---

原文：[https://blog.logrocket.com](https://blog.logrocket.com/publishing-node-modules-typescript-es-modules/)，作者：Jack Franklin ，翻译：公众号《前端全栈开发者》
