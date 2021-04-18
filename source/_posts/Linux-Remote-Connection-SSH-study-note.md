---
title: Linux SSH 入门笔记
date: 2015-05-29 00:00:04
categories:
  - 服务端技术
tags:
  - Linux
  - SSH
---

![](//ww2.sinaimg.cn/large/006tNc79ly1g5d87xr5tvj30dc078ac9.jpg)

## 1.认识 SSH 和安装

### 1.1 SSH 是什么

- SSH：Secure Shell 安全外壳协议
- 建立在应用层基础上的安全协议
- 可靠，转为全程登录会话和其他网络服务提供安全性的协议
- 有效防止远程登录管理过程中信息泄露的问题
- SSH 客户端适应于多种平台
- SSH 服务端几乎支持所有 UNIX 平台

<!-- more -->

### 1.2 服务器安装 SSH 服务

- 安装 SSH：`yum install openssh-server`
- 启动 SSH：`service sshd start`
- 设置开机运行：`chkconfig ssh on`
- 查看进程在不在的命令：`ps -ef |grep ssh`

### 1.3 SSH 客户端连接服务器

- SSH 是典型的客户端和服务端的交互模式，客户端广泛的支持各个平台
- Windows 有很多工具可以支持 SSH 连接功能，例如：Xshell、Putty、secureCRT
- Linux 平台需要安装客户端软件：`yum install openssh-clients`，在安装服务端 SSH 的时候已经默认安装好了不需要再安装

## 2. SSH 使用

### 2.1 SSH config 用法详解

- config 为了方便我们批量管理多个 ssh
- config 存放在 `~/.ssh/config`

**SSH config 语法关键字**
|Host|别名|
|:----|:-----|
|**HostName**|**主机名**|
|**Port**|**端口**|
|User|用户名|
|IdentityFile|密钥文件的路径|

在`~/.ssh`下新建 config 文件：

```shell
host "aliyun"
    HostName: 39.104.184.253
    Port 22
    User root
```

然后即可使用别名登录：`ssh aliyun`

### 2.2 免密码登录方案之 SSH Key

- ssh key 使用非对称加密方式生成公钥和私钥
- 私钥存放在本地`~/.ssh`目录
- 公钥可以对外开放，放在服务器的`~/.ssh/ authorized_keys`

Linux 平台生成 ssh key：

- ssh-keygen -t rsa
- ssh-keygen -t dsa

在服务器上，编辑该文件：`vim ~/.ssh/authorized_keys`，把生成的 sshkey 粘贴进去

添加私钥到 ssh 服务：`ssh-add ~/.ssh/aliyun`

**SSH 端口安全**
端口安全是指尽量避免服务器的远程连接端口被不法份子知道，为此而改变默认服务端口号的操作。

如何改变 SSH 服务端口，修改`/etc/ssh/sshd_config`配置
