---
title: 小技巧|给Mac添加右键菜单「使用 VSCode 打开」的方法
date: 2020-11-20 22:26:05
categories:
  - 工具
tags:
  - macOS
  - 小技巧
---

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/Open-With-VSCode/banner.jpeg)

用 macOS 系统的苹果电脑用户都知道，macOS 某些地方确实没 Windows 方便，比如右键菜单，没有复制粘贴之类的菜单，刚开始还有点使用不方便，今天我介绍两种方法来实现一个用右键通过 VSCode 打开文件和文件夹的方法，第一个是使用原生方式，第二种是借助第三方软件。

<!-- more -->

## 1.不借助第三方 APP 实现

我们要实现的最终的实现效果是在文件/文件夹上右击时，会出现菜单项「用 VSCode 打开」，点击后会启动 Visual Studio Code 打开对应的文件/文件夹。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/Open-With-VSCode/1.png)

实现步骤如下。

点击 Dock（程序坞）上的 Launchpad（小火箭），打开启动台，找到其他

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/Open-With-VSCode/2.png)

点击自动操作（小机器人），如图：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/Open-With-VSCode/3.png)

Command + n 新建文稿，在「选取文稿类型」里选择「快速操作」：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/Open-With-VSCode/4.png)

点击选取，在左侧面板选择“实用工具”；然后找到”运行 Shell 脚本“，把它拽到右侧面板里，在右侧“服务”收到选定选择文件夹，位置 Finder（访达）；“运行 Shell 脚本”的面板里，选择 Shell”/bin/bash“，传递输入“作为自变量”，然后修改 Shell 脚本，如图：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/Open-With-VSCode/5.png)

按顺序操作，复制以下内容到 Shell 脚本中：

```
for f in "$@"
do
     open -a "Visual Studio Code" "$f"
done
```

以上代码片段的大概意思是对于传入的一个或多个参数，都使用 Visual Studio Code 这个 APP 打开（将以下步骤配置完成后，可以分别选中一个、多个文件 / 文件夹，然后右键用 VSCode 打开看看效果）。

Command + s 保存为 「用 VSCode 打开」：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/Open-With-VSCode/6.png)

好了，现在试试在 Finder 里右键一个文件，就可以直接看到「用 VSCode 打开」菜单，右键一个文件夹，就可以看到「服务」-「用 VSCode 打开」菜单了。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/Open-With-VSCode/7.png)

愉快地使用 Visual Studio Code 和各种文件、文件夹玩耍吧。

## 安装超级右键 APP

超级右键 APP 图标如下，可直接在 Mac 商店安装

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/Open-With-VSCode/8.png)

在其他设置中可以勾选你想要的服务：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/Open-With-VSCode/9.png)

你也可以在新建文件设置中勾选你想添加到右键菜单的服务：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/Open-With-VSCode/9.1.png)

你可以选择其中一些在主菜单中显示，我的菜单效果如下：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/Open-With-VSCode/10.png)

新建 TXT、Markdwon 直接显示在主菜单中，新建 Office 文档不太常用就折叠了。

**快试试吧！**
