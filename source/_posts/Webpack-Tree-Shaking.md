---
title: Webpack 的 Tree Shaking 用法
date: 2019-12-29 21:46:03
categories:
  - 前端框架&工具&工具
tags:
  - Webpack
---

webpack 2.0 开始引入 tree shaking 技术，翻译过来的中文意思就是摇树，它可以在打包时忽略没有用到的代码。

<!-- more -->

![疼！大连这些树快被摇断了](https://5b0988e595225.cdn.sohucs.com/images/20180623/8029f5ff915041c0a5e88ea0be6a82a0.gif)

## 机制简述

tree shaking 是 rollup 作者首先提出的。这里有一个比喻：

> 如果把代码打包比作制作蛋糕。传统的方式是把鸡蛋(带壳)全部丢进去搅拌，然后放入烤箱，最后把(没有用的)蛋壳全部挑选并剔除出去。而 treeshaking 则是一开始就把有用的蛋白蛋黄放入搅拌，最后直接作出蛋糕。

因此，相比于 排除不使用的代码，tree shaking 其实是找出使用的代码。

Tree shaking 两步走：

1. webpack 负责对代码进行标记，把 `import` & `export` 标记为 3 类：

   1. 所有 `import` 标记为 `/* harmony import */`
   2. 被使用过的 `export` 标记为 `/* harmony export ([type]) */`，其中 `[type]` 和 webpack 内部有关，可能是 `binding`, `immutable` 等等。
   3. 没被使用过的 `export` 标记为 `/* unused harmony export [FuncName] */`，其中 `[FuncName]` 为 `export` 的方法名称

2. 之后在 Uglifyjs (或者其他类似的工具) 步骤进行代码精简，把没用的都删除。

基于 ES6 的静态引用，tree shaking 通过扫描所有 ES6 的 `export`，找出被 `import` 的内容并添加到最终代码中。 webpack 的实现是把所有 `import` 标记为有使用/无使用两种，在后续压缩时进行区别处理。因为就如比喻所说，在放入烤箱(压缩混淆)前先剔除蛋壳(无使用的 `import`)，只放入有用的蛋白蛋黄(有使用的 `import`)

## 使用方法

首先源码必须遵循 ES6 的模块规范 (import & export)，如果是 CommonJS 规范 (require) 则无法使用。

根据 webpack 官网的提示，webpack2 支持 tree-shaking，需要修改配置文件，指定 babel 处理 js 文件时不要将 ES6 模块转成 CommonJS 模块，具体做法就是：在`.babelrc` 设置 `babel-preset-es2015` 的 `modules` 为 `fasle`，表示不对 ES6 模块进行处理。

```js
// .babelrc
{
    "presets": [
        ["es2015", {"modules": false}]
    ]
}
```

经过测试，webpack 3 和 4 不增加这个 `.babelrc` 文件也可以正常 tree shaking。

**在 production 环境下**，webapck 会自动写好配置不需要我们配置，production 环境下 的 devtool 配置一般会使用 `cheap-module-source-map`。

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/Tree-Shaking/1.png)

webpack4 在 `package.json` 新增了一个配置项叫做 `sideEffects`， 值为 `false` 表示整个包都没有副作用；或者是一个数组列出有副作用的模块。

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/Tree-Shaking/2.png)

## 总结

1. 使用 ES6 模块语法编写代码
2. 工具类函数尽量以单独的形式输出，不要集中成一个对象或者类
3. 声明 sideEffects
4. 自己在重构代码时也要注意副作用
