---
title: GitHub中的Pull Request和Pulse详解
date: 2016-04-12 22:21:00
categories:
- 工具使用
tags:
- github
description: "Pull Request是用户修改代码后向对方仓库发送采纳的请求功能，也是GitHub的核心功能，正式因为有了这个功能，才会让众多开发者轻松地加入到开源开发的队伍中来。"
---

Pull Request
=========================
Pull Request是用户修改代码后向对方仓库发送采纳的请求功能，也是GitHub的核心功能，正式因为有了这个功能，才会让众多开发者轻松地加入到开源开发的队伍中来。
![1.png](http://ww4.sinaimg.cn/large/006tNc79ly1g5d7zbhwbqj30rm0dogmq.jpg)

在Pull Request界面能查看当前处于Open状态的Pull Request。通过点击列表上方的页面特定的Pull Request就会进入详细页面选项可以重新筛选和排列。点击列表中特定Pull Request就会进入详细页面。
![2.png](http://ww4.sinaimg.cn/large/006tNc79ly1g5d7zcduboj30se0g6tac.jpg)

页面上方（红框）显示着这次时从谁的哪个分支向谁的哪个分支发送Pull Request。我们接着看每一个标签页的功能。

### Conversation（谈话、会话）
在Conversation标签页中，可以查看与当前Pull Request相关的所有评论以及提交的历史记录。人们在这里添加评论互相探讨，发送提交落实讨论内容的整个过程会按时间顺序排列，提交日志的右侧会有该提交的哈希值（HashCode）,点击链接即可进入该提交的详细信息。

**小技巧：R键引用评论**
在Conversation中人们通过添加评论进行对话，这里有一个简单的方法可以帮助你引用一个人的评论。选中想引用的评论文字后按R键，杯选择的部分就会自动以评论语法写入评论文本框。该技巧在Issue中同样适用。
![3.png](http://ww3.sinaimg.cn/large/006tNc79ly1g5d7zda332j30n40f6myj.jpg)

### Commits（提交）
在Commits标签页中，按时间顺序列表显示了与当前Pull Request相关的提交。标签上的数字为提交次数。每个提交右侧的哈希值可以连接到该提交的代码。
![4.png](http://ww3.sinaimg.cn/large/006tNc79ly1g5d7zenrghj30s10c2jsv.jpg)

**小技巧：在评论中添加表情**
GitHub的文化中有使用表情的习惯，不止中国人喜欢在QQ上用表情，老外其实更早更习惯用表情。表情种类繁多，要一次全记下来是否困难。这时候我们可以利用表情的自动补全功能了。

在评论中输入“:”（冒号）便会开启表情自动补全功能，只要输入几个与该表情相关的字母，系统就会为你筛选自动补全的对象。
![5.png](http://ww4.sinaimg.cn/large/006tNc79ly1g5d7zfl31ej30n307lwf2.jpg)

### Files Changed（文件变化）
Files Changed表情页中可以产看当前Pull Request更改的文件内容以及前后差别。标签上的数字表示新建及被更改的文件数。

默认情况下系统会将空格不同也高亮显示，所以在空格有改动的情况下会难以阅读。这时候只需要在URL的末尾添加“?w=1”就可以不显示空格的差别。

将鼠标指针放到被更改行号的左侧，我们会看到一个加号。点击这个加号可以在代码中插入评论，这样，评论时针对哪一行代码就一目了然了。
![6.png](http://ww3.sinaimg.cn/large/006tNc79ly1g5d7zgl0uxj30s50c83zv.jpg)

Pulse（活跃度）
===========================
Pulse是体现该仓库软件开发活跃度的功能。近期该仓库创建了多少个Pull Request或Issue，有多少人参与了这个仓库的开发等，都可以在这里一目了然。

根据这个页面，用户可以判断目前这个软件是否正在被积极开发，或者有仓库修改权限的人是否在认真地进行BUG修正等维护工作。在GitHub选软件时，它可以作为一个重要的衡量标准。
![7.png](http://ww4.sinaimg.cn/large/006tNc79ly1g5d7zhk7ftj30rp0fuabf.jpg)

### active pull request
在页面中Overview的左边部分显示了特定期间内活动过的Pull Request数。上图中有6个Pull Request，其中2个被采纳，其余4个仍然保持Open状态，剩余的这4个Pull Request将来要么会被采纳要么会被Close。

如果想查看清单的详细内容，只要点击对应项目即可。Pull Request的概要及链接按照合并的先后顺序排列。下图是以合并的Pull Request的概要及链接。
![8.png](http://ww4.sinaimg.cn/large/006tNc79ly1g5d7zif7z6j30p0046dfy.jpg)

### active issue
在页面中Overview的右边部分显示了特定时期内活动过的Issue数。上图中有35个Issue，其中26个被Close，其余9个为Open状态。

如果想查看清单详细内容，只要点击对应项即可。Issue的概要及链接按照Close的先后顺序排列。

点击new issue则可以按创建的先后顺序查看Issue的概要及链接。
通过观察Issue的整体动向，用户能够知道这个软件是否有人在积极地维护与支持。对方仓库是否活跃，用户发送的BUG报告和相关探讨越可能收到回应。

### commits
Overview下方显示的是与提交相关信息。左侧部分包含了如下几类信息，以上图为例：
编写过代码的人数（3个）  
提交的次数（18次）  
default branch 中修改过的文件数（3个）  
default branch 中添加的行数（139）  
default branch 中删除的行数（230）  

通过这些信息，用户可以大致把握该仓库中活跃开发者的人数。另外，右侧图标显示了这些开发者具体发送的提交数。

### Unresolved Conversation
![9.png](http://ww2.sinaimg.cn/large/006tNc79ly1g5d7zjdrqej30sc05y0t9.jpg)
这个部分，列出的Issue和Pull Request都创建于Period指定的时间之前，它们都尚未Close并且仍有人参与评论。一般情况下，仓库中软件的重大事项讨论都会持续很长时间，所以这些讨论大多放在这里。其中会有不少关于该软件今后的发展方向的讨论。