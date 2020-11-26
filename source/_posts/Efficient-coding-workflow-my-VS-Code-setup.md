---
title: 高效的编码：我的VS Code设置
date: 2020-03-16 22:31:08
img: https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/vscode-setting/1.png
categories:
  - 工具
tags:
  - VS Code
---

代码编辑器很多，有些是免费的，有些是付费的。其中最喜欢的代码编辑器是 Visual Studio Code。它是免费的，并具有强大的功能，我陆续抛弃了 Atom、Sublime Text 以及也很强大的 WebStorm。

<!-- more -->

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/vscode-setting/1.png)

今天，我将分享我最喜欢的代码编辑器设置，用于我的 Web 开发。我将从代码编辑器的外观开始。毕竟外观颜值很重要。

## **🎨** 主题

我最常用的 VS Code 主题是[Snazzy Operator](https://marketplace.visualstudio.com/items?itemName=aaronthomas.vscode-snazzy-operator)，目前正在使用。

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/vscode-setting/2.jpeg)

此主题基于 [hyper-snazzy](https://github.com/sindresorhus/hyper-snazzy) 并针对与 [Operator Mono](https://www.typography.com/fonts/operator/overview) 字体一起使用进行了优化。我喜欢 😍 这个主题。

⭐ 我之前使用过的其他一些主题：

- [Oceanic Next](https://marketplace.visualstudio.com/items?itemName=naumovs.theme-oceanicnext) - 我使用了 Oceanic Next (dimmed bg)
- [emedy](https://marketplace.visualstudio.com/items?itemName=robertrossmann.remedy) - 我使用了 Remedy Dark (straight)

## **✒** 字体

对我的代码编辑器来说，另一个重要的事情是，我用于代码编辑器的字体是 [JetBrains Mono](https://www.jetbrains.com/lp/mono/)。这是带有连字支持的免费字体。

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/vscode-setting/3.png)

连字是一种新的字体格式，支持符号装饰，而不是`=` `>`、`<` `=`。

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/vscode-setting/4.png)

在使用 [JetBrains Mono](https://www.jetbrains.com/lp/mono/) 之前，我使用了**Operator Mono**。这也是一个不错的字体。

⭐ 我以前使用过的其他一些字体：

- [Operator Mono](https://www.cufonfonts.com/font/operator-mono) - 支持连字。
- [Fira Code](https://github.com/tonsky/FiraCode) - 免费并支持连字。
- [Dank Mono](https://dank.sh/) - 付费并支持连字。

🌟🌟🌟 您要使用我的设置，使用我的 VS Code 字体吗？在 VS Code 中，按 **Ctrl + P**，输入 **settings.json** 并打开该文件。现在，用我的给定值替换下面的属性值。

```json
{
  "workbench.colorTheme": "Snazzy Operator",
  "editor.fontFamily": "'JetBrains Mono', Consolas, 'Courier New', monospace",
  "editor.fontLigatures": true,
  "editor.fontSize": 14,
  "editor.lineHeight": 22,
  "editor.letterSpacing": 0.5,
  "editor.fontWeight": "400",
  "editor.cursorStyle": "line",
  "editor.cursorWidth": 5,
  "editor.cursorBlinking": "solid"
}
```

## 📁 图标

文件图标增强了 VS Code 的外观，主要是它可以帮助我们通过给定的图标区分不同的文件和文件夹。对于我的 VS Code，我使用两个文件图标：

- [Material Icon Theme](https://marketplace.visualstudio.com/items?itemName=PKief.material-icon-theme) - VS Code 最受欢迎的图标主题之一。
- [Material Theme Icons](https://marketplace.visualstudio.com/items?itemName=Equinusocio.vsc-material-theme-icons) - 目前正在使用。

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/vscode-setting/5.png)

## 我使用的扩展

### [**🟠 Auto Rename Tag**](https://marketplace.visualstudio.com/items?itemName=SimonSiefke.auto-rename-tag)

自动重命名配对的 HTML / XML 标签，也可以在 JSX 中使用。

在 **settings.json** 文件中的 `auto-rename-tag.activationOnLanguage` 中添加一项以设置扩展名将被激活的语言。默认情况下，它是**[“ *”]**，将为所有语言激活。

```js
"auto-rename-tag.activationOnLanguage": ["html", "xml", "php", "javascript"]
```

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/vscode-setting/6.gif)

### 🟠 [**Bracket Pair Colorizer 2**](https://marketplace.visualstudio.com/items?itemName=CoenraadS.bracket-pair-colorizer-2)

此扩展名允许用颜色标识匹配的括号，用户可以定义要匹配的符号，以及要使用的颜色。

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/vscode-setting/7.png)

### 🟠 [**Color Highlight**](https://marketplace.visualstudio.com/items?itemName=naumovs.color-highlight)

此扩展程序设置在文档中找到的 css / web 颜色的样式。
![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/vscode-setting/8.gif)

### 🟠 [**CSS Peek**](https://marketplace.visualstudio.com/items?itemName=pranaygp.vscode-css-peek)

- Peek：内联加载 css 文件并在那里进行快速编辑。（Ctrl + Shift + F12）
- Go to：直接跳转到 CSS 文件或在新的编辑器（F12）中打开
- Hover：在符号上悬停显示定义（Ctrl + hover）

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/vscode-setting/9.gif)

### 🟠 [**DotENV**](https://marketplace.visualstudio.com/items?itemName=mikestead.dotenv)

在 `.env` 文件中高亮显示键值对。

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/vscode-setting/10.png)

### 🟠 [**ES7 React/Redux/GraphQL/React-Native snippets**](https://marketplace.visualstudio.com/items?itemName=dsznajder.es7-react-js-snippets)

该扩展为您提供 ES7 中的 JavaScript 和 React / Redux 片段，以及 VS Code 的 Babel 插件功能。

### 🟠 [**Highlight Matching Tag**](https://marketplace.visualstudio.com/items?itemName=vincaslt.highlight-matching-tag)

突出显示匹配的开始或结束标签。

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/vscode-setting/11.gif)

我使用的扩展的样式：

```json
"highlight-matching-tag.styles": {
  "opening": {
    "left": {
      "custom": {
        "borderWidth": "0 0 0 1.5px",
        "borderStyle": "solid",
        "borderColor": "yellow",
        "borderRadius": "5px",
        "overviewRulerColor": "white"
      }
    },
    "right": {
      "custom": {
        "borderWidth": "0 1.5px 0 0",
        "borderStyle": "solid",
        "borderColor": "yellow",
        "borderRadius": "5px",
        "overviewRulerColor": "white"
      }
    }
  }
}
```

### 🟠 [**Image preview**](https://marketplace.visualstudio.com/items?itemName=kisstkondoros.vscode-gutter-preview)

悬停时显示图像预览。

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/vscode-setting/12.png)

### 🟠 [**Indent Rainbow**](https://marketplace.visualstudio.com/items?itemName=oderwat.indent-rainbow)

此扩展使文本前面的缩进着色，在每个步骤上交替使用四种不同的颜色。

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/vscode-setting/13.png)

### 🟠 [**REST Client**](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)

REST Client 允许您发送 HTTP 请求并直接在 Visual Studio Code 中查看响应。

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/vscode-setting/14.gif)

### 🟠 [**Settings Sync**](https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync)

使用 GitHub Gist 在多台机器上同步设置，代码片段，主题，文件图标，启动，键绑定，工作区和扩展。具体操作可以看我的这篇文章[《小技巧|同步你的 VSCode 设置及扩展插件，换机不用愁！》](https://blog.zhangbing.site/2019/11/02/%E5%90%8C%E6%AD%A5%E4%BD%A0VSCode%E8%AE%BE%E7%BD%AE%E5%8F%8A%E6%89%A9%E5%B1%95%E6%8F%92%E4%BB%B6-%E6%8D%A2%E6%9C%BA%E4%B8%8D%E7%94%A8%E6%84%81/)

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/vscode-setting/15.png)

### 🟠 [**TODO Highlight**](https://marketplace.visualstudio.com/items?itemName=wayou.vscode-todo-highlight)

在代码中突出显示 TODO，FIXME 和其他注释。

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/vscode-setting/16.png)

### 🟠 [**Version Lens**](https://marketplace.visualstudio.com/items?itemName=pflannery.vscode-versionlens)

显示 package.json 文件中每个软件包的最新版本。

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202003/vscode-setting/17.gif)

## 📃 Terminal 设置

我的操作系统是 Windows，我通过命令行使用 Git，所以我有一个 Git terminal，我用这个终端作为我的集成 terminal。我的 terminal 设置如下：

```js
"terminal.integrated.shell.windows": "C:\\Program Files\\Git\\bin\\bash.exe",
"terminal.integrated.fontFamily": "FuraMono Nerd Font",
"terminal.integrated.fontSize": 12,
"terminal.integrated.rightClickCopyPaste": true
```

## ✔ 有用的 VS Code 快捷键

我在日常生活中使用了一些重要的键盘快捷键，这些快捷方式使 Visual Studio Code 提高了我的工作效率。

- **Ctrl + P** ：转到文件，您可以在 Visual Studio Code 中移动到打开的文件/文件夹的任何文件。
- **Ctrl + `** ：在 VS Code 中打开 terminal
- **Alt + Down**：下移一行
- **Alt + Up**：上移一行
- **Ctrl + D**：将选定的字符移动到下一个匹配字符串上
- **Ctrl + Space**：触发建议
- **Shift + Alt + Down**：向下复制行
- **Shift + Alt + Up**：向上复制行
- **Ctrl + Shift + T**：重新打开最新关闭的窗口

感谢您的阅读和关注。
