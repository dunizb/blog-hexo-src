---
title: 3个非常有用的Node.js软件包
date: 2020-04-20 21:01:27
categories:
  - 技术
tags:
  - 前端
  - NodeJS
  - 开源推荐
---

Node.js已成为IT不可或缺的一部分。有了自己的软件包管理器NPM，Node可以发现许多非常有用的库和框架。

在本文中，我将向您展示一些使用Node.js构建复杂动态应用程序的可能性。

<!-- more -->

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202004/3-Nodejs-packages/1.png)

## 1. Chalk：在终端中设置输出样式

在开发新的Node.js应用程序期间 `console.log` 必不可少，不管我们用它来输出错误、系统数据还是函数和co的输出。但是，这确实会造成一些混乱，因为默认情况下 `console.log` 函数在终端中输出纯白色文本。

Chalk改变了这一点。

只需像往常一样从https://www.npmjs.com/package/chalk用 `npm install chalk` 安装Chalk就可以了。

这是一个代码示例，下面是我的终端的实际情况。

```javascript
const chalk = require(‘chalk’)
// just blue font
console.log(chalk.blue(‘this is lit’))
// blue & bold font, red background (bg = background)
console.log(chalk.blue.bgRed.bold(‘Blue & Bold on Red’))
// blue font, red background
console.log(chalk.blue.bgRed(‘Regular Blue on Red’))
// combining multiple font colors
console.log(chalk.blue(‘Blue’) + ‘ Default’ + chalk.red(‘ Red’))
// Underlining text
console.log(chalk.red(‘There is an ‘, chalk.underline(‘Error’)))
// Using RGB-colors
console.log(chalk.rgb(127, 255, 0).bold(‘Custom green’))
```

输出：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202004/3-Nodejs-packages/2.png)

## 2. Morgan — 记录HTTP请求中的所有重要信息

同样，这在应用程序的开发中特别有用。因为HTTP请求是数字世界的心跳，所以完全控制对应用程序中影响它们的所有内容的重要性如此重要。

Morgan提供了有关此的重要信息。

像往常一样，通过 `npm install morgan` 从https://www.npmjs.com/package/morgan获取它，在morgan中，我们可以定义我们想要获得的关于请求的信息。

正如在描述的文档中所述，只需将其传递到morgan中间件中，因此我们将在下面的代码示例中使用它。

```javascript
const express = require(‘express’)
const morgan = require(‘morgan’)
const app = express()
app.use(
morgan(
 ‘:method :url :status :response-time ms’
))
app.get(‘/’, function(req, res) {
  res.send(‘hello, world!’)
})
app.listen(8080)
```

因此，我们希望获得有关传入HTTP请求的以下详细信息：方法，请求的URL，请求的状态以及响应所花费的时间。

在浏览器中打开网站时，运行此代码应导致以下输出：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202004/3-Nodejs-packages/3.png)

当我们在浏览器中打开页面时，它总是向服务器发出GET-Request请求，因为我们请求了 `/`，morgan也会显示这个，以及我们的“hello, world！”站点被成功交付——这意味着状态码200。整个执行过程大约需要2.3毫秒，这相当快。

但我们不仅要求我们的网站，而且浏览器也总是要求一个favicon，找不到——错误状态404。

让我们来衡量一个实验：我们更改代码，使每个响应之前有200毫秒的停顿。以下是代码中的更改：

```javascript
app.get(‘/’, function(req, res) {
  setTimeout(function() {
    res.send(‘hello, world!’)
  }, 200)
})
```

现在，当我们再次在浏览器中请求页面时，morgan将记录此内容：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202004/3-Nodejs-packages/4.png)

现在，响应花费了200多个毫秒——就像我们想要的那样。但最后，页面再次成功交付，除了favicon，我们现在还没有，而且只用了几个MS，因为我们只延迟了对 `/` 路由的请求。

## 3. Cheerio：使用类似jQuery的语法处理服务器上已经存在的DOM

特别是当我们不提供静态HTML文件而是动态网站时，Cheerio非常实用。我们可以在浏览器的请求和响应之间直接修改请求的HTML代码，而客户端不会知道。由于类似jQuery的语法，这特别容易。当然，您也可以使用Cheerio做爬虫和其他许多操作。

使用 `npm install cheerio` 从https://www.npmjs.com/package/cheerio安装。通过Cheerio，我们可以获得有关HTML结构和内容的信息：

```javascript
const template = `
  <div id=”main”>
    <h1 id=”message”>Welcome on our site</h1>
  </div>
`
const $ = cheerio.load(template)
console.log($(‘h1’).text()) // Welcome on our site
```

将HTML添加到现有模板：

```javascript
let template = `
  <div id=”main”>
    <h1 id=”message”>Welcome on our site</h1>
  </div>
`
const $ = cheerio.load(template)
$(‘div’).append(‘<p class=”plum”>Paragraph</p>’)
template = $.html()
```

现在的模板：

```html
<div id="main"> 
  <h1 id="message">Welcome on our site</h1>   
  <p class="plum">Paragraph</p>
</div>
```

但是Cheerio最常用的一种情况可能是随后将内容写入模板：

```javascript
let template = `
  <div id=”main”>
    <h1 id=”message”></h1>
  </div>
`
const $ = cheerio.load(template)
$(‘h1’).append(‘New welcome message!’)
template = $.html()
```

现在的模板：

```html
<div id=”main”> 
  <h1 id=”message”>New welcome message!</h1> 
</div>
```

而且，您可以使用Cheerio做更多的事情。只需查看文档即可！

****

原文：https://medium.com/javascript-in-plain-english/3-incredibly-useful-node-js-packages-that-you-should-try-3f9f8c427795

作者：[Louis Petrik](https://medium.com/@louispetrik?source=follow_footer--------------------------follow_footer-)


****

如果对你有所启发和帮助，可以点个关注、收藏、转发，也可以留言讨论，这是对作者的最大鼓励。

作者简介：Web前端工程师，全栈开发工程师、持续学习者。

关注公众号 **《前端外文精选》**，私信回复：大礼包，送某网精品视频课程网盘资料，准能为你节省不少钱！
