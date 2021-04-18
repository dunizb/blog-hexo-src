---
title: 【译】如何设计一个JavaScript插件系统
date: 2020-08-30 12:24:28
cover: true
categories:
  - JavaScript
tags:
  - 翻译
---

WordPress 有插件、 jQuery 有插件、Gatsby、Eleventy 和 Vue 也是如此。

插件是库和框架的常见功能，并且有一个很好的理由：它们允许开发人员以安全，可扩展的方式添加功能。这使核心项目更具价值，并建立了一个社区——所有这些都不会增加额外的维护负担。太好了！

那么如何去构建一个插件系统呢？让我们用 JavaScript 构建一个我们自己的插件来回答这个问题。

<!-- more -->

## 让我们构建一个插件系统

让我们从一个名为 BetaCalc 的示例项目开始。BetaCalc 的目标是成为一个简约的 JavaScript 计算器，其他开发人员可以在其中添加“按钮”。以下是一些基本的入门代码：

```javascript
// 计算器
const betaCalc = {
  currentValue: 0,

  setValue(newValue) {
    this.currentValue = newValue;
    console.log(this.currentValue);
  },

  plus(addend) {
    this.setValue(this.currentValue + addend);
  },

  minus(subtrahend) {
    this.setValue(this.currentValue - subtrahend);
  },
};

// 使用计算器
betaCalc.setValue(3); // => 3
betaCalc.plus(3); // => 6
betaCalc.minus(2); // => 4
```

我们将计算器定义为一种客观事物，以使事情变得简单，计算器通过 `console.log` 打印结果来工作。

目前功能确实很有限。我们有一个 `setValue` 方法，该方法接受一个数字并将其显示在“屏幕”上。我们还有加法（`plus`）和减法（`minus`）方法，它们将对当前显示的值执行一个运算。

现在该添加更多功能了。首先创建一个插件系统。

## 世界上最小的插件系统

我们将从创建一个注册（`register`）方法开始，其他开发人员可以使用该方法向 BetaCalc 注册插件。该方法的工作很简单：获取外部插件，获取其 `exec` 函数，并将其作为新方法附加到我们的计算器上：

```javascript
// 计算器
const betaCalc = {
  // ...其他计算器代码在这里

  register(plugin) {
    const { name, exec } = plugin;
    this[name] = exec;
  },
};
```

这是一个示例插件，为我们的计算器提供了一个“平方（`squared`）”按钮：

```javascript
// 定义插件
const squaredPlugin = {
  name: "squared",
  exec: function() {
    this.setValue(this.currentValue * this.currentValue);
  },
};

// 注册插件
betaCalc.register(squaredPlugin);
```

在许多插件系统中，插件通常分为两个部分：

1. 要执行的代码
2. 元数据（例如名称，描述，版本号，依赖项等）

在我们的插件中，`exec` 函数包含我们的代码，`name` 是我们的元数据。注册插件后，`exec` 函数将作为一种方法直接附加到我们的 `betaCalc` 对象，从而可以访问 BetaCalc 的 `this`。

现在，BetaCalc 有一个新的“平方”按钮，可以直接调用：

```javascript
betaCalc.setValue(3); // => 3
betaCalc.plus(2); // => 5
betaCalc.squared(); // => 25
betaCalc.squared(); // => 625
```

这个系统有很多优点。该插件是一种简单的对象字面量，可以传递给我们的函数。这意味着插件可以通过 npm 下载并作为 ES6 模块导入。易于分发是超级重要的！

但是我们的系统有一些缺陷。

通过为插件提供访问 BetaCalc 的 `this` 权限，他们可以对所有 BetaCalc 的代码进行读/写访问。虽然这对于获取和设置 `currentValue` 很有用，但也很危险。如果插件要重新定义内部函数（如 `setValue`），则它可能会为 BetaCalc 和其他插件产生意外的结果。这违反了开放-封闭原则，即一个软件实体应该是开放的扩展，但封闭修改。

另外，“squared”函数通过产生副作用发挥作用。这在 JavaScript 中并不少见，但感觉并不好——特别是当其他插件可能处在同一内部状态的情况下。一种更实用的方法将大大有助于使我们的系统更安全、更可预测。

## 更好的插件架构

让我们再来看看一个更好的插件架构。下一个例子同时改变了计算器和它的插件 API：

```javascript
// 计算器
const betaCalc = {
  currentValue: 0,

  setValue(value) {
    this.currentValue = value;
    console.log(this.currentValue);
  },

  core: {
    plus: (currentVal, addend) => currentVal + addend,
    minus: (currentVal, subtrahend) =>
      currentVal - subtrahend,
  },

  plugins: {},

  press(buttonName, newVal) {
    const func =
      this.core[buttonName] || this.plugins[buttonName];
    this.setValue(func(this.currentValue, newVal));
  },

  register(plugin) {
    const { name, exec } = plugin;
    this.plugins[name] = exec;
  },
};

// 我们得插件，平方插件
const squaredPlugin = {
  name: "squared",
  exec: function(currentValue) {
    return currentValue * currentValue;
  },
};

betaCalc.register(squaredPlugin);

// 使用计算器
betaCalc.setValue(3); // => 3
betaCalc.press("plus", 2); // => 5
betaCalc.press("squared"); // => 25
betaCalc.press("squared"); // => 625
```

我们在这里做了一些值得注意的更改。

首先，我们将插件与“核心（core）”计算器方法（如 plus 和 minus）分开，方法是将其放入自己的插件对象中。将我们的插件存储在`plugins` 对象中可使我们的系统更安全。现在，访问此 plugins 的插件将看不到 BetaCalc 属性，而只能看到 `betaCalc.plugins` 的属性。

其次，我们实现了一个 `press` 方法，该方法按名称查找按钮的功能，然后调用它。现在，当我们调用插件的 `exec` 函数时，我们将当前的计算器值（`currentValue` ）传递给该函数，并期望它返回新的计算器值。

本质上，这个新的 `press` 方法将我们所有的计算器按钮转换为纯函数。他们获取一个值，执行一个操作，然后返回结果。这有很多好处：

- 它简化了 API。
- 它使测试更加容易（对于 BetaCalc 和插件本身）。
- 它减少了我们系统的依赖性，使其更松散地耦合在一起。

这种新架构比第一个示例受到更多限制，但效果很好。我们基本上为插件作者设置了护栏，限制他们只能做我们希望他们做的改动。

实际上，它可能太严格了！现在，我们的计算器插件只能对 `currentValue` 进行操作。如果插件作者想要添加高级功能（例如“记忆”按钮或跟踪历史记录的方式），那么他们将无能为力。

也许这就是好的。你给插件作者的能力是一种微妙的平衡。给他们太多的权力可能会影响你项目的稳定性。但给它们的权力太小，它们就很难解决自己的问题——在这种情况下，你还不如不要插件。

## 我们还能做什么？

我们还有很多工作可以改善我们的系统。

如果插件作者忘记定义名称或返回值，我们可以添加错误处理以通知插件作者。像 QA 开发人员一样思考并想象一下我们的系统如何崩溃，以便我们能够主动处理这些情况，这是很好的。

我们可以扩展插件的功能范围。当前，一个 BetaCalc 插件可以添加一个按钮。但是，如果它还可以注册某些生命周期事件的回调（例如当计算器将要显示值时）怎么办？或者说，如果有一个专门的地方让它在多个交互中存储一段状态呢？这会不会开辟一些新的用例？

我们还可以扩展插件注册的功能。如果一个插件可以通过一些初始设置来注册呢？这是否能使插件更加灵活？如果一个插件作者想注册一整套按钮，而不是一个单一的按钮——比如“BetaCalc 统计包”？需要做哪些改动来支持呢？

## 你的插件系统

BetaCalc 及其插件系统都非常简单。如果你的项目较大，则需要探索其他一些插件架构。

一个很好的起点是查看现有项目，以获取成功的插件系统的示例。对于 JavaScript，这可能意味着 jQuery，Gatsby，D3，CKEditor 或其他。你可能还想熟悉各种 JavaScript 设计模式，每种模式都提供了不同的接口和耦合程度，这给你提供了很多好的插件架构选择。了解这些选项有助于你更好地平衡使用你的项目的每个人的需求。

除了模式本身之外，你还可以借鉴许多好的软件开发原则来做出此类决策。我已经提到了一些方法（例如开闭原则和松散耦合），但是其他一些相关的方法包括 Demeter 定律和依赖注入。

我知道这听起来很多，但你必须进行研究。没有什么比让每个人都重写他们的插件更痛苦的了，因为你需要更改插件架构。这是一种快速失去信任的方式，让人们失去对未来贡献的信心。

## 总结

从头开始编写好的插件架构是困难的！你必须平衡很多考虑因素，才能建立一个满足大家需求的系统。它是否足够简单？功能够强大吗？它是否能长期工作？

不过这也是值得的，有一个好的插件系统对大家都有帮助，开发者可以自由地解决他们的问题。最终用户可以从中选择大量可选择的特性。你可以围绕你的项目建立一个生态系统和社区。这是一个三赢的局面。

---

原文：https://css-tricks.com/designing-a-javascript-plugin-system/

作者：Bryan Braun
