---
title: 小技巧|同步你的VSCode设置及扩展插件，换机不用愁！
date: 2019-11-02 21:44:22
categories:
- 工具
tags:
- VSCode
- 小技巧
---

有了这个技能，换电脑，换工作等就不用愁了。
<!-- more -->
实现同步的功能主要依赖于VSCode插件[“Settings Sync”](https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync&ssr=false#overview)。它是基于 GitHub Tokens 和 GitHub Gist 功能实现，可以一键轻松实现上传下载跨多台机器同步设置、代码片段、主题、文件图标、启动、键绑定、工作区和扩展。

## 配置同步
下载安装插件后，会显示插件的欢迎页面
![插件的欢迎页面](https://i.loli.net/2019/11/02/VnjkPrxBNQMKb5E.png)

“设置同步配置”页面将在代码启动时自动打开，并且需要进行两项设置
1. GitHub Token
2. GitHub Gist Id

如果您是首次用户，则GitHub帐户需要检索 GitHub 令牌，而 Settings Sync 将创建 Gist。

执行的配置步骤：
1. 点击 Login with GitHub ，登录GitHub成功后即可以自动拿到 GitHub Token。
2. 登录 GitHub 成功后你将得到成功的消息。 
![成功的消息](https://i.loli.net/2019/11/02/ihDOqXN95e1HJuB.png)
3. 如果您是第一次使用设置同步，则在您上传设置时会自动创建 
GIST。
4. 如果您已经拥有 GitHub Gist，则将打开新窗口，以允许您选择 GitHub Gist 或跳过以创建新的 Gist。 
![Settings-Sync-3.png](https://i.loli.net/2019/11/02/JOdV9ZTtkNzA1qI.png)

您始终可以通过转到[https://gist.github.com](https://gist.github.com)并检查名为 cloudSettings 的 gist 来验证创建的 gist。

也可以在这里找到Gist的入口。
> 访问Gist需要科学上网 

![Settings-Sync-4.png](https://i.loli.net/2019/11/02/7nZwMyt9eYHFGmA.png)

cloudSettings 的 gist 就创建了，点进去查看，里面是诸如 extensions.json 等VSCode各方面的配置文件。

![Settings-Sync-5.png](https://i.loli.net/2019/11/02/YlD6tXqwrA3RJhB.png)

## 上传你的VSCode的配置

按下 `Shift + Alt + U` (macOS: `Shift + Option + U`)，也可以使用命令来选择你的操作
![Settings-Sync-6.png](https://i.loli.net/2019/11/02/fnOoRk125lChHzJ.png)

首次下载或上传时，欢迎页面将自动打开，您可以在其中配置“设置同步”。选择上传设置后，你将看到“摘要”详细信息，以及上载的每个文件和扩展名的列表。
![Settings-Sync-7.png](https://i.loli.net/2019/11/02/LoRHcFOC5hr7KA3.png)

这里其实也不用记下来，它会自动配置到你的扩展配置里： 
![Settings-Sync-8.png](https://i.loli.net/2019/11/02/mTfE2jCIORtNcn7.png)

每次上传配置同步会用这个创建好的 ID。

## 下载你的VSCode的配置
在另一台电脑的 VSCode 中，同样先也要走安装插件、登录 GitHub 的步骤，登录成功后会列出你的所有的 Gist，点击这个配置同步的 Gist。
![Settings-Sync-9.png](https://i.loli.net/2019/11/02/9HjgaR5ZoSuPIFc.png)

点击后会弹出一个提示 
![Settings-Sync-10.png](https://i.loli.net/2019/11/02/zQ13cwVaMGI6kAH.png)

点击CLOSE TAB，然后 VSCode 就会开始下载你的配置信息。 

慢慢等待下载完成，完成后重启 VSCode，你就可以看到新电脑上的 VSCode 插件、配置信息等全部装好了。
![Settings-Sync-11.png](https://i.loli.net/2019/11/02/7hqTy6VUPnpFAdK.png)

## GitHub Gist 是什么？
扩展一下，GitHub Tokens 你应该知道是什么把，Gist又是什么呢？Gist 是 GitHub 提供的一个有趣的服务，最简单的功能就是分享代码片段，但是 gist 提供的功能不仅限于此。

开发人员常常使用 Gist 记录他们的代码片段，但是 Gist 不仅仅是为极客和码农开发的，每个人都可以用到它。如果您听说过类似 Pastebin 或者 Pastie 这样的web应用的话，那您就可以看到它们和 Gist 很像，但是 Gist 比它们要更优雅。因为这些免费应用一般含有广告，而且带有很多其他杂七杂八的功能。

搞不清楚为什么国内不能访问 Gist，需要梯子....，更多 Gist 的信息可以参考这个链接：[https://www.zhihu.com/question/21343711/answer/32023379](https://www.zhihu.com/question/21343711/answer/32023379)

*************
关注公众号，第一时间接收最新文章。如果对你有一点点帮助，可以点喜欢点赞点收藏，还可以小额打赏作者，以鼓励作者写出更多更好的文章。

<center>
<img src="https://i.loli.net/2019/11/03/2gzpnIAqcTjB1hL.jpg" style="width:320px;" />
</center>