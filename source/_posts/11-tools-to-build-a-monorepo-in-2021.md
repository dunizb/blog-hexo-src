---
title: 2021年管理Monorepo代码库的11种出色工具
date: 2020-12-26 00:03:43
img: https://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/monorepo/banner.png
categories:
  - 前端框架&工具
tags:
  - 翻译
---

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/monorepo/banner.png)

如今，许多工具可以在 20 个不同的文件夹中运行“npm install”和“npm run build”。但是，并不是所有的工具都能促进正确的 monorepo。

促进一个正确的单体开发意味着要解决一些挑战，比如为分离的模块运行测试和构建过程，能够从项目中独立发布模块，以及管理变更对项目中每个受影响的依赖模块的部分影响。

挑战的清单还在继续，甚至包括“琐碎”的事情，比如你如何管理 issues 和 PRs，这可能会随着你的开发规模而变得困难。

> 请注意，**一个 monorepo 不是一个整体的应用程序(!)** ——它不是一次性构建或部署的，它是一组单独开发的应用程序。

## 什么是 monorepo？

国庆期间 10 月 5 日尤大公开了 vue3.0 已完成的源码，也是采用了 monorepo 管理模式，看来 monorepo 确实有其独到的优势。

monorepo 是一种将多个 package 放在一个 repo 中的代码管理模式，摒弃了传统的多个 package 多个 repo 的模式。

目前 Babel, React, Angular, Ember, Meteor, Jest 等许多开源项目都使用该种模式来管理代码。

**解决的问题**

- 多个 repo 难以管理，编辑器需要打开多个项目；
- 某个模块升级，依赖改模块的其他模块需要手动升级，容易疏漏；
- 公用的 npm 包重复安装，占据大量硬盘容量，比如打包工具 webpack 会在每个项目中安装一次；
- 对新人友好，一句命令即可完成所有模块的依赖安装，且整个项目模块不用到各个仓库去找；

**带来的问题**

- 所有 package 代码集中在一个项目，单个项目体积较大；
- 所有 package 代码对所有人可见，无法做权限管理；

还不知道 monorpo 的同学可以阅读以下文章：

- [精读《Monorepo 的优势》](https://mp.weixin.qq.com/s?src=11&timestamp=1607953230&ver=2766&signature=8uylB9UXfpAnKx3kiM7B6*25FbcpOq4dXAA6XfLY6cXBDidv-9RCJRji7RR-dXvGt0EeDBE5GVuF2L8b614jud8tk5Z*SzMrqHz5Ocl54h1UbkPnbG-RILo6jRWHZXCY&new=1)
- [Vue3.0 中的 monorepo 管理模式的实现](https://www.jb51.net/article/171866.htm)
- [Lerna 包管理](https://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&mid=2651231474&idx=2&sn=455fad50ff0d6393a416b58c329c053b&chksm=bd494d768a3ec460be9cf21fdf940a3386d660ef397c86207fefdd92ad0eba0b49a667687fb7&scene=21#wechat_redirect)

在这篇综述中，我收集了一些世界上最好的工具来构建一个“monorepo”，你可以在一个项目里面构建多个模块，并且有不错的开发者体验，可以扩展。

这个列表并没有进行排名，旨在根据每个工具的优点来概述其优势。希望能帮助你节省时间，找到合适的工具。

欢迎在下方评论，分享自己的心得。

## 1. Yarn Workspaces

[**Yarn Workspaces**](https://classic.yarnpkg.com/en/docs/workspaces/) 的目标是简化与 monorepos 的工作，以更明确的方式解决 `yarn link` 的一个主要用例。你的依赖关系可以链接在一起，这意味着你的工作空间可以相互依赖，同时总是使用最新的代码。这也是比 `yarn link` 更好的机制，因为它只影响你的工作空间树而不是你的整个系统。

Workspaces 有助于解决一些问题，使其成为一个很好的单兵装备。

- 它设置了一个单一的 `node_modules`，不需要在项目中的不同包中重复或克隆依赖关系。
- 你的所有项目依赖都将被安装在一起，从而给 Yarn 更大的空间来更好地优化它们。
- Yarn 将使用一个单一的锁文件，而不是为每个项目使用不同的锁文件，这意味着更少的冲突和更容易的审查。
- 它允许你改变你的一个软件包的代码，并让使用它的其他软件包立即看到这些变化。对一个包的源代码的任何修改都会立即应用到其他包中。

因此，Yarn Workspaces 是一个非常强大的组合，可以和列表中的几乎所有工具，特别是 Bit、Nx 和 Lerna 等工具一起使用，作为你的 monorepo 管理抽象的下层。

不过，你也可以直接用 workspaces 发布。当一个工作空间被打包到一个存档中时，它会动态地将任何 `workspace:` 依赖关系替换为一个包的版本，因此您可以将结果包发布到远程注册表，而无需运行中间步骤——消费者将能够像使用任何其他包一样使用发布的工作空间。太酷了！

> **参考阅读**：[基于 lerna 和 yarn workspace 的 monorepo 工作流](https://zhuanlan.zhihu.com/p/71385053)

## 2. Bit

[**Bit**](https://bit.dev/)是用于构建模块化项目的下一代工具。这是一种新的、令人兴奋的单仓库方法，在这种方法中，由同一个项目（同一个 Bit 工作空间）管理的模块实际上分布在不同的范围内，而不考虑仓库。

Bit 让你以完全解耦的方式拆分模块的开发，享受简单的、整体的开发体验来协调一切。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/monorepo/1.png)

使用 bit，你可以在你的项目中解耦组件，这样每个组件都是独立开发、构建、测试和发布的。每个组件都是使用特殊的环境进行开发和构建的，这些环境是可扩展和可重用的，这样你就可以快速定制和再次使用它们。

Bit 的工作空间管理着项目中所有组件之间的关系。当你对任何组件进行更改时，Bit 会单独构建和测试它，并将更改传播到依赖关系图中。

组件可以作为独立的包，批量发布到 NPM 和/或 bit.dev 平台，用于协作、消费和文档。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/monorepo/2.png)

Bit 的 UI 可以帮助你查看你的 monorepo 的开发情况。当你编写代码时，每个组件都会被记录、测试、构建等，你可以通过实时反馈和热重载直观地看到正在发生的事情。

Bit 提供了解耦的开发环境--可重用和可定制的模块，这些模块将独立组件整个生命周期所需的不同服务配置和“捆绑”在一起，如编译、捆绑、测试、磨合、文档等。

![Bit的工作空间以简单而全面的方式解耦组件开发](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/monorepo/3.png)

**掌握组件图**——Bit 定义、管理并帮助你利用项目中所有组件之间的关系。

**图形驱动的构建**——当您对某个组件进行更改时，Bit 会自动检测依赖于它的其他组件，并“知道”只构建依赖组件的受影响的图形。

“图形驱动的构建”也意味着，万一一个组件被标记了新的发布版本（在被导出到 Bit 的云端之前），Bit 不仅会在每个受影响的组件上运行构建，而且会确保给它们标记一个新的发布版本。

**隔离的测试和构建**——每个组件都是在项目外部隔离地构建和测试的，因此您可以确切地看到更改的影响。

**组件构建管道**——您可以在可重用的管道中构建作业，该管道可应用于项目或所有项目中的所有组件。

**批量发布**——在 Bit monorepo 中开发的每个组件都可以作为一个独立的包发布。Bit 去掉了配置每个组件的“package.json”和其他设置文件的所有开销。你要做的就是运行'bit tag'，这样 Bit 就会自动给所有修改过的组件打上版本补丁（支持 semver 规则），然后批量发布修改。

**可重复使用的文档模板**——每个组件都使用可重复使用和可定制的模板进行文档化，Bit 为您自动完成大部分工作。用 MDX 工作？也许还可以添加一些可视化的例子？没问题。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/monorepo/4.png)

**独立渲染的组合**——每个组件都是完全独立渲染的，完全在项目之外渲染，渲染的视觉效果(在编写代码时热重新加载)成为每个组件文档的一部分。

## 3. NX

[**NX**](https://nx.dev/react)是一套先进的可扩展的开发工具，适用于 monorepos，非常强调现代全栈 Web 技术。

![空NX monorepo](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/monorepo/5.png)

NX 的目标是通过 CLI（带编辑器插件）提供整体的开发体验，并提供可控代码共享和一致代码生成的功能。它还提供了增量构建，因此它不会在你的每一次提交中重建和重新测试所有内容，从而加快构建时间。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/monorepo/6.png)

有了 Nx，你可以使用你喜欢的框架，集成你可能已经在使用的现代工具。例如，NX 可以让你使用与 Cypress、Jest、Typescript、Prettier 和其他工具的开箱即用的集成。

NX 团队还提供了[NX 云](https://nx.app/)，通过云中的智能计算记忆和更快的构建来帮助使用 NX 的团队更快地交付。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/monorepo/7.png)

![8](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/monorepo/8.png)

## 4. Rush

Rush 是由微软+开源的一个强大的 monorepo 基础设施，它的目的是帮助你在一个仓库中构建和发布许多包。

![登陆页面和一些组件，两个项目，一个仓库](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/monorepo/9.png)

rush 的一些主要功能包括一个单一的 NPM 安装（也可以和 Yarn 和 pnpm 一起使用），所以你可以将所有项目的所有依赖关系安装到一个共同的文件夹中，使用隔离的符号链接为每个项目重新构建一个准确的“node_modules”文件夹。

这也有助于确保没有幻影依赖，所以你不会意外地导入一个在 package.json 中缺失的库，也不会在 node_modules 中发现 10 份 lib 的依赖重复。

![Rush交互式CLI不错](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/monorepo/10.png)

自动本地链接意味着你所有的项目都会自动地相互建立符号链接，当你做了一个改变，你可以看到下游的效果，而不需要发布任何东西，也没有任何 `npm link` 的麻烦。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/monorepo/11.jpeg)

Rush 独特的安装策略为你的所有项目生成一个快速安装的单一收缩/锁定文件。Rush 会检测你的依赖关系图，并以正确的顺序构建你的项目，所以如果两个包之间没有直接的依赖关系，Rush 会将它们作为单独的进程并行构建。

如果你只打算使用你的 repo 中的几个项目，Rush 提供了子集和增量构建，所以 `rush rebuild --to <project>` 只对你的上游依赖进行干净的构建。在你做了修改之后，`rush rebuild --from <project>` 只对受影响的下游项目进行清理。而 `rush build` 则提供了强大的跨项目增量构建，Rush 甚至可以通过分离项目的版本来处理循环依赖关系。

当你想发布的时候，Rush 支持批量发布，所以它会检测哪些包有变化，自动跳转所有相关的版本号，并在每个文件夹中运行 `npm publish` 。

Rush 还有助于实施和执行发展政策。例如，当创建 PR 时，你可以要求开发人员提供受影响项目的主要/次要/补丁日志条目，这些条目随后将在发布时汇总到一个变更日志文件中。它还可以帮助你执行诸如发布前的审查、特定的依赖版本等东西。

## 5. Lerna

Lerna（以多头野兽 Hydra 的家命名）是一个“用于管理带有多个包的 JavaScript 项目的工具”。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/monorepo/12.png)

Lerna 的创建是为了解决 Babel 的多包问题，以优化使用 git 和 npm 管理多包仓库的工作流程，它本质上是一种工具和脚本，可以有效地管理和发布许多独立版本的包在一个 Git 仓库中。

```
my-lerna-repo/
  package.json
  packages/
    package-1/
      package.json
    package-2/
      package.json
```

Lerna 的两个主要命令是 `lerna bootstrap` 和 `lerna publish`。`bootstrap` 会将 repo 中的依赖关系连接在一起，`publish` 会帮助发布任何更新的包。

您可以使用以下两种模式之一来管理项目：固定(Fixed)或独立(Independent)。

固定模式的 Lerna 项目是以单一的版本行来操作的，版本是保存在你的项目根目录下的 `lerna.json` 文件中的 `version` 键。当您运行 `lerna publish` 时，如果一个模块在上次发布后被更新，它将被更新到您发布的新版本。这是 Babel 目前使用的模式。

![一个带有Yarn Workspaces的Lerna例子](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/monorepo/13.png)

独立模式 Lerna 项目允许维护者相互独立地增加包的版本，每次发布时，你都会收到一个提示，提示你每一个已经改变的软件包，以指定它是一个补丁，小的，大的或自定义的变化。独立模式可以让你更具体地更新每个包的版本，对于一组包来说是有意义的。

“lerna.json”文件是一个匹配包含 `package.json` 的目录的 globs 列表，这也是 lerna 识别“叶子”包的方式(相对于管理整个 repo 的开发依赖和脚本)。例子：

```json
{
  "version": "1.1.3",
  "npmClient": "npm",
  "command": {
    "publish": {
      "ignoreChanges": ["ignored-file", "*.md"],
      "message": "chore(release): publish",
      "registry": "https://npm.pkg.github.com"
    },
    "bootstrap": {
      "ignore": "component-*",
      "npmClientArgs": ["--no-package-lock"]
    }
  },
  "packages": ["packages/*"]
}
```

即使你不打算发布到 NPM，Lerna 仍然可以在 monorepo 中帮助管理版本管理和常见的开发任务。

## 6. Bazel 构建系统 (Google)

谷歌推出了[**Bazel build system**](https://bazel.build/)，它是一个类似于 Make、Maven 和 Gradle 的开源构建和测试工具，使用的是人类可读的高级构建语言。Bazel 支持多种语言的项目，并为多种平台构建输出。它支持大型单一仓库中的大型代码库或跨多个仓库的大型代码库和大量用户。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/monorepo/14.png)

Uber 开发者使用 Bazel 来构建他们的 Go monorepo。Uber 用 Go 编写了大部分的后端服务和库，在 2018 年，这些服务和库都被归纳到一个大型的 Go monorepo 中，现在有超过 10 万个文件。Bazel 让这个项目得以扩展，缩短了构建时间，并支持其发展。

> 这是一个不错的小型开源项目，以 Bazel 作为演示：[thundergolfer/example-bazel-monorepo](https://github.com/thundergolfer/example-bazel-monorepo)

Bazel 被设计成大规模工作，并支持跨分布式基础设施的增量密封构建，这是大型代码库所必需的。有了 Bazel 的远程缓存，构建服务器还可以共享它们的构建工件。Bazel 缓存所有以前完成的工作，并跟踪对文件内容和构建命令的更改。只有在包或包的依赖关系发生更改时，才构建和测试包。

Bazel 可以在 Linux、macOS 和 Windows 上运行。Bazel 可以从同一个项目为多个平台构建二进制文件和可部署的包，包括桌面、服务器和移动设备。支持许多语言，你可以扩展 Bazel 来支持任何其他语言或框架。

## 7. Buck 构建系统 (Facebook)

[Buck](https://buck.build/)是一个鼓励创建由代码和资源组成的小型可重用模块的构建系统，支持不同平台上的各种语言。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/monorepo/15.gif)

它是由 Facebook 开发和使用的，作为 FB 单体的官方构建系统，由于被 Uber 开发者等团队使用，大大缩短了构建时间，因此名声大噪。而 AirbnbEng 的团队则将构建速度提高了 50%，将应用程序缩小了 30%。

![Uber凭借buck获得了更好的构建结果](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/monorepo/16.jpeg)

Buck 被设计用来构建一个 monorepo，而对 monorepo 设计的支持激发了 Buck 对 cell 和项目的支持。

Facebook 的经验是，将所有的依赖关系维护在同一个版本库中，可以更容易地确保所有开发者拥有正确的代码版本，并简化了进行原子提交的过程。

Buck 常用于 Android 和 iOS 开发。

## 8. Pants 构建系统(Twitter)

2014 年，Twitter 推出了名为[Pants](https://github.com/pantsbuild/pants)的 monorepo 构建系统。今天，在 v2 版本上，Pants 的目标是成为一个快速、可扩展的构建系统，以适应不断增长的代码库。目前，它的重点是 Python，很快就会支持其他语言。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/monorepo/17.gif)

Pants 使用细粒度的工作流，并将每个工作单元与副作用隔离，因此可以利用所有可用的内核。Pant 的一些最佳特性包括明确的依赖建模、细粒度的无效化、共享结果缓存、并发执行、远程执行，以及通过插件 API 的可扩展性和可定制性。

Pants 引擎是用 Rust 写的，为的是性能。构建规则是用类型化的 Python 3 写的，为了熟悉和简单。该引擎的设计使得细粒度的无效化、并发性、密封性、缓存和远程执行自然发生，而无需规则作者的干预。

## 9. Please 构建系统

[**Please**](https://please.build/)是一个跨语言的构建系统，强调高性能、可移植性、可扩展性和正确性。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/monorepo/18.png)

请确保构建步骤是在自己的密封环境中执行的，只能访问被赋予权限的文件和 env 变量。增量构建意味着它只构建它需要的东西，它还提供了任务并行性，以及分布式缓存，以实现大规模的可靠和高性能的构建系统。

Please 的目标也是专注于开发体验，所以你可以享受一个常用的 CLI，并为使用自动完成的常见任务定义别名。

Please 用 Go 编写，Please 提供所有这些用户体验，没有运行时依赖。并且，没有需要处理太多配置的单个大工作区文件。

## 10. Oao

[Oao](https://github.com/guigrpa/oao)并不是列表中最成熟、最丰富、最容易使用的工具，但它还是很有趣。它是一个基于 Yarn 的，有意见的 monorepo 管理工具，p 提供 monorepo 功能，如安装所有的依赖关系，添加/删除/升级子包的依赖关系，验证版本号，确定更新的子包，一次性发布所有的东西，更新变更日志等。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/monorepo/19.gif)

Oao 可以让你在所有子包上运行命令或 `package.json` 脚本，串行或并行，可选择遵循反向依赖树。而且，它支持 yarn workspaces，从整体上优化了 monorepo 依赖树，简化了 bootstrap 以及依赖的添加/升级/删除。

支持非单包发布：从*oao*’s 的发布前检查、标签、版本选择、变更日志更新等方面受益，也可以在你的单包、非单包中使用。需要注意的是，Oao 使用的是同步版本方案，所以在根级的 `package.json` 中配置了一个主版本，而子包也将与该版本同步。你可以在这里尝试一下。

## 11. Bolt

[Boltpkg](https://github.com/boltpkg/bolt)旨在成为一个“超级功能 JavaScript 项目管理工具”。

Bolt 在 Yarn 的基础上实现了 workspaces 的概念。Bolt CLI 在很大程度上是 Yarn CLI 的替代品，你可以在任何 Yarn 项目中使用它。

我们知道，workspaces 是嵌套在一个更大的项目/repo 中的，每个 workspaces 都可以有自己的依赖关系，有自己的代码和脚本。workspaces 也可以归入子目录进行组织。

使用 Bolt，你可以一次安装所有这些包的依赖关系（而且你可以做得非常非常快）。而且，当你从一个工作区指定一个依赖关系到另一个工作区时，它将被链接到源代码。这样，当你去测试你的代码时，你所有的变化都会被一起测试。

---

原文：[https://blog.zhangbing.site/2020/12/26/11-tools-to-build-a-monorepo-in-2021/](https://blog.zhangbing.site/2020/11/26/11-tools-to-build-a-monorepo-in-2021/)  
来源：[https://blog.bitsrc.io](https://blog.bitsrc.io/11-tools-to-build-a-monorepo-in-2021-7ce904821cc2)  
作者：Jonathan Saring
