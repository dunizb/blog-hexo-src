---
title: 使用GitHub Pages和GitHub Actions部署React应用
date: 2021-04-16 21:26:32
img: http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/react-app-using-github-pages-and-github-actions/banner.png
categories:
  - 前端框架
tags:
  - React
  - Github
summary: "本文的目的是重新创建该场景，并带领您完成解决我们一路上遇到的每个问题的过程"
---

## 介绍

我最近用 Create React App starter 模板创建了一个网站来演示我开发的一个 npm 包。我认为使用 GitHub 页面部署这个站点是非常简单的，然而，我错了。经过反复试验，我设法解决了这个问题。本文的目的是重新创建该场景，并带领您完成解决我们一路上遇到的每个问题的过程。

## 1.起点

让我们从一个共同的基础开始。我们先用 Create React App 工具创建一个 React 应用，同时将代码添加到 GitHub 仓库。我使用了以下命令来生成这个示例 React 应用。

```bash
npx create-react-app <project directory> --template typescript
```

此时，你的项目目录应该看起来像下面的截图。我没有添加或修改任何东西--这些是当我们运行上述 npx 命令时，开箱即生成的文件和文件夹。我只是通过运行 `npm run start` 命令来确保它在本地工作，仅此而已。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/react-app-using-github-pages-and-github-actions/1.png)

我已经把这些改动推送到了我的 [GitHub 仓库](https://github.com/ClydeDz/create-react-app-ghpages-demo)，如果你也在关注，你也可以这样做。如果你想比较一下，[这是](https://github.com/ClydeDz/create-react-app-ghpages-demo/tree/step-1)我的版本库现阶段的样子。

## 2.部署到 GitHub Pages

当我们运行 `npm run build` 命令时，Create React App 会将生产文件放入 `build` 目录中。然而，如果你看一下 `.gitignore` 文件，你会发现构建目录被添加到这个列表中，因此，你无法将这个文件夹的内容提交到 GitHub。那么，我们该如何发布我们的应用呢？

### GitHub Actions

让 GitHub Actions 来拯救我们吧！我们需要在每次代码提交时构建我们的应用程序，这就是[GitHub Actions](https://docs.github.com/en/actions)的作用。在你的应用程序的 `.github/workflows` 目录下创建一个名为 `build-deploy.yml` 的文件。将以下内容粘贴到这个 YAML 文件中。[这是](https://github.com/ClydeDz/create-react-app-ghpages-demo/tree/step-2)我的 GitHub 仓库在这个阶段的样子。

```yaml
name: Build & deploy

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build:
    name: Build
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Install Node.js
        uses: actions/setup-node@v1
        with:
          node-version: 13.x

      - name: Install NPM packages
        run: npm ci

      - name: Build project
        run: npm run build

      - name: Run tests
        run: npm run test

      - name: Upload production-ready build files
        uses: actions/upload-artifact@v2
        with:
          name: production-files
          path: ./build

  deploy:
    name: Deploy
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'

    steps:
      - name: Download artifact
        uses: actions/download-artifact@v2
        with:
          name: production-files
          path: ./build

      - name: Deploy to gh-pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./build
```

最近我写了这篇文章，解释了 GitHub Actions 的基本原理，这里就不多说了。总结一下，这个 YAML 文件定义了 GitHub Actions 中的工作流程。这个工作流会在每次推送变更到主分支或创建拉请求合并变更到主分支时被触发，它将构建 React 应用，并将 `build` 目录的内容部署到 `gh-pages` 分支。

关于 `${{ secrets.GITHUB_TOKEN }}`的快速注释——GitHub 自动创建一个 `GITHUB_TOKEN` 密钥以在您的工作流程中使用。因此，它具有对存储库的写访问权，因此，您可以更新 `gh-pages` 分支。

如果您继续学习，请将此文件提交到存储库。马上，您就会注意到 GitHub Pages 现在将基于您在工作流文件中的内容进行构建。如果您转到 GitHub 中的 Actions 选项卡，您将看到您的工作流正在执行，并且在一段时间后有望被标记为成功。请随意单击 UI 并探索 GitHub 存储库的这个区域。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/react-app-using-github-pages-and-github-actions/2.png)

假定状态显示为成功，此操作还将创建一个名为 `gh-pages` 的新分支，并将在其中部署生产就绪代码。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/react-app-using-github-pages-and-github-actions/3.png)

很简单，不是吗？

### GitHub Pages

现在我们已经将构建文件放到了不同的分支中，让我们继续启用 GitHub Pages。点击菜单中的**Settings**，然后向下滚动到 **GitHub Pages** 部分。

在这里，我们将配置网站内容的位置。 由于我们的构建文件已推送到 `gh-pages` 分支，因此请从下拉列表中进行选择。点击**Save**按钮，页面会刷新，当你向下滚动到这部分时，你会看到一个网址。点击该网址，即可看到网站。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/react-app-using-github-pages-and-github-actions/4.png)

等等，怎么了？我看不到 React 应用的输出，你能看到吗？

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/react-app-using-github-pages-and-github-actions/5.png)

您可能会看到一个空白的屏幕，并且如果打开控制台，则会看到很多错误。

> 提示：如果你没有看到空屏，而是看到 GitHub 的 404 信息，请等待几分钟，换个浏览器试试，最后，尝试清除缓存。由于这将是你第一次访问网站，它可能还没有在后台更新东西。

请注意它试图获取 JavaScript 和 CSS 文件的 URL——它使用的是基础 URL，但没有使用路径 `create-react-app-ghpages-demo`。显然，由于基础 URL 中不存在 JavaScript 或 CSS 文件，我们得到了一个 404 错误。

只有当你的项目站点使用的是 GitHub Pages，即格式为 `https://<username>.github.io/<project>/` 时，才会出现这个错误。如果你的版本库使用 `<username>.github.io` 的格式命名，那么启用 GitHub Pages 后可能不会出现上述错误。这是因为你的网站不再部署在根目录下，而是部署在更深一层的 `https://<username>.github.io/<project>/`。

那么，我们如何解决这个问题呢？让我们来看看。

## 3.设置首页值

打开这个应用的源代码，在 `package.json` 文件中，添加这个键值对，适当替换下面 URL 中的部分。

```
"homepage": "https://<username>.github.io/<project>/",
```

在我的实例中，这是我必须添加的内容：

```
"homepage": "https://clydedz.github.io/create-react-app-ghpages-demo/",
```

做完这个改动后，把它推送到 GitHub 上。这将触发一次构建和部署。

给它一两分钟，然后再次访问网站。现在你应该看到你的 React 应用已经启动并运行了。万岁！

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/react-app-using-github-pages-and-github-actions/6.png)

## 4.添加 React Router

接下来，我们就来看看在 React 应用中添加 Router 的常见场景。会不会无缝运行？还是会再次遇到错误？让我们一探究竟吧。

我将使用 React Router 来完成这个任务，我将输入以下命令来安装这个 npm 包。

```bash
npm install --save react-router-dom
```

我按照基本的例子添加了三个路由。这三条路由分别指向三个独立的 React 组件。[这是](https://github.com/ClydeDz/create-react-app-ghpages-demo/tree/step-4)我的 GitHub 仓库在添加 React Router 后的样子。

如果你运行 `npm run start` 命令，你将能够观察到一个非常奇怪的行为。

- 它的开头是http://localhost:3000/create-react-app-ghpages-demo，但页面只包含导航链接，没有其他内容。
- 点击“关于”链接将 URL 更新为http://localhost:3000/about，现在会显示一些内容。然而，由于URL中完全删除了 `create-react-app-ghpages-demo` 的值，我们已经不在正确的网站上了（硬刷新该 URL 会出现错误）。

无论如何将这些更改提交到 GitHub 上（你可能还需要更新你的单元测试）。在成功部署后，你应该也能在线复制这种行为。这显然不是很理想。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/react-app-using-github-pages-and-github-actions/7.gif)

## 5.解决路由错误

造成这种奇怪行为的原因是现在路由器认为网站是从根目录服务的。这是不正确的--演示应用程序是由一个子目录提供服务的--因此出现了不匹配。

要解决此问题，请更新以下代码行：

```
<Router>
```

到

```
<Router basename={process.env.PUBLIC_URL}>
```

`process.env.PUBLIC_URL` 的值将是 `/<project>`。basename 属性允许我们指定路由的实际基础 URL，在本例中，它将是子目录。

现在剩下的就是让我们测试该[演示网站](https://clydedz.github.io/create-react-app-ghpages-demo/)，并确认它可以像魅力一样工作。

就是这样！谢谢阅读。

---

翻译自[codeburst.io](https://codeburst.io/deploying-a-react-app-using-github-pages-and-github-actions-7fc14d380796)，作者：Clyde D'Souza
