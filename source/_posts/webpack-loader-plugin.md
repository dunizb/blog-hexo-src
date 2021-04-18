---
title: Webpack进阶：两个小例子学会编写自己的Loader和Plugin
date: 2018-12-21 19:08:11
categories:
  - 前端框架&工具&工具
tags:
  - Webpack
---

本文介绍通过两个小需求，教你如何编写自己的 Loader 和 Plugins

<!-- more -->

![](https://gitee.com/dunizb/imgcloud/raw/master/webpack1.png)

## 1、Loader 和 Plugin 的区别

### 1.1 Loader

loader 是一个文件加载器，能够加载资源文件，并对这些文件进行一些处理，诸如编译、压缩等，最终一起打包到指定的文件中。

Loader 的特点：

- 处理一个文件可以使用多个 loader，loader 的执行顺序是和本身的顺序是相反的，即最后一个 loader 最先执行，第一个 loader 最后执行。
- 第一个执行的 loader 接收源文件内容作为参数，其他 loader 接收前一个执行的 loader 的返回值作为参数。最后执行的 loader 会返回此模块的 JavaScript 源码
- loader 可以是同步的，也可以是异步的。
- loader 是用 node.js 来跑，可以做一切可能的事情。
- loader 接收 query 参数。这些参数会传入 loaders 内部作为配置来用。
- loader 可以用 npm 发布或者安装。
- 除了用 package.json 的 main 导出的 loader 外， 一般的模块也可以导出一个 loader。

### 1.2 Plugin

plugin 用于扩展 webpack 的功能。它直接作用于 webpack，扩展了它的功能。当然 loader 也时变相的扩展了 webpack ，但是它只专注于转化文件（transform）这一个领域。而 plugin 的功能更加的丰富，而不仅局限于资源的加载。

在 Webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。

### 1.3 plugin 和 loader 的区别是什么？

对于 loader，它就是一个转换器，将 A 文件进行编译形成 B 文件，这里操作的是文件，比如将 A.scss 或 A.less 转变为 B.css，单纯的文件转换过程

plugin 是一个扩展器，它丰富了 wepack 本身，针对是 loader 结束后，webpack 打包的整个过程，它并不直接操作文件，而是基于事件机制工作，会监听 webpack 打包过程中的某些节点，执行广泛的任务。

在使用上：loader 不需要 import 导入就可以使用，而 plugin 需要 import 导入才能使用。

## 2、如何编写一个 Loader

### 2.1 先定义一个需求

我们要实现的需求：处理 JS 文件，把内容变为大写。例如，JS 文件源内容为”hello webpack“，那么经过我们的 Loader 转化后的内容为"HELLO WEBPACK"。

### 2.2 编写 Loader

**1)编写要处理的源文件**

在 src 下编写入口的 index.js 文件，内容如下

```
console.log('hello webapck')
console.log('http://blog.zhangbing.site')
```

**2）编写 Loader**

我们需要编写一个 Loader 来处理此文件

```js
const loaderUtils = require("loader-utils");

module.exports = function(source) {
  // console.log(this.query) 拿到配置参数
  const options = loaderUtils.getOptions(this);
  if (options) {
    // origin 要被替换的旧内容；replace 新内容
    source = source.replace(
      options.origin,
      options.replace
    );
  }
  this.callback(null, source);
};
```

所谓 [loader](https://webpack.docschina.org/api/loaders/) 只是一个导出为函数的 JavaScript 模块。loader runner 会调用这个函数，然后把上一个 loader 产生的结果或者资源文件(resource file)传入进去。函数的 `this` 上下文将由 webpack 填充，并且 loader runner 具有一些有用方法，可以使 loader 改变为异步调用方式，或者获取 query 参数。

第一个 loader 的传入参数只有一个：资源文件(resource file)的内容。compiler 需要得到最后一个 loader 产生的处理结果。这个处理结果应该是 `String` 或者 `Buffer`（被转换为一个 string），代表了模块的 JavaScript 源码。另外还可以传递一个可选的 SourceMap 结果（格式为 JSON 对象）。

> 如果是单个处理结果，可以在**同步模式**中直接返回。如果有多个处理结果，则必须调用 `this.callback()`。在**异步模式**中，必须调用 `this.async()`，来指示 loader runner 等待异步结果，它会返回 `this.callback()` 回调函数，随后 loader 必须返回 `undefined` 并且调用该回调函数。

其中使用了 loader-utils 中提供的 getOptions 方法来提取给定 loader 的 option，这也是官方推荐使用的，如果不用这个包我们需要使用原生的 API： `this.query` 去拿，具体可以参考 [Webpack Loader API](https://webpack.docschina.org/api/loaders/#this-query)。

**3）编写配置**

webpack.config.js 使用我们编写好的 Loader

```js
const path = require("path");

module.exports = {
  mode: "development",
  entry: "./src/index.js",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "[name].js",
  },
  resolveLoader: {
    // 当你引用一个Loader的时候它会先去查找node_modules，如果找不到再去./loaders找
    modules: ["node_modules", "./loaders"],
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        use: [
          {
            loader: "replaceLoader",
            options: {
              origin: "http",
              replace: "https",
            },
          },
        ],
      },
    ],
  },
};
```

在 `rules` 中我们 `use` 了自己的 Loader，并且传入了 `options`，想要把字符串 http 替换为 https。运行 `webpack` 命令打包，输出的打包结果如下

![](https://gitee.com/dunizb/imgcloud/raw/master/webpack-Loader-Plugin-1.png)

可以看到，内容全部变成了大写。那么我们既想替换指定字符串，又想把内容变成大写呢？只需要把两个 Loader 都配置即可

```js
module: {
  rules: [
    {
      test: /\.js$/,
      use: [
        {
          // loader: path.resolve(__dirname, './loaders/replaceLoader.js'),
          loader: "replaceLoaderAsync",
        },
        {
          // loader: path.resolve(__dirname, './loaders/replaceLoaderAsync.js'),
          loader: "replaceLoader",
          options: {
            origin: "http",
            replace: "https",
          },
        },
      ],
    },
  ];
}
```

这样配置后，会先执行后面的 Loader 先把指定字符串替换掉，再把内容大写。

> Loader 示例完整源码：[GitHub](https://github.com/dunizb/CodeTest/tree/master/Webpack/make-loader)

## 3、如何编写一个 Plugin

Plugin 和 Loader 不一样，一个插件必须是一个 Class，因为我们在使用插件的时候都是 `new xxxx()` 的形式。

### 3.1 先定义需求

需求：我们在打包的时候需创建一个版权文件

### 3.2 编写插件

新建一个 copyright-webpack-plugin.js，先写好结构代码，内容如下

```js
// 生成一个版权文件
class CopyrightWebpackPlugin {
  constructor() {
    console.log("插件被使用了");
  }
  apply(compiler) {
    // 调用插件的时候会调用此方法
    // ...
  }
}
module.exports = CopyrightWebpackPlugin;
```

apply 方法就是我们逻辑代码处理的方法，后面我们再完善，调用这个插件会输出一句”插件被使用了“。

接着我们在 webpack.config.js 中把插件配置上

```
const path = require('path')
const CopyrightWebpackPlugin = require('./plugins/copyright-webpack-plugin')

module.exports = {
    mode: 'development',
    entry: './src/index.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: '[name].js'
    },
    plugins: [
        new CopyrightWebpackPlugin()
    ]
}
```

当使用 webpack 编译的时候控制台就会输出“插件被使用了”。

![](https://gitee.com/dunizb/imgcloud/raw/master/webpack-Loader-Plugin-2.png)

继续补充 apply 方法

```js
// ...
// 调用插件的时候会调用此方法
// compiler 是webpack的实例
apply(compiler) {
    // emit钩子是生成资源到 output 目录之前。异步钩子
    // compilation存放了这次打包的所有内容
    compiler.hooks.emit.tapAsync('CopyrightWebpackPlugin', (compilation, cb) => {
        // 添加copyright.txt
        compilation.assets['copyright.txt'] = {
            source: function() {
                return 'Copyright by Dunizb'
            },
            size: function() { // 文件大小,长度
                return 20;
            }
        }
        cb(); // 最后一定要调用
    })
}
```

更多[compiler.hooks](https://webpack.docschina.org/api/compiler-hooks/)，如果是同步时刻就直接调用`tap`方法。

编译结果如下，可以看到打包多出来一个 copyright.txt 文件，大小为 20 bytes。并且在最终打包出来的 dist 目录下生成了一个 以“Copyright by Dunizb”为内容的名为 copyright.txt 的文件。

![](https://gitee.com/dunizb/imgcloud/raw/master/webpack-Loader-Plugin-3.png)

好了，编写 Loader 和 Plugin 基本方法就是这样了，剩下就是自己根据实际业务需求去创造自己工具。

> Plugin 示例完整源码：[GitHub](https://github.com/dunizb/CodeTest/tree/master/Webpack/make-pulgin)

全文完。

---

关注公众号，第一时间接收最新文章。如果对你有一点点帮助，可以点喜欢点赞点收藏，还可以小额打赏作者，以鼓励作者写出更多更好的文章。

![关注公众号](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/关注名片-大礼包_横版二维码_2020-01-01-0.jpg)
