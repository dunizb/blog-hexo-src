---
title: Vite使Vue CLI过时了吗？
date: 2020-12-18 00:49:52
img: https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/blogCover/vite_vue_cli.jpg
categories:
  - 前端框架&工具
tags:
  - Vue.js
  - Vite
  - Vue CLI
  - 翻译
---

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/blogCover/vite_vue_cli.jpg)

Vue 生态系统中有一个名为 Vite 的新构建工具，它的开发服务器比 Vue CLI 快 10-100 倍。

这是否意味着 Vue CLI 已经过时了?在本文中，我将比较这两种构建工具，并说明它们的优缺点，以便你可以决定哪一种适合你的下一个项目。

<!-- more -->

## Vue CLI 概述

大多数 Vue 开发人员都知道，Vue CLI 是使用标准构建工具和最佳实践配置快速建立基于 Vue 的项目的不可或缺的工具。

其主要功能包括：

- 工程脚手架
- 带热模块重载的开发服务器
- 插件系统
- 用户界面

在本讨论中需要注意的是，Vue CLI 是构建在 Webpack 之上的，因此开发服务器和构建功能和性能都将是 Webpack 的超集。

## Vite 概述

与 Vue CLI 类似，Vite 也是一个提供基本项目脚手架和开发服务器的构建工具。

然而，Vite 并不是基于 Webpack 的，它有自己的开发服务器，利用浏览器中的原生 ES 模块。这种架构使得 Vite 比 Webpack 的开发服务器快了好几个数量级。Vite 采用 Rollup 进行构建，速度也更快。

Vite 目前还处于测试阶段，看来 Vite 项目的目的并不是像 Vue CLI 那样的一体化工具，而是专注于提供一个快速的开发服务器和基本的构建工具。

## Vite 怎么这么快？

Vite 开发服务器至少会比 Webpack 快 10 倍左右。对于一个基本的项目来说，与 2.5 秒相比，开发构建/重新构建的时间相差 250ms。

在一个较大的项目中，这种差异会变得更加明显。Webpack 开发服务器在构建/重新构建时可能会慢到 25-30 秒，有时甚至更慢。与此同时，Vite 开发服务器可能会以恒定的 250ms 的速度为同一个项目提供服务。

这显然是开发经验和游戏规则改变的差异，Vite 是如何做到这一点的？

## Webpack 开发服务器架构

Webpack 的工作方式是，它通过解析应用程序中的每一个 `import` 和 `require` ，将整个应用程序构建成一个基于 JavaScript 的捆绑包，并在运行时转换文件（例如 Sass、TypeScript、SFC）。

这都是在服务器端完成的，依赖的数量和改变后构建/重新构建的时间之间有一个大致的线性关系。

## Vite 开发服务器架构

Vite 不捆绑应用服务器端。相反，它依赖于浏览器对 JavaScript 模块的原生支持（也就是 ES 模块，是一个比较新的功能）。

浏览器将在需要时通过 HTTP 请求任何 JS 模块，并在运行时进行处理。Vite 开发服务器将按需转换任何文件（如 Sass、TypeScript、SFC）。

这种架构避免了服务器端对整个应用的捆绑，并利用浏览器高效的模块处理，提供了一个明显更快的开发服务器。

> 提示：当你对应用程序进行 code-split 和 tree-shake 动时，Vite 的速度会更快，因为它只加载它需要的模块，即使是在开发阶段。这与 Webpack 不同，在 Webpack 中，代码拆分只对生产包有利。

## Vite 的缺点

你可能已经明白了，Vite 的主要特点是它的开发服务器快得离谱。

如果没有这个功能，可能就不会再讨论了，因为与 Vue CLI 相比，它确实没有其他的功能，而且确实有一些缺点。

由于 Vite 使用了 JavaScript 模块，所以最好让依赖关系也使用 JavaScript 模块。虽然大多数现代 JS 包都提供了这一点，但一些老的包可能只提供 CommonJS 模块。

Vite 可以将 CommonJS 转换为 JavaSript 模块，但在一些边缘情况下它可能无法做到。当然，它还需要支持 JavaScript 模块的浏览器。

与 Webpack/Vue CLI 不同，Vite 无法创建针对旧版浏览器、web components 等的捆绑包。

而且，与 Vue CLI 不同，开发服务器和构建工具是不同的系统，导致在生产与开发中可能出现不一致的行为。

## Vue CLI vs Vite 总结

| Vue CLI 优点               | Vue CLI 缺点                   |
| -------------------------- | ------------------------------ |
| 经历过战斗考验，可靠       | 开发服务器速度与依赖数量成反比 |
| 与 Vue 2 兼容              |                                |
| 可以捆绑任何类型的依赖关系 |                                |
| 插件生态系统               |                                |
| 可以针对不同的目标进行构建 |                                |

| Vite 优点                         | Vite 缺点                         |
| --------------------------------- | --------------------------------- |
| 开发服务器比 Webpack 快 10-100 倍 | 只能针对现代浏览器（ES2015+）     |
| 将 code-splitting 作为优先事项    | 与 CommonJS 模块不完全兼容        |
|                                   | 处于测试阶段，仅支持 Vue 3        |
|                                   | 最小的脚手架不包括 Vuex、路由器等 |
|                                   | 不同的开发服务器与构建工具        |

那么判决结果是什么？

对于有经验的 Vue 开发来说，Vite 是一个很好的选择，因为它的开发服务器速度快得离谱，让 Webpack 看起来像史前时代。

但是，对于喜欢一些手把手的 Vue 新开发人员来说，或者，对于使用遗留模块和需要复杂构建的大型项目来说，Vue CLI 很可能在目前仍然是必不可少的。

## Vite 的未来

虽然上面的比较主要集中在 Vite 和 Vue CLI 的现状上，但仍有几点需要考虑：

- 仅当浏览器中的 JavaScript 模块支持得到改善时，Vite 才会有所改善。
- 随着 JS 生态系统的追赶，更多的软件包将支持 JavaScript 模块，减少 Vite 无法处理的边缘情况。
- Vite 仍处于测试阶段--功能可能会有变化。
- 有可能 Vue CLI 最终会结合 Vite，这样你就不用再使用其中一个了。

> 值得注意的是，Vite 并不是唯一一个利用浏览器中 JavaScript 模块的开发服务器项目。还有更著名的[Snowpack](https://www.snowpack.dev/)，甚至可能会挤掉 Vite 的发展。时间会证明这一点

## 参考

- [Vite and VitePress, Evan You, VueConf Toronto 2020](https://www.youtube.com/watch?v=xXrhg26VCSc)
- [Vite - GitHub](https://github.com/vitejs/vite)
- [Vue CLI - GitHub](https://github.com/vuejs/vue-cli)

---

原文：[https://blog.zhangbing.site/2020/12/18/vite-vue-cli/](https://blog.zhangbing.site/2020/12/18/vite-vue-cli/)  
来源：[https://vuejsdevelopers.com](https://vuejsdevelopers.com/2020/12/07/vite-vue-cli/)
