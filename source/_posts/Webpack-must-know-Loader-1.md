---
title: Webpack必知必会之Loader篇（上）
date: 2018-04-25 23:06:22
categories:
- 和我一起读书
tags:
- 前端工程化
- Webpack
---
<img src="https://doc.webpack-china.org/cd0bb358c45b584743d8ce4991777c42.svg" style="background-color:red;padding:10px;">

为什么需要Loader？Webpack 是一个打包模块化的JavaScript的工具，在 Webpack 里一切文件皆模块，通过 `Loader` 转换文件，通过Plugin 注入钩子，最后输出由多个模块组合成的文件。在 Webpack 眼里，只有 JavaScript 文件才是原生，其他类型文件都需要经过转换才能被处理，而这种转换机制就是`Loader`机制。
<!-- more -->
## 一、使用 Loader

有如下配置：
```js
const path = require('path');
module.exports = {
    // JavaScript 执行入库文件
    entry: './main.js',
    output: {
        // 将所有依赖的模块合并输出到一个 bundle.js 文件中
        filename: 'bundle.js',
        // 将输出的文件都放到 dist 目录下
        paht: path.resolve(__dirname, './dist'),
    }，
    module: {
        rules: [
            {
                // 用正则表达式去匹配要用改Loader转换的CSS文件
                test: /\.css$/
                use: ['style-loader', 'css-loader?minimize'],
            }
        ]
    }
};
```
Loader 可以看做具有文件转换功能的翻译员，配置里的 `module.rules` 数组配置了一组规则，告诉 Webpack 在遇到哪些文件时使用哪些 Loader 去加载和转换。如上述配置告诉 Webpack，在遇到以`.css`结尾的文件时，先使用`css-loader`读取CSS文件，再由`style-loader`将CSS的内容注入到JavaScript里。在配置 Loader 时需要注意：
- `use`属性的值需要是一个由 Loader 名称组成的数组，Loader 的执行顺序是由后到前的；
- 每个 Loader 都可以通过 `URL querystring` 的方式传入参数，例如 css-loader?minimize 中的 minimize 告诉 css-loader 要开启 CSS压缩。

经过上面的配置，执行构建后，main.css 中写的CSS被注入到 bundle.js 中了，而不会额外生成一个CSS文件，这就是 `style-loader` 的功劳，它的工作原理大概是将CSS的内容用JavaScript例的字符串存储起来，在网页执行JavaScript时通过DOM操作，动态的向`HTML head`标签里插入`HTML style`标签。  

也许你认为这样做会导致JavaScript文件变大并且加载网页的时间变长，想让Webpack 单独输出CSS文件，这时你可以通过Webpack Plugin 机制来实现。

向Loader 传入属性的方式除了可以通过 `querystring` 实现，还可以通过 `Object`实现，以上 Loader 配置可以修改为如下形式：
```js
use: [
    'style-loader',
    {
        loader: 'css-loader',
        options: {
            miniminze: true,
        }
    }
]
```
除了在 `webpack.config.js`配置文件中配置 Loader，还可以在源码中指定用什么 Loader去处理文件。以加载 CSS 文件为例，原来`main.js`内容如下：
```js
// 通过CommonJS规范导入CSS模块
require('./main.css');
// 通过CommonJS规范导入 show 函数
require('./show.js');
// 执行show 函数
show('Webpack');
```
我们也可以直接在导入的时候进行Loader处理：
```js
require('style-loader!css-loader?minimize!./main.css');
```
这样就能指定对`./main.css`这个文件先采用 css-loader 再采用 style-loader 进行转换。

## 二、配置 Loader

`rules`配置模块的读取和解析规则，通常用来配置 Loader。其类型是一个数组，数组里的每一项都描述了如何处理部分文件。配置一项`rules`时大致可以通过一下方式来完成。
- 条件匹配：通过test、include、exclude三个配置项来选中Loader要应用规则的文件。
- 应用规则：对选中的文件通过 `use` 配置项来应用Loader，可以只应用一个Loader或者按照从后往前的顺序应用一组Loader，同时可以分别向Loader传入参数。
- 重置顺序：一组Loader的执行顺序默认是从右往左执行的，通过 `enforce`选项可以将其中一个Loader的执行顺序放到最前或者最后。

下面通过一个例子来说明具体的使用方法：
```js
module: {
    rules: [
        {
            // 命中JavaScript文件
            test: /\.js$/,
            // 用 babel-loader 转换JavaScript文件
            // ?cacheDirectory表示给babel-loader的参数，用于缓存babel-loader的编译结果，加快重新编译的速度
            use: ['babel-loader?cacheDirectory'],
            // 只命中 srcc 目录下的 JavaScript文件，加快Webpack的搜索速度
            include: path.resolve(__dirname, src)
        },
        {
            // 命中 SCSS 文件
            test: /\.scss$/,
            // 使用一组 Loader 去处理 SCSS文件
            // 处理顺序为从后到前，即先交给 sass-loader处理，再将结果交给css-loader，最后交给style-loader
            use: ['style-loader', 'css-loader', 'sass-loader'],
            // 排除 node-modules 目录下的文件
            exclude: path.resolve(__dirname, 'node_modules')
        },
        {
            // 对非文本文件采用 file-loader加载
            test: /\.(gif|png|jpe?g|eot|woff|ttf|svg|pdf)$/,
            use: ['file-loader'],
        }
    ]
}
```
在Loader需要传入很多参数时，我们可以通过一个 Obejct 来描述，例如上面的`babel-loader`配置中有如下代码：
```js
use: [
    {
        loader: 'babel-loader',
        options: {
            cacheDirectory: true,
        },
        // enforce:'post' 的含义是将该Loader的执行顺序放到最后
        // enforce的值还可以是 pre，代表将Loader的执行顺序放到最前面
        enforce: 'post'    
    },
    // 省略其他Loader
]
```
在上面的例子中，test、include、exclude这三个命中文件的配置项之传入了一个字符串或正则，其实他们也支持数组类型，使用如下：
```js
{
    test: [
        /\.jsx?$/,
        /\.tsx?$/
    ],
    include: [
        path.resolve(__dirname, ''src),
        path.resolve(__dirname, 'tests'),
    ],
    exclude: [
        path.reslove(__dirname, 'node_modules'),
        path.resolve(__diename, 'bower_modules'),
    ]
}
```
数组中的每项之间是“或”的关系，即文件的路径只要满足数组中的任何一项条件，就会被命中。

## 三、ResolveLoader

ResolveLoader 用来告诉 Webpack 如何去寻找 Loader，因为在使用 Loader 时是通过其报名称去引用的，Webpack 需要根据配置的Loader包名称去找到Loader对的实际代码，以调用Loader去处理源文件。

ResolveLoader 的默认配置如下：
```js
module.exports = {
    resolveLoader: {
        // 去哪个目录下寻找Loader
        modules: ['node_modules'],
        // 入口文件的后缀
        extensions: ['.js', '.json'],
        // 指明入口文件位置的字段
        mainFields: ['loader', 'main']
    }
}
```
该配置项常用于加载本地的Loader。

## 四、优化Loader配置

由于Loader对文件的转换操作很耗时，所以需要尽可能少的文件被Loader处理。前面我们使用Loader时通过test、include、exclude三个配置项来命中Loader 要应用规则的文件。为了尽可能少地让文件被Loader处理，可以通过include去命中只有哪些文件需要被处理。

以采用ES6的项目为例，在配置 babel-loader 时可以这样：
```js
module: {
    rules: [
        {
            // 如果项目源码中只有 js 文件，就不要写成/\.jsx$/，以提升正则表达式性能
            test: /\.js$/,
            // babel-loader支持缓存转换的结果，通过 cacheDirectory 选项开启
            use: ['babel-loader?cacheDirectory'],
            // 只对项目根目录下的 src 目录中的文件采用 babel-loader
            include: path.resolve(__dirname, src)
        }
    ]
}
```
我们可以适当调整项目的目录结构，以方便在配置 Loader 时通过 include 缩小命中的范围。


******
> 本文内容摘自吴浩麟著《深入浅出Webpack》一书，版权归原作者所有