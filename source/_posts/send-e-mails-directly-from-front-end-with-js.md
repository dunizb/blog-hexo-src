---
title: EmailJS：5步使用JavaScript直接从前端发送电子邮件💥💥
date: 2020-06-13 00:08:23
categories:
  - 技术
tags:
  - 前端
  - JavaScript
---

前端使用 JavaScript 发送电子邮件，5 个步骤图文教程。你**不需要使用任何后端语言**，如 PHP 或 Python。此外，你甚至不需要 Node.js!

<!-- more -->

有很多方法可以读取这些数据。你可以将你的表单与数据库（如 MySQL）连接，然后从数据库中读取传入的信息。好吧，这是一个选择，但是我认为这对于你的非技术客户来说可能会很麻烦。

你需要的只是一个简单的 **EmailJS** 库。

本文将介绍下面两个重要功能：

- 配置 EmailJS 帐户
- 使用 JS 发送电子邮件

我将分 5 个步骤向你展示如何从头开始构建电子邮件发送器。

在我的项目中使用了**Webpack**，我在 **src** 文件夹存放源码，**dist** 存放最终发布版本的代码，使用 `npm run dev` 可以把项目跑起来。

> **项目完整代码：** https://github.com/dunizb/CodeTest/tree/master/projects/emailjs-demo，真实可运行。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202006/email-js/14.png)

## 步骤 1，用 HTML 创建表单

首先需要做的当然是创建一个 HTML 表单。注意，你不必设置 `required` 或 `max` 等验证属性，因为稍后，`preventDefault()` 函数将在你的提交事件上运行，它将取消这些属性的工作。

表单中最重要的事情是为每个输入设置 `name` 属性，这在后面会用到的。

我的简单表格如下所示：

src/html/index.html

```html
<form class="form">
  <input name="to" type="text" placeholder="收件人" class="form__input" />
  <input name="name" type="text" placeholder="你的名字" class="form__input" />
  <input name="topic" type="text" placeholder="主题" class="form__input" />
  <textarea
    name="message"
    type="text"
    placeholder="你的消息"
    class="form__input"
  ></textarea>

  <input type="submit" value="发送" class="form__input form__input--button" />
</form>
```

## 步骤 2，注册 emailjs

要配置电子邮件，您必须注册[emailjs 服务](https://dashboard.emailjs.com/account/create)。不用担心，使用此网站非常友好，你不会花很多时间在该网站上。

登录后，将询问你有关电子邮件服务的信息。它放置在个人电子邮件服务区域中，就我而言，我选择了 Gmail。

![选择邮件服务](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202006/email-js/2.png)

点击 Connect account 连接 Gmail

![连接Gmail](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202006/email-js/3.png)

此时会弹出 Gmail 的授权窗口，在请求权限对话框中点击允许。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202006/email-js/4.png)

连接 Gmail 帐户后，点击“Add Service”按钮。成功添加后可以看到如下界面

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202006/email-js/5.png)

例如，如果你连接上你的 xyz@gmail.com 账户，你未来收到的邮件就会从这个账户发出。所以不要担心让 Gmail 代你发送电子邮件——这正是你所需要的！

## 步骤 3，创建你的邮件模板

![创建你的邮件模板](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202006/email-js/6.png)

经过上面的步骤，你已经成功地连接了您的 Gmail 帐户，在你的仪表板中应该可以看到，点击左侧的导航进入邮件模板设置页面。

然后单击“Create a new template”按钮创建新模板，界面非常友好，所以创建它不会有任何问题。你可以选择模板的名称和 ID，我设置为“my-amazing-template”。

![创建新模板](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202006/email-js/7.png)

你现在必须指定，传入的电子邮件应该是什么样的。将使用来自于表单中的 `name` 属性作为变量插入到 `{{{ }}}` 符号中。

不要忘记在 `To email`（收件人）部分中放置一个电子邮件地址，这里我们读取我们输入的收件人变量。

![插入变量](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202006/email-js/8.png)

这是我的简单模板，它使用了 4 个变量，分别来自于我的 HTML 表单，我还指定了一个收发邮件的主题。

## 步骤 4，保存你的 API 密钥

好吧，这部分没有什么特别的。 Emailjs 共享授权 API 密钥，这些密钥将在发送电子邮件期间使用。当然，放置这些密钥的最佳位置是 `.env` 配置文件。但由于我的工作对象是简单的静态文件，不想做服务器配置的工作，所以我会把它们保存在 `apikeys` 文件中，以后再导入。

你的 `USER_ID` 位于 Account > API Keys 中。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202006/email-js/9.png)

并且你的 `TEMPLATE_ID` 位于模板标题的下方。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202006/email-js/10.png)

这是我的 src/js/apikeys.js 的示例配置。

```javascript
export default {
  USER_ID: "user_DPUd-rest-of-my-id",
  TEMPLATE_ID: "my_amazing_template",
};
```

## 步骤 5，发送邮件！

现在是该项目的最后也是最重要的部分了，现在我们必须使用 javascript 发送电子邮件。

首先，你必须下载 emailjs 软件包。

```shell
npm i emails-com
```

之后，转到你的 src/js/main.js 文件并导入你的库和 apikey。

```javascript
import emailjs from "emailjs-com";
import apiKeys from "./apikeys";
```

现在是时候在 src/js/main.js 中编写发送电子邮件功能了。

```javascript
const sendEmail = (e) => {
  e.preventDefault();

  emailjs
    .sendForm("gmail", apiKeys.TEMPLATE_ID, e.target, apiKeys.USER_ID)
    .then(
      (result) => {
        console.log(result.text);
      },
      (error) => {
        console.log(error.text);
      }
    );
};
```

很简单。如你所见，`sendForm` 函数采用 4 个参数。

第一个参数：你的电子邮件的 ID，位于以下位置

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202006/email-js/11.png)

第二个参数：`TEMPLATE_ID` 来自你的 apikey 文件。

第三个参数：表单提交中的事件对象`e`。

第四个参数：`USER_ID` 来自你的 apikey 文件。

最后，找到表单并添加提交事件侦听器。

```javascript
// src/js/main.js
const form = document.querySelector(".form");
form.addEventListener("submit", sendEmail);
```

如前所述，由于使用了 `preventDefault()` 函数，因此无法进行属性验证，你必须使用 JS 自己进行验证和清除输入。

仅此而已，最后让我们使用 `npm run dev` 测试一下，我填写页面上的表单并发送。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202006/email-js/12.png)

我的 163 邮箱收到了电子邮件，内容正是根据我们的模板和表单数据渲染出来的。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202006/email-js/13.png)

通过上图可以看出，所有的变量的值都填充到了正确的位置上。

## 结束

通过本文的介绍你会发现用 JS 发送邮件并非难事。

使用 emailjs，你可以简单的方式发送电子邮件。

我相信你未来的用户会很高兴收到来自他们网页上表单填写数据的 t 邮件，相信本文对你有帮助。

**文章配套代码**: https://github.com/dunizb/CodeTest/tree/master/projects/emailjs-demo
