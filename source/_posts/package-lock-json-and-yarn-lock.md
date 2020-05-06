---
title: package-lock.json和yarn.lock是您最好的朋友
date: 2020-03-29 16:15:57
categories:
  - 技术
tags:
  - 前端
  - Node
---

为什么要保留它们以及何时要抛弃它们？ 

<!-- more -->

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/package-lock/1.jpeg)

你是不是遇到和思考过下面的问题？
- 什么是package-lock.json（或yarn.lock）？
- 我们为什么需要它？
- 我的package-lock.json文件中有冲突
- 我提交时会忽略它
- **我要把它删掉**

更糟糕的是，您可能已经删除了它，并提交了您的 PR 或 push 到master！

如果是这种情况，那么您已经修改了**比预期大得多**的源代码。

> 您可以控制自己的源代码，但package-lock.json和yarn.lock文件可确保您的第三方代码在两次提交之间不会发生变化

## 将第三方包视为一等公民

在解开包锁文件的秘密时，理解这一点至关重要。许多开发人员只考虑更改自己的源代码，但是通过软件包管理器（如npm和yarn）安装的第三方代码也同样重要，甚至更多。

为什么？因为它们会导致很难追踪的bug。

换句话说，如果没有package lock文件，您就不知道代码中发生了什么变化，从而产生了您可能观察到的bug。您可能会盯着提交之间的代码差异，花上几个小时思考这些更改不可能导致这个问题。

你是对的，这是你看不到的代码导致问题。

给予您的第三方软件包应有的尊重，并将他们视为您自己的代码。

> 将您的包看作是由多个开发人员提交给您的项目的二进制代码，就好像它是开源的一样

****

让我们解决以上几点。

## 什么是 package lock 文件？水果和蔬菜的比喻

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/package-lock/2.jpeg)

这样想吧，你喜欢吃水果和蔬菜。每周您都需要购买水果和蔬菜，因此您有一个标准的购物清单，很少更改：
```
每周购物清单：

水果
蔬菜
```
现在，您实际上喜欢某些种类的水果和蔬菜，因此您想使列表更具体：
```
每周购物清单：

 水果
    苹果
    葡萄
    番茄
蔬菜
    洋葱
    土豆
    南瓜
```
你知道你的口味比这个好一点，所以你添加了一些关于每种水果和蔬菜的细节：
```
每周购物清单：

水果
    苹果：红色
    葡萄：绿色
    番茄：红色
蔬菜
    洋葱：白色，红色或棕色
    土豆：洗干净的或刨皮了的
    南瓜：任何一种
```    
好的，我们有清单，而且相当详细，但是我们的一些水果和蔬菜还有一些变动的空间。所以我们出去买了清单上的东西，最后得到了一张收据：

```
收据 - 01/01/2020

苹果 - 富士
番茄 - 红色
葡萄 - 绿的
洋葱 - 棕色
土豆 - 洗净
南瓜 - 原产地
```
看到这里发生了什么吗？尽管我们没有具体说明一些水果和蔬菜的确切类型，但我们不得不购买确切的类型。

现在，当我们回到家，吃了我们的苹果，煮了我们所有的蔬菜，我们对我们的选择非常满意，所以我们保留着收据，下次带着它去购物，连同我们的清单。

**因为我们有收据，上面列出了我们上周购买的商品，所以本周我们购买的商品完全相同。**

这可能会一直持续下去，但是假设我们对苹果的口味发生了变化，所以我们写了一个新的清单：
```
每周购物清单

水果：
    苹果：绿的
    葡萄：白的
    番茄：红色
蔬菜：
    洋葱：白的红的或者棕色的
    土豆：洗干净的或刨皮了的    
    南瓜：任何一种
```
现在，当我们去购物时，我们将不得不购买某种绿色苹果。结算时，我们得到了如下的收据：
```
收据- 02/01/2020

苹果 - 南方青苹果
番茄 - 红色
葡萄 - 绿的
洋葱 - 棕色
土豆 - 洗净
南瓜 - 原产地
```
我们已经购买了与上周完全相同的东西，除了我们的苹果换成了南方青苹果！

因为我们保留了收据，所以我们能够记住我们喜欢的所有水果和蔬菜，并继续购买相同的水果和蔬菜。

> 但是，如果我们扔掉收据怎么办？我们必须从头开始，并可能最终得到我们不喜欢的水果和蔬菜。

**得到它了购物清单、收据、水果和蔬菜，但这是一篇有关软件工程的文章？**

让我们重新回到软件角度：
1. 购物清单代表您的 `package.json` 文件
2. 每个水果和蔬菜代表一个package
3. 水果或蔬菜的类型代表该package的确切版本或可接受版本的范围
4. 收据代表您的 `package-lock.json` 或 `yarn.lock` 文件

当我们保存收据，也就是我们的 package lock（包锁定） 文件时，我们能够确保获得相同package的版本，直到我们的购物清单，也就是我们的 `package.json` 文件发生了变化。

如果我们丢掉包锁定文件，很可能最终得到的包不是我们期望的。

## 包锁定文件使提交保持不变

如果在所选的版本控制工具中打开提交图，则图上的每个尖点都代表一个版本的代码，如果该代码的版本被检索（pull）是不可变的，则无论谁以及何时检索它都应该是相同的。

如果您的代码库包含具有依赖性的 `package.json` 文件，而不是`package-lock.json` 或 `yarn.lock` 文件（或其他软件包管理器用来锁定软件包版本的另一个文件），则不是这种情况。如果在不同的时间访问提交，我们可以得到不同版本的软件包。

这是因为包的新版本一直都在发布，而且我们已经允许在一些我们将接受的版本中存在差异。例如：

```json
{
  "name": "my-angular-app",
  "version": "1.0.0",
  "scripts": {
    "ng": "ng",
    "start": "ng serve",
    "build": "ng build",
    "test": "ng test",
    "lint": "ng lint",
    "e2e": "ng e2e"
  },
  "private": true,
  "dependencies": {
    "@angular/animations": "^8.2.1",
    "@angular/common": "^8.2.1",
    "@angular/compiler": "^8.2.1",
    "@angular/core": "^8.2.1",
    "@angular/forms": "^8.2.1",
    "@angular/http": "^8.0.0-beta.10",
    "@angular/platform-browser": "^8.2.1",
    "@angular/platform-browser-dynamic": "^8.2.1",
    "@angular/router": "^8.2.1",
    "core-js": "^2.5.4",
    "csstype": "^2.5.8",
    "ng2-file-upload": "^1.3.0",
    "rxjs": "~6.5.3",
    "zone.js": "~0.9.1",
    "typescript": "~3.5.0"
  },
  "devDependencies": {
    "@angular-devkit/build-angular": "~0.803.19",
    "@angular/cli": "^8.3.19",
    "@angular/compiler-cli": "^8.2.1",
    "@angular/language-service": "^8.2.1",
    "@nguniversal/express-engine": "^7.0.2",
    "@types/jasmine": "~2.8.8",
    "@types/jasminewd2": "~2.0.3",
    "@types/node": "~8.9.4",
    "codelyzer": "~4.5.0",
    "jasmine-core": "~2.99.1",
    "jasmine-spec-reporter": "~4.2.1",
    "karma": "~3.0.0",
    "karma-chrome-launcher": "~2.2.0",
    "karma-coverage-istanbul-reporter": "~2.0.1",
    "karma-jasmine": "~1.1.2",
    "karma-jasmine-html-reporter": "^0.2.2",
    "protractor": "~5.4.0",
    "ts-node": "~7.0.0",
    "tslint": "~5.11.0"
  }
}
```

注意所有的 `〜` 和 `^` 吗？如果在不同的时间安装这些软件包，则可能会导致下载这些软件包的不同版本。

> 尽管package.json文件中存在差异，但保留package-lock.json或yarn.lock文件将锁定这些版本。

我将让一些数字来说话，`yarn` 初始安装后，`node_modules` 文件夹中文件的大小和数量 

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/package-lock/3.png)

删除 `node_modules` 并重新安装后的文件大小和数量，保持`yarn.lock`

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/package-lock/4.png)

删除 `node_modules` 并重新安装，删除 `yarn.lock` 之后的文件大小和数量 

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/package-lock/5.png)

上述明细表面，在删除 `node_modules` 并重新安装包之后，`node_modules` 文件夹没有发生任何变化。

但是，在删除了 `node_modules` 文件夹和 `yarn.lock` 文件之后，又有了1507个文件，它们的大小总计超过**7mb**。

这些多余的文件到底是什么，其中一些会进入我们应用程序的运行代码中吗？可怕的是我们可能永远不会知道。

**删除或不提交您的package-lock.json 或 yarn.lock 就像说"提交我的代码并随机修改我们源代码其余部分的 X%"**

## 删除程序包锁定是否安全？

您是否曾经在代码库上做过跨越大量不相关的功能的重大重构？也许你有意更新了主要库，例如前端框架，你不确定什么，如果有什么可能会坏掉，所以你在你的应用程序上运行了一个完整的回归测试？

好吧，如果您要删除软件包锁定文件，我可能会建议您执行相同的步骤。

**如果更改代码的一个大的横截面会导致您运行一个完整的回归测试，那么也应该更改您的第三方代码的一个大的比例**

如果您确实想根据 `package.json` 文件获取最新最好的软件包（所有`〜` 和 `^` 都可以更新为最新的补丁程序和次要版本），则可以删除软件包锁定文件。

> 删除程序包锁定文件可能是利用package.json文件的强大功能的一种好方法，但请准备运行完整的回归测试或解决任何意外行为

## 总结

除了在某些极端情况下，npm包不会被删除，任何给定的版本也不会被修改，他们是不可变的。

对于任何给定的提交，您自己的代码也应该是不可变的，如果对于 Yarn 用户使用 `npm` 或 `yarn.lock`，则允许这样做的机制称为 `package-lock.json` 文件。

此文件的存在可确保针对给定的提交安装相同的软件包版本，因此无论谁使用它，何时使用它，您自己的源代码和第三方打包的代码都是相同的。

删除或不提交这些文件可能会导致应用程序发生不可预测的行为，因为对于同一次提交，软件包的实际安装版本可能会随着时间而变化，如果发现错误是由错误的程序包引起的，则可能很难追踪到它们。

因此，建议您提交而不删除这些文件，除非您打算根据 `package.json` 规范更新软件包，并且准备进行彻底的测试或快速修复生产中发现的所有错误。

感谢您的阅读，我希望这能解释 `package-lock.json` 和 `yarn-lock.json` 文件。

****
相关链接：
- [package-lock](https://docs.npmjs.com/configuring-npm/package-lock-json.html)
- [yarn-lock](https://classic.yarnpkg.com/en/docs/yarn-lock/)

****
> 原文：https://levelup.gitconnected.com/package-lock-json-and-yarn-lock-are-your-best-friends-7ec63c24fbe5
> 
> 翻译：Dunizb
