---
title: 如何在Mac上安装Python 3 – Brew安装更新指南
date: 2021-04-15 17:43:25
img: http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/python-version-on-mac-update/banner.jpeg
categories:
  - 其它
tags:
  - Python
summary: "MacOS 预先安装了 Python。但是它是 Python 版本 2.7，现已弃用（Python 开发者社区已弃用）"
---

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/python-version-on-mac-update/banner.jpeg)

**MacOS 预先安装了 Python。但是它是 Python 版本 2.7，现已弃用（Python 开发者社区已弃用）。**

整个 Python 社区现在都开始使用 Python 3.x（撰写本文时的最新版本是 3.9）。 Python 4.x 即将发布，但将完全向后兼容。

<!-- more -->

如果尝试从 MacOS 终端运行 Python，甚至会看到以下警告：

![警告：不推荐使用Python 2.7。这个版本包含在 macOS 中是为了与旧版软件兼容。未来版本的 macOS 将不包含 Python 2.7。相反，我们推荐您在终端中使用“python3”。](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/python-version-on-mac-update/1.png)

在 Apple 决定将 Python 3.x 设置为默认值之前，您将必须自己安装它。

或者，您可以运行以下命令以打开 Python3：

```bash
python3
```

但你可能想安装一个合适的 Python 版本控制“shim”来跟踪各种版本，并对你使用的版本进行精细的控制。而本教程将告诉你如何做到这一点。

![顺便说一句，如果您想知道为什么我继续提到Python 3.x，则x是子版本（或开发人员称之为点发布的版本）的代名词。这表示Python 3的任何版本。](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/python-version-on-mac-update/1.jpeg)

## 如何使用 Homebrew 在 Mac 上安装最新版本的 Python

首先，您需要安装 Homebrew。

打开你的终端。你可以通过使用 MacOS spotlight（command+space）并输入“terminal”来实现。

现在您已进入命令行，您可以通过运行以下命令来安装最新版本的 Homebrew：

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

你的终端将要求超级用户级别的访问。你需要输入密码来运行这个命令。这与你登录 Mac 时输入的密码相同。输入后按回车键。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/python-version-on-mac-update/2.png)

Homebrew 会要求你确认你要安装以下内容。你必须按回车键才能继续。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/python-version-on-mac-update/3.png)

## 如何安装 pyenv 来管理您的 Python 版本

现在让我们花点时间来安装 PyEnv。这个库将帮助您在不同版本的 Python 之间进行切换。(万一您因为某些原因需要运行 Python 2.x，并且期待 Python 4.0 的到来。)

运行以下命令：

```bash
brew install pyenv
```

![PyEnv安装](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/python-version-on-mac-update/4.png)

现在，您可以安装最新版本的 Python。

## 如何使用 pyenv 安装最新版本的 Python

现在，您只需要运行以下命令：

```bash
pyenv install 3.9.2
```

注意，你可以用 3.9.2 来代替 Python 的任何最新版本。例如，Python 4.0.0 出来后，你可以运行这个。

```bash
pyenv install 4.0.0
```

## 对 pyenv 安装进行故障排除

如果遇到“C compiler cannot create executables(C 编译器无法创建可执行文件)”的错误，则解决此问题的最简单方法是重新安装 Apple 的 Xcode。

Xcode 是苹果公司创建的一个工具，它包含了 Python 在 MacOS 上运行时使用的所有 C 库和其他工具。Xcode 的容量高达 11 千兆字节，但你会想要更新。你可能想在睡觉的时候运行这个。

你可以在这里获得[最新版本的苹果 Xcode](https://developer.apple.com/download/)。我不得不在升级到 MacOS Big Sur 后进行这项工作，但一旦我这样做了，下面的命令都能正常工作。只要重新运行上面的 `pyenv install 3.9.2` ，现在应该可以了。

## 如何为 pyenv 设置 MacOS 路径（Bash 或 ZSH）

首先你需要更新你的 Unix 路径，为 PyEnv 与你的系统交互铺平道路。我将跳过解释这一切是如何工作的，而只是给你提供你可以运行的官方单行命令。但如果你想了解路径和垫片的工作原理，PyEnv 的官方 GitHub repo 很好地解释了这些概念。

以下是在 Bash（默认情况下安装在 MacOS 中）中更新 `.bash_profile` 的方法：

```bash
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bash_profile
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bash_profile
```

或者，如果像我一样安装了 ZSH（或 OhMyZSH），则需要编辑 `.zshrc` 文件：

```bash
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
```

然后，您想将 PyEnv Init 添加到您的终端。如果您使用的是 Bash，请运行以下命令（同样，这是 MacOS 的默认设置）：

```bash
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.bash_profile
```

或者，如果您使用的是 ZSH，请运行以下命令：

```bash
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.zshrc
```

现在，通过运行以下命令重置终端：

```bash
reset
```

## 如何将 Python 版本设置为全局默认值（Bash 或 ZSH）

您可以将 Python 的最新版本设置为全局版本，这意味着当您运行 Python 应用程序时，它将是 MacOS 使用的 Python 的默认版本。

运行以下命令：

```bash
pyenv global 3.9.2
```

同样，您可以将 3.9.2 替换为最新版本。

现在你可以通过检查 Python 的全局版本来验证这是否有效：

```bash
pyenv versions
```

您应该看到以下输出：

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/python-version-on-mac-update/5.png)

## 最后一步：关闭终端并重新启动

重启浏览器后，运行 `python` 命令，你就会启动新版本的 Python，而不是旧版本。

![Python 3.9.2，无弃用警告](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202104/python-version-on-mac-update/6.png)

恭喜你感谢您阅读本文，并祝您编程愉快。

---

翻译自[www.freecodecamp.org](https://www.freecodecamp.org/news/python-version-on-mac-update/)，作者：Quincy Larson
