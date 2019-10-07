---
title: 在Koa.js中实现文件上传的接口
date: 2019-03-07 12:47:31
categories:
- 技术
tags:
- 前端
- Node
---

文件上传是一个基本的功能，每个系统几乎都会有，比如上传图片、上传Excel等。那么在Node Koa应用中如何实现一个支持文件上传的接口呢？本文从环境准备开始、最后分别用 [Postman](https://www.getpostman.com/) 和一个HTML页面来测试。
<!-- more -->

## 环境准备
首先当然是要初始化一个Koa项目了，安装 Koa、koa-router 即可。
```
npm install koa koa-router
```
设置图片上传目录，把图片上传到指定的目录中，在 app 路径下新建 public 文件夹，目录结构如下：
```
koa-upload/
--app
----public
------uploads
----index.js
--package.json
```

编写 index.js 
```javascript
const koa = require('koa')
const app = new koa()

router.post('/upload', ctx => {
    ctx.body = 'koa upload demo'
})
app.use(router.routes());

app.listen(3000, () => {
    console.log('启动成功')
    console.log('http://localhost:3000')
});
```

然后启动，确保这一步没有问题。

## 使用 koa-body 中间件获取上传的文件

[koa-body](https://www.npmjs.com/package/koa-body) 支持文件、json、form格式的请求体，安装 koa-body
```
npm install koa-body
```

设置 koaBody 配置参数，index.js
```javascript
const koa = require('koa')
const koaBody = require('koa-body')
const path = require('path')
const app = new koa()

app.use(koaBody({
    // 支持文件格式
    multipart: true,
    formidable: {
        // 上传目录
        uploadDir: path.join(__dirname, 'public/uploads'),
        // 保留文件扩展名
        keepExtensions: true,
    }
}));
... ...
```

接下来完善 `/upload` 路由，获取文件，然后直接返回文件路径
```javascript
router.post('/upload', ctx => {
    const file = ctx.request.files.file
    ctx.body = { path: file.path }
})
```

这样我们其实已经可以进行文件上传，并把文件上传到 `public/uploads` 中了，我们用 Postman 来测试一下。

## 使用 Postman 测试

打开 Postman，输入 `http://localhost:3001/upload`，选择 `POST` 方法，并且选择文件用 Body 来传输，并且选择 `form-data` 格式，然后在 KEY 中选择 file类型。

![](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201903/koa-upload-20191007180046.png)

然后就可以选择图片进行上传了,上传成功后就可以看到 uploads 文件夹下有利一个图片了，并且输出量图片的路径。

## 使用 koa-static 中间件生成图片链接

直接返回图片的本地路径在实际上是没什么用的，我们应该返回一个http链接的图片地址，点击地址就可以查看图片。

借助 [koa-static](https://www.npmjs.com/package/koa-static) 中间件可以帮助我们生成一个静态服务，它指定一个文件夹，文件夹下所有的文件都可以通过 http服务来访问。

安装：`npm install koa-static` 并注册到 app 上，我们把他注册在 koaBody 中间件的前面，把 public 设置为静态文件目录。
```javascript
const koaStatic = require('koa-static')
... ...
app.use(koaStatic(path.join(__dirname, 'public')))
```

启动程序，这样 public 下的文件就可以使用HTTP服务来大开了，我们可以打开之前上传的图片：`http://localhost:3001/uploads/upload_65c1d26e5a47870cf4011aad1243fce0.png`，可以在浏览器中直接显示了。

然后我们改造一下 upload 路由的实现，让它生成图片链接返回给客户端

```javascript
router.post('/upload', ctx => {
    const file = ctx.request.files.file
    const basename = path.basename(file.path)
    ctx.body = { "url": `${ctx.origin}/uploads/${basename}` }
})
```

`basename` 可以拿到文件的文件名和扩展名，`ctx.origin` 拿到服务器的域名，即诸如 localhost:3001，但我们不能写死。

再用 Postman 测试一下，即可看到返回的 图片URL了，点击可以直接打开。

![](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201903/koa-upload-20191007183425.png)

## 编写前端页面上传文件

前面我们用 Postman 模拟了上传文件进行测试，虽然可以高效的测试我们编写的后端接口，但是我们前端有些同学可能通常更熟悉前端页面的方式测试，那么我们来写一个表单页面来测试。

在 public 中新建 `upload.html` 文件作为测试页面。

```html
<form action="/upload" method="POST" enctype="multipart/form-data">
    <input type="file" name="file">
    <button type="submit">上传</button>
</form>
```

这是传统的表单提交，我们实际工作中这样的代码可能已经不常见了，`action` 就是我们的提交到的接口，`enctype="multipart/form-data"` 就是指定上传文件格式。input 的 name 属性一定要等于`file`，因为我们接受的字段名是 file。

然后我们用HTTP服务打开这个页面：`http://localhost:3001/upload.html`，因为我们整个 public 目录已经是一个静态HTTP服务目录了，里面的所有文件都可以通过HTTP访问。

![](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201903/koa-upload-20191007184601.png)

选择文件，点击上传，上传成功后可以看到返回了文件地址

![](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201903/koa-upload-20191007184711.png)

完！

*****

如果喜欢我的博客，如果对你恰好有所帮助，可以打赏1块钱鼓励我哈。还可以关注我的微信公众号号（做工程师不做码农），第一时间接受博客推送。

![做工程师不做码农](https://i.loli.net/2019/10/07/YIaqB24OoR7HxdV.png)
