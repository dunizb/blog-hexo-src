---
title: 详解Webpack的loader和plugin编写
date: 2018-12-21 19:08:11
categories:
- 技术
tags:
- Webpack
---
本文介绍如何编写自己的Loader和Plugins
<!-- more -->

## 1、Loader 和 Plugin 的区别
### 1.1 Loader
loader是文件加载器，能够加载资源文件，并对这些文件进行一些处理，诸如编译、压缩等，最终一起打包到指定的文件中。

Loader的特点：
- 处理一个文件可以使用多个loader，loader的执行顺序是和本身的顺序是相反的，即最后一个loader最先执行，第一个loader最后执行。
- 第一个执行的loader接收源文件内容作为参数，其他loader接收前一个执行的loader的返回值作为参数。最后执行的loader会返回此模块的JavaScript源码
- loader 可以是同步的，也可以是异步的。
- loader 是用node.js来跑，可以做一切可能的事情。
- loader 接收query参数。这些参数会传入 loaders内部作为配置来用。
- loader 可以用npm 发布或者安装。
- 除了用 package.json 的 main 导出的 loader 外， 一般的模块也可以导出一个loader。

### 1.2 Plugin

plugin 用于扩展webpack的功能。它直接作用于 webpack，扩展了它的功能。当然loader也时变相的扩展了 webpack ，但是它只专注于转化文件（transform）这一个领域。而plugin的功能更加的丰富，而不仅局限于资源的加载。

在 Webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。

### 1.3 plugin和loader的区别是什么？
对于loader，它就是一个转换器，将A文件进行编译形成B文件，这里操作的是文件，比如将A.scss或A.less转变为B.css，单纯的文件转换过程

plugin是一个扩展器，它丰富了wepack本身，针对是loader结束后，webpack打包的整个过程，它并不直接操作文件，而是基于事件机制工作，会监听webpack打包过程中的某些节点，执行广泛的任务。

在使用上：loader 不需要 import 导入就可以使用，而 plugin 需要 import 导入才能使用。

## 2、如何编写一个Loader
所谓 [loader](https://webpack.docschina.org/api/loaders/) 只是一个导出为函数的 JavaScript 模块。loader runner 会调用这个函数，然后把上一个 loader 产生的结果或者资源文件(resource file)传入进去。函数的 `this` 上下文将由 webpack 填充，并且 loader runner 具有一些有用方法，可以使 loader 改变为异步调用方式，或者获取 query 参数。

第一个 loader 的传入参数只有一个：资源文件(resource file)的内容。compiler 需要得到最后一个 loader 产生的处理结果。这个处理结果应该是 `String` 或者 `Buffer`（被转换为一个 string），代表了模块的 JavaScript 源码。另外还可以传递一个可选的 SourceMap 结果（格式为 JSON 对象）。

如果是单个处理结果，可以在**同步模式**中直接返回。如果有多个处理结果，则必须调用 `this.callback()`。在**异步模式**中，必须调用 `this.async()`，来指示 loader runner 等待异步结果，它会返回 `this.callback()` 回调函数，随后 loader 必须返回 `undefined` 并且调用该回调函数。

在src下编写入口的index.js 文件，内容如下

```js
console.log('hello webapck')
console.log('http://blog.dunizb.com')
```

我们需要编写一个Loader来处理此文件，把字符串 hello 替换为我们配置传入的字符串（options.name）

同步模式简单[示例](https://github.com/dunizb/CodeTest/tree/master/Webpack/make-loader/loaders/replaceLoader.js)

```js
const loaderUtils = require('loader-utils')
module.exports = function(source) {
    // console.log(this.query)
    const options = loaderUtils.getOptions(this)
    const result = source.replace('hello', options.name)
    this.callback(null, result)
}
```

异步模式简单[示例](https://github.com/dunizb/CodeTest/tree/master/Webpack/make-loader/loaders/replaceLoaderAsync.js)

```
const loaderUtils = require('loader-utils')
module.exports = function(source) {
    const options = loaderUtils.getOptions(this)
    const callback = this.async();
    setTimeout(() => {
        const result = source.replace('hello', options.name)
        callback(null, result);
    }, 1000);
}
```

webpack.config.js 使用你编写好的Loader

```
const path = require('path')

module.exports = {
    mode: 'development',
    entry: './src/index.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: '[name].js'
    },
    resolveLoader: {
        modules: ['node_modules', './loaders'], // 当你引用一个Loader的时候它会先去查找node_modules，如果找不到再去./loaders找
    },
    module: {
        rules: [{
            test: /\.js$/,
            use: [
                {
                    // loader: path.resolve(__dirname, './loaders/replaceLoaderAsync.js'),
                    loader: 'replaceLoaderAsync',
                    options: {
                        name: 'https'
                    }
                },
                {
                    // loader: path.resolve(__dirname, './loaders/replaceLoader.js'),
                    loader: 'replaceLoader',
                    options: {
                        name: '你好'
                    }
                }
            ]
        }]
    }
}
```

## 3、如何编写一个Plugin

Plugin和Loader不一样，一个插件必须是一个Class，因为我们在使用插件的时候都是 `new xxxx()` 的形式。加入我们在build的时候需要创建一个版权文件，那么我们写一个Plugin来处理。

定义插件，[源代码](https://github.com/dunizb/CodeTest/tree/master/Webpack/make-plugin/)

```
// 生成一个版权文件
class CopyrightWebpackPlugin {
    constructor() {
        console.log('插件被使用了')
    }
    apply(compiler) {    // 调用插件的时候会调用此方法
        // ...
    }
}
module.exports = CopyrightWebpackPlugin
```

配置 webpack.config.js

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

当使用webpack编译的时候控制台就会输出“插件被使用了”。

继续补充 apply 方法

```
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

编译结果如下，可以看到打包多出来一个 copyright.txt 文件，大小为20 bytes。并且在最终打包出来的 dist 目录下生成了一个 以“Copyright by Dunizb”为内容的名为 copyright.txt 的文件。

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/52528913.png)


全文完。

*************
关注公众号，第一时间接收最新文章。如果对你有一点点帮助，可以点喜欢点赞点收藏，还可以小额打赏作者，以鼓励作者写出更多更好的文章。
![关注公众号](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/关注名片-大礼包_横版二维码_2020-01-01-0.jpg)