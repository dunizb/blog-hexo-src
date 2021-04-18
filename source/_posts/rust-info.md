---
title: 针对JavaScript开发人员的Rust简介
date: 2021-02-16 16:03:28
img: https://content.markdowner.net/pub/QXzdL1-deep42B
categories:
  - 其他
tags:
  - Rust
summary: "Rust 是 2010 年起源于 Mozilla Research 的一种编程语言。如今，所有大公司都在使用它。"
---

![](https://content.markdowner.net/pub/QXzdL1-deep42B)

**Rust 是 2010 年起源于 Mozilla Research 的一种编程语言。如今，所有大公司都在使用它。**

<!-- more -->

亚马逊和微软都认可它是其系统中 C / C ++的最佳替代品，但是 Rust 并不止于此。像 Figma 和 Discord 这样的公司现在也通过在客户端应用中使用 Rust 来引领潮流。

本篇 Rust 教程旨在简要介绍 Rust，如何在浏览器中使用它，以及何时应该考虑使用它。我将从比较 Rust 和 JavaScript 开始，然后引导你完成 Rust 在浏览器中运行的步骤。最后，我将介绍一个使用 Rust 和 JavaScript 的 COVID simulator web 应用程序的快速性能评估。

## 简而言之

Rust 在概念上与 JavaScript 非常不同。但也有相似之处需要指出，让我们来看看问题的两面。

### 相似之处

这两种语言都有一个现代化的包管理系统。JavaScript 有 npm，Rust 有 Cargo。Rust 有 `Cargo.toml` 来代替 `package.json` 进行依赖管理。要创建一个新的项目，使用 `cargo init`，要运行它，使用 `cargo run`。不太陌生吧？

Rust 中有许多很酷的功能，你已经从 JavaScript 中知道了，只是语法略有不同。利用这个常见的 JavaScript 模式，对数组中的每个元素都应用一个闭包：

```js
let staff = [
  { name: "George", money: 0 },
  { name: "Lea", money: 500000 },
];
let salary = 1000;
staff.forEach((employee) => {
  employee.money += salary;
});
```

在 Rust 中，我们可以这样写：

```rust
let salary = 1000;
staff.iter_mut().for_each(
    |employee| { employee.money += salary; }
);
```

诚然，习惯这种语法需要时间，用管子( `|` )代替括号。但在克服了最初的尴尬之后，我发现它比另一组括号读起来更清晰。

再举一个例子，这是 JavaScript 中的对象解构：

```js
let point = { x: 5, y: 10 };
let { x, y } = point;
```

同样在 Rust 中：

```rust
let point = Point { x: 5, y: 10 };
let Point { x, y } = point;
```

主要的区别是，在 Rust 中我们必须指定类型（`Point`），更普遍的是，Rust 需要在编译时知道所有类型。但与大多数其他编译语言不同的是，编译器尽可能自己推断类型。

为了进一步解释这个问题，下面是在 C++和许多其他语言中有效的代码。每个变量都需要明确的类型声明。

```c++
int a = 5;
float b = 0.5;
float c = 1.5 * a;
```

在 JavaScript 以及 Rust 中，这段代码是有效的：

```js
let a = 5;
let b = 0.5;
let c = 1.5 * a;
```

共享功能不胜枚举：

- Rust 具有 `async` + `await` 语法。
- 数组可以像让 `let array = [1,2,3]` 一样简单地创建。
- 代码按模块组织，有明确的导入和导出。
- 字符串是用 Unicode 编码的，处理特殊字符没有问题。

我可以继续列举下去，但我想我的观点现在已经很清楚了。Rust 有一系列丰富的功能，这些功能在现代 JavaScript 中也有使用。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/blogAssert/bcrl.png)

### 不同点

Rust 是一种编译语言，这意味着没有运行时可以执行 Rust 代码。一个应用程序只能在编译器(`rustc`)完成它的魔法之后运行。这种方法的好处通常是更好的性能。

幸运的是，Cargo 为我们解决了调用编译器的问题。而有了 webpack，我们还可以将 `Cargo` 隐藏在 `npm run build` 后面。有了这个指南，只要为项目设置好 Rust，就可以保留 Web 开发者的正常工作流程。

Rust 是一种强类型语言，这意味着在编译时所有类型必须匹配。例如，你不能调用一个参数类型错误或参数数量错误的函数。编译器会在你运行时遇到这个错误之前为你捕捉到它。显而易见的比较是 TypeScript，如果你喜欢 TypeScript，那么你很可能会喜欢 Rust。

但别担心：如果你不喜欢 TypeScript，Rust 可能还是适合你。Rust 是近几年从头开始构建的，它考虑到了过去几十年来人类在编程语言设计方面所学到的一切。其结果是一种令人耳目一新的简洁语言。

Rust 中的模式匹配是我最喜欢的一个特征，其他语言有 `switch` 和 `case` 来避免像这样的长链:

```js
if (x == 1) {
  // ...
} else if (x == 2) {
  // ...
} else if (x == 3 || x == 4) {
  // ...
} // ...
```

Rust 使用了如下更优雅的匹配项：

```rust
match x {
  1 => { /* Do something if x == 1 */},
  2 => { /* Do something if x == 2 */},
  3 | 4 => { /* Do something if x == 3 || x == 4 */},
  5...10 => { /* Do something if x >= 5 && x <= 10 */},
  _ => { /* Catch all other cases */ }
}
```

我认为这是非常整洁的，我希望 JavaScript 开发人员也能欣赏这种语法扩展。

不幸的是，我们还得谈谈 Rust 的黑暗面。直言不讳地说，使用严格的类型系统有时会让人感觉非常繁琐。如果你认为 C++或 Java 的类型系统很严格，那么请准备好迎接 Rust 的艰难之旅吧。

就我个人而言，我很喜欢 Rust 这部分。我依赖于严格的类型系统，因此可以关闭大脑的一部分——每当我发现自己在编写 JavaScript 时，大脑的一部分就会剧烈地兴奋起来。但是我知道对于初学者来说，总是和编译器作对是很烦人的。我们将在稍后的 Rust 教程中看到一些。

## Hello Rust

现在，让我们用 Rust 在浏览器中运行一个 `hello world` ，我们首先要确保所有必要的工具都已安装。

### 工具

**使用 rustup 安装 Cargo + rustc。** Rustup 是推荐的安装 Rust 的方法，它将安装最新的稳定版 Rust 的编译器（rustc）和包管理器（Cargo）。它将安装 Rust 最新稳定版本的编译器（rustc）和包管理器（Cargo）。它还可以管理 beta 版和每夜构建版，但对于本例来说，这不是必需的。

- 在终端机上输入 `cargo --version` 来检查安装情况，你应该可以看到 `cargo 1.48.0 (65cbdd2dc 2020-10-14)` 这样的内容。
- 还要检查 Rustup：`rustup --version` 应该产生 `rustup 1.23.0（00924c9ba 2020-11-27）`。

**安装 wasm-pack。** 这是为了将编译器与 npm 集成。

- 通过输入 `wasm-pack --version` 来检查安装，这应该为您提供 `wasm-pack 0.9.1` 之类的东西。

我们还需要 Node 和 npm。我们有一篇完整的[文章](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/ "文章")解释了安装这两个的最佳方法。

### 编写 Rust 代码

现在一切都安装好了，让我们来创建项目。最终的代码也可以在这个[GitHub 仓库](https://github.com/sitepoint-editors/rust-wasm-hello-world "GitHub仓库")中找到。我们从一个可以编译成 npm 包的 Rust 项目开始，之后会有导入该包的 JavaScript 代码。

要创建一个名为 `hello-world` 的 Rust 项目，请使用 `cargo init --lib hello-world`。这将创建一个新目录并生成 Rust 库所需的所有文件：

```
├──hello-world
    ├── Cargo.toml
    ├── src
        ├── lib.rs
```

Rust 代码将放在 `lib.rs` 中，在此之前我们必须调整 `Cargo.toml`。它使用 [TOML](https://github.com/toml-lang/toml "TOML") 定义了依赖关系和其他包的信息。如果想在浏览器中看到 hello world，请在 `Cargo.toml` 中的某个地方添加以下行数（例如，在文件的最后）。

```toml
[lib]
crate-type = ["cdylib"]
```

这告诉编译器在 C 兼容模式下创建一个库。显然我们在我们的例子中没有使用 C。C-compatible 只是意味着不是 Rust 专用的，这是我们使用 JavaScript 中的库所需要的。

我们还需要两个外部库，将它们作为单独的一行添加到依赖关系部分。

```toml
[dependencies]
wasm-bindgen = "0.2.68"
web-sys = {version = "0.3.45", features = ["console"]}
```

这些都是来自 [crates.io](https://crates.io/ "crates.io") 的依赖项，它是 Cargo 使用的默认包仓库。

[wasm-bindgen](https://crates.io/crates/wasm-bindgen "wasm-bindgen")是必要的，以创建一个我们以后可以从 JavaScript 中调用的入口点。(你可以在这里找到完整的文档。)值 `”0.2.68"` 指定了版本。

[web-sys](https://crates.io/crates/web-sys "web-sys")包含了所有 Web API 的 Rust 绑定，它将使我们能够访问浏览器控制台。请注意，我们必须明确地选择控制台功能，我们最终的二进制文件将只包含这样选择的 Web API 绑定。

接下来是 `lib.rs` 内部的实际代码。自动生成的单元测试可以删除。只需使用以下代码替换文件的内容：

```toml
use wasm_bindgen::prelude::*;
use web_sys::console;

#[wasm_bindgen]
pub fn hello_world() {
    console::log_1("Hello world");
}
```

顶部的 `use` 语句是用于从其他模块导入项目。这与 JavaScript 中的 `import` 类似）。

`pub fn hello_world（）{...}` 声明一个函数。 `pub` 修饰符是“public”的缩写，作用类似于 JavaScript 中的 `export`。注释 `＃[wasm_bindgen]` 特定于 Rust 编译为[WebAssembly (Wasm)](https://webassembly.org/ "wasm_bindgen]`特定于Rust编译为[WebAssembly (Wasm "wasm_bindgen]` 特定于 Rust 编译为[WebAssembly (Wasm)")")。我们在这里需要它来确保编译器将包装函数公开给 JavaScript。

在功能主体中，“Hello world”被打印到控制台上。 Rust 中的 `console :: log_1()` 是对 `console.log()` 的调用的包装。

你是否注意到函数调用中的 `_1` 后缀？这是因为 JavaScript 允许使用可变数量的参数，而 Rust 不允许。为了解决这个问题， `wasm_bindgen` 为每种参数数量生成一个函数。是的，这很快就会变得丑陋！但这有效。 在[web-sys 文档](https://rustwasm.github.io/wasm-bindgen/api/web_sys/console/index.html "web-sys文档")中提供了一个可以在 Rust 控制台中调用的完整函数列表。

现在我们应该已经一切就绪，试着用下面的命令编译它。这将下载所有的依赖项并编译项目，第一次可能会花一些时间。

```toml
cd hello-world
wasm-pack build
```

哈！Rust 编译器对我们不满意。

```
error[E0308]: mismatched types
 --> src\lib.rs:6:20
  |
6 |     console::log_1("Hello world");
  |                    ^^^^^^^^^^^^^ expected struct `JsValue`, found `str`
  |
  = note: expected reference `&JsValue`
             found reference `&'static str
```

注意：如果您看到其他错误（`error: linking with cc failed: exit code: 1`）并且你使用的是 Linux，则说明缺少交叉编译依赖性。 `sudo apt install gcc-multilib` 应该可以解决此问题。

正如我前面提到的，编译器很严格。当它期望一个 `JsValue` 的引用作为一个函数的参数时，它不会接受一个静态字符串。为了满足编译器的要求，必须进行显式转换。

```rust
console::log_1(&"Hello world".into());
```

方法 [into()](https://doc.rust-lang.org/std/convert/trait.Into.html "into( "into()")") 将一个值转换为另一个值。Rust 编译器很聪明，它可以推迟哪些类型参与转换，因为函数签名只留下了一种可能性。在这种情况下，它将转换为 `JsValue`，这是一个由 JavaScript 管理的值的包装类型。然后，我们还得加上 `&`，通过引用而不是通过值来传递，否则编译器又会抱怨。

尝试再次运行 `wasm-pack build`，如果一切顺利，则最后一行应如下所示：

```
[INFO]: :-) Your wasm pkg is ready to publish at /home/username/intro-to-rust/hello-world/pkg.
```

如果你能走到这一步，你现在就可以手动编译 Rust 了。下一步，我们将把它与 npm 和 webpack 集成，后者将自动为我们完成这项工作。

## JavaScript 整合

在这个例子中，我决定将 `package.json` 放在 `hello-world` 目录内。我们也可以为 Rust 项目和 JavaScript 项目使用不同的目录,这是个口味问题。

以下是我的 `package.json` 文件。遵循的最简单方法是将其复制并运行 `npm install`，或者运行 `npm init` 并仅复制 `dev` 依赖项：

```json
{
  "name": "hello-world",
  "version": "1.0.0",
  "description": "Hello world app for Rust in the browser.",
  "main": "index.js",
  "scripts": {
    "build": "webpack",
    "serve": "webpack serve"
  },
  "author": "Jakob Meier <inbox@jakobmeier.ch>",
  "license": "(MIT OR Apache-2.0)",
  "devDependencies": {
    "@wasm-tool/wasm-pack-plugin": "~1.3.1",
    "@webpack-cli/serve": "^1.1.0",
    "css-loader": "^5.0.1",
    "style-loader": "^2.0.0",
    "webpack": "~5.8.0",
    "webpack-cli": "~4.2.0",
    "webpack-dev-server": "~3.11.0"
  }
}
```

如你所见，我们使用的是 webpack 5。Wasm-pack 也可以和旧版本的 webpack 一起使用，甚至可以不使用捆绑程序。但每个设置的工作方式都有些不同，我建议你在跟随这个 Rust 教程时使用完全相同的版本。

另一个重要的依赖项是 `wasm-pack-plugin`。这是一个 Webpack 插件，专门用于加载使用 wasm-pack 构建的 Rust 软件包。

继续，我们还需要创建 `webpack.config.js` 文件来配置 webpack。它应该是这样的：

```js
const path = require("path");
const webpack = require("webpack");
const WasmPackPlugin = require("@wasm-tool/wasm-pack-plugin");

module.exports = {
  entry: "./src/index.js",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "index.js",
  },
  plugins: [
    new WasmPackPlugin({
      crateDirectory: path.resolve(__dirname, "."),
    }),
  ],
  devServer: {
    contentBase: "./src",
    hot: true,
  },
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: ["style-loader", "css-loader"],
      },
    ],
  },
  experiments: {
    syncWebAssembly: true,
  },
};
```

所有的路径都配置为 Rust 代码和 JavaScript 代码并排。`index.js` 将在 `src` 文件夹中，紧挨着 `lib.rs`。如果你喜欢不同的设置，可以随时调整这些。

你还会注意到，我们使用[webpack experiments](https://webpack.js.org/configuration/experiments/ "webpack experiments")，这是 webpack 5 引入的新选项。请确保将 `syncWebAssembly` 设置为 true。

最后，我们必须创建 JavaScript 入口点 `src/index.js`：

```js
import("../pkg")
  .catch((e) =>
    console.error("Failed loading Wasm module:", e)
  )
  .then((rust) => rust.hello_world());
```

我们必须异步加载 Rust 模块。调用 `rust.hello_world()` 会调用一个生成的封装函数，而这个函数又会调用 `lib.rs` 中定义的 Rust 函数 `hello_world`。

现在，运行 `npm run serve` 应该可以编译所有内容并启动开发服务器。我们没有定义 HTML 文件，因此页面上没有任何显示。你可能还必须手动转到 http://localhost:8080/index，因为http://localhost:8080只是列出文件而不执行任何代码。

打开空白页后，打开开发人员控制台。 Hello World 应该有一个日志条目。

好吧，对于一个简单的 hello world 来说，这是相当多的工作。但现在一切都到位了，我们可以轻松地扩展 Rust 代码，而不用担心这些。保存对 `lib.rs` 的修改后，你应该会自动看到重新编译和浏览器中的实时更新，就像 JavaScript 一样。

## 何时使用 Rust

Rust 不是 JavaScript 的一般替代品。它只能通过 Wasm 在浏览器中运行，这在很大程度上限制了它的作用。即使你可以用 Rust 替换几乎所有的 JavaScript 代码，如果你真的想的话，那是一个坏主意，而且不是 Wasm 的目的。例如，Rust 并不适合与你网站的 UI 进行交互。

我认为 Rust + Wasm 是一个额外的选项，可以用来更有效地运行 CPU 重的工作负载。以较大的下载量为代价，Wasm 避免了 JavaScript 代码面临的解析和编译开销。这一点，再加上编译器的强力优化，可能会带来更好的性能。这通常是公司为特定项目选择 Rust 的原因。选择 Rust 的另一个原因可能是语言偏好，但这是一个完全不同的讨论，我不会在这里讨论。
