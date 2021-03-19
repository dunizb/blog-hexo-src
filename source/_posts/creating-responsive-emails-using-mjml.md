---
title: 使用Vue.js和MJML创建响应式电子邮件
date: 2021-03-19 22:20:46
img: http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202103/mjml/banner.png
categories:
  - 翻译
tags:
  - Vue.js
summary: "MJML 是一种现代的电子邮件工具，使开发人员可以在所有设备和邮件客户端上创建美观、响应迅速的出色电子邮件。这种标记语言是为了减少编写响应式电子邮件的痛苦而设计的"
---

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202103/mjml/banner.png)

> 翻译自[blog.logrocket.com](https://blog.logrocket.com/creating-responsive-emails-using-mjml/)，作者：Ogundipe Samuel

MJML 是一种现代的电子邮件工具，使开发人员可以在所有设备和邮件客户端上创建美观、响应迅速的出色电子邮件。这种标记语言是为了减少编写响应式电子邮件的痛苦而设计的。

它的语义语法使其易于使用。它还具有功能丰富的标准组件，可缩短开发时间。在本教程中，我们将使用 MJML 构建漂亮的响应式邮件，并在多个邮件客户端上进行测试。

## 开始 MJML

你可以使用 npm 安装 MJML，以将其与 Node.js 或 CLI 结合使用：

```shell
$ npm install -g mjml
```

## 构建我们的电子邮件

首先，请创建一个名为 `email.mjml` 的文件，尽管你也可以选择其他任何名称。创建文件后，我们的响应式电子邮件将分为以下几部分：

- 公司 header
- 图片 header
- Email 介绍
- 栏目部分
- 图标
- 社交图标

### 栏目

这些部分是我们响应式电子邮件的框架。如上所示，我们的电子邮件将分为六个部分，在我们的 `email.mjml` 文件中：

```html
<mjml>
  <mj-body>
    <!-- 公司 Header -->
    <mj-section background-color="#f0f0f0"></mj-section>
    <!-- 图片 Header -->
    <mj-section background-color="#f0f0f0"></mj-section>
    <!-- Email 介绍 -->
    <mj-section background-color="#fafafa"></mj-section>
    <!-- 栏目部分 -->
    <mj-section background-color="white"></mj-section>
    <!-- 图标 -->
    <mj-section background-color="#fbfbfb"></mj-section>
    <!-- 社交图标 -->
    <mj-section background-color="#f0f0f0"></mj-section>
  </mj-body>
</mjml>
```

从上面可以看到，我们正在使用两个 MJML 组件：`mj-body` 和 `mj-section`。`mj-body` 定义了我们电子邮件的起点，而 `mj-section` 定义了一个包含其他组件的节。

对于定义的每个部分，还定义了具有各自十六进制值的 `background-color` 属性。

### 公司 Header

我们电子邮件的此部分仅在中心横幅位置包含我们的公司/品牌名称：

```html
<!-- 公司 Header -->
<mj-section background-color="#f0f0f0">
  <mj-column>
    <mj-text
      font-style="bold"
      font-size="20px"
      align="center"
      color="#626262"
    >
      Central Park Cruise
    </mj-text>
  </mj-column>
</mj-section>
```

`mj-column` 组件是用来定义一个列。`mj-text` 组件用于我们的文本内容，并采取字体样式、字体大小、颜色等样式属性。

### 图片 Header

在本部分中，我们将有一个背景图片和一段文字，它们应代表我们的公司口号。我们还会有一个号召性用语按钮，指向一个包含更多详细信息的页面。

要添加图片标题，你必须将该部分的背景颜色替换为 `background-url`。与第一个标题相似，你将不得不在垂直和水平方向上居中放置文本，padding 保持不变。

按钮的 `href` 设置按钮的位置。为了让背景在列中呈现全宽，将列宽设置为 600px，`width=“600px"`。

我们的电子邮件的这一部分将只包含我们的公司/品牌名称的中心横幅位置。

```html
<!-- Image Header -->
<mj-section
  background-url="https://ca-times.brightspotcdn.com/dims4/default/2af165c/2147483647/strip/true/crop/2048x1363+0+0/resize/1440x958!/quality/90/?url=https%3A%2F%2Fwww.trbimg.com%2Fimg-4f561d37%2Fturbine%2Forl-disneyfantasy720120306062055"
  background-size="cover"
  background-repeat="no-repeat"
>
  <mj-column width="600px">
    <mj-text
      align="center"
      color="#fff"
      font-size="40px"
      font-family="Helvetica Neue"
      >Christmas Discount</mj-text
    >
    <mj-button background-color="#F63A4D" href="#">
      See Promotions
    </mj-button>
  </mj-column>
</mj-section>
```

要使用图像 header，我们将向 `jms -section` 组件添加 `background-url` 属性，然后使用 `background-size` 和 `background-repeat` 属性设置图像的样式。

对于我们的口号文本块，我们使用 `align` 属性将文本在水平和垂直方向上居中对齐。你还可以根据需要设置文本颜色，字体大小，字体系列等。

号召性用语按钮是使用 `mj-button` 组件实现的。`background-color` 属性允许我们指定按钮的背景色，然后使用 `href` 指定链接或页面的位置。

### Email 件介绍

简介文字将由标题，主体文字和号召性用语组成。

```html
<!-- Intro text -->
<mj-section background-color="#fafafa">
  <mj-column width="400px">
    <mj-text
      font-style="bold"
      font-size="20px"
      font-family="Helvetica Neue"
      color="#626262"
      >Ultimate Christmas Experience</mj-text
    >
    <mj-text color="#525252">
      Lorem ipsum dolor sit amet, consectetur adipiscing
      elit. Proin rutrum enim eget magna efficitur, eu
      semper augue semper. Aliquam erat volutpat. Cras id
      dui lectus. Vestibulum sed finibus lectus, sit amet
      suscipit nibh. Proin nec commodo purus. Sed eget nulla
      elit. Nulla aliquet mollis faucibus.
    </mj-text>
    <mj-button background-color="#F45E43" href="#"
      >Learn more</mj-button
    >
  </mj-column>
</mj-section>
```

### 栏目部分

在这封邮件的部分，我们会有两栏：一栏是描述性的图片，二栏是我们的文字块，用来补充第一部分的图片。

```html
<!-- Side image -->
<mj-section background-color="white">
  <!-- Left image -->
  <mj-column>
    <mj-image
      width="200px"
      src="https://navis-consulting.com/wp-content/uploads/2019/09/Cruise1-1.png"
    />
  </mj-column>
  <!-- right paragraph -->
  <mj-column>
    <mj-text
      font-style="bold"
      font-size="20px"
      font-family="Helvetica Neue"
      color="#626262"
    >
      Amazing Experiences
    </mj-text>
    <mj-text color="#525252">
      Lorem ipsum dolor sit amet, consectetur adipiscing
      elit. Proin rutrum enim eget magna efficitur, eu
      semper augue semper. Aliquam erat volutpat. Cras id
      dui lectus. Vestibulum sed finibus lectus.
    </mj-text>
  </mj-column>
</mj-section>
```

左侧的第一列使用 `mj-image` 组件指定要使用的图像。该图像可以是本地文件，也可以是远程托管的图像（在我们的情况下是这样）。

右侧的第二列包含两个文本块，一个用于我们的标题，另一个用于主体文本。

### 图标

图标部分将分为三列。你还可以添加更多内容，具体取决于你希望电子邮件的外观。

```html
<!-- Icons -->
<mj-section background-color="#fbfbfb">
  <mj-column>
    <mj-image
      width="100px"
      src="https://191n.mj.am/img/191n/3s/x0l.png"
    />
  </mj-column>
  <mj-column>
    <mj-image
      width="100px"
      src="https://191n.mj.am/img/191n/3s/x01.png"
    />
  </mj-column>
  <mj-column>
    <mj-image
      width="100px"
      src="https://191n.mj.am/img/191n/3s/x0s.png"
    />
  </mj-column>
</mj-section>
```

每列都有其自己的 `mj-image` 组件，用于渲染图标图像。

### 社交图标

本部分将包含指向我们的社交媒体帐户的图标。

```html
<mj-section background-color="#e7e7e7">
  <mj-column>
    <mj-social>
      <mj-social-element name="instagram" />
    </mj-social>
  </mj-column>
</mj-section>
```

MJML 带有 `mj-social` 组件，可轻松用于显示社交媒体图标。在我们的电子邮件中，我们使用了 Twitter `mj-social-element`。

## 全部放在一起

至此，我们已经实现了所有部分，完整的 `email.mjml` 应该如下所示：

```html
<mjml>
  <mj-body>
    <!-- Company Header -->
    <mj-section background-color="#f0f0f0">
      <mj-column>
        <mj-text
          font-style="bold"
          font-size="20px"
          align="center"
          color="#626262"
        >
          Central Park Cruises
        </mj-text>
      </mj-column>
    </mj-section>
    <!-- Image Header -->
    <mj-section
      background-url="https://ca-times.brightspotcdn.com/dims4/default/2af165c/2147483647/strip/true/crop/2048x1363+0+0/resize/1440x958!/quality/90/?url=https%3A%2F%2Fwww.trbimg.com%2Fimg-4f561d37%2Fturbine%2Forl-disneyfantasy720120306062055"
      background-size="cover"
      background-repeat="no-repeat"
    >
      <mj-column width="600px">
        <mj-text
          align="center"
          color="#fff"
          font-size="40px"
          font-family="Helvetica Neue"
          >Christmas Discount</mj-text
        >
        <mj-button background-color="#F63A4D" href="#">
          See Promotions
        </mj-button>
      </mj-column>
    </mj-section>
    <!-- Email Introduction -->
    <mj-section background-color="#fafafa">
      <mj-column width="400px">
        <mj-text
          font-style="bold"
          font-size="20px"
          font-family="Helvetica Neue"
          color="#626262"
          >Ultimate Christmas Experience</mj-text
        >
        <mj-text color="#525252">
          Lorem ipsum dolor sit amet, consectetur adipiscing
          elit. Proin rutrum enim eget magna efficitur, eu
          semper augue semper. Aliquam erat volutpat. Cras
          id dui lectus. Vestibulum sed finibus lectus, sit
          amet suscipit nibh. Proin nec commodo purus. Sed
          eget nulla elit. Nulla aliquet mollis faucibus.
        </mj-text>
        <mj-button background-color="#F45E43" href="#"
          >Learn more</mj-button
        >
      </mj-column>
    </mj-section>
    <!-- Columns section -->
    <mj-section background-color="white">
      <!-- Left image -->
      <mj-column>
        <mj-image
          width="200px"
          src="https://navis-consulting.com/wp-content/uploads/2019/09/Cruise1-1.png"
        />
      </mj-column>
      <!-- right paragraph -->
      <mj-column>
        <mj-text
          font-style="bold"
          font-size="20px"
          font-family="Helvetica Neue"
          color="#626262"
        >
          Amazing Experiences
        </mj-text>
        <mj-text color="#525252">
          Lorem ipsum dolor sit amet, consectetur adipiscing
          elit. Proin rutrum enim eget magna efficitur, eu
          semper augue semper. Aliquam erat volutpat. Cras
          id dui lectus. Vestibulum sed finibus lectus.
        </mj-text>
      </mj-column>
    </mj-section>
    <!-- Icons -->
    <mj-section background-color="#fbfbfb">
      <mj-column>
        <mj-image
          width="100px"
          src="https://191n.mj.am/img/191n/3s/x0l.png"
        />
      </mj-column>
      <mj-column>
        <mj-image
          width="100px"
          src="https://191n.mj.am/img/191n/3s/x01.png"
        />
      </mj-column>
      <mj-column>
        <mj-image
          width="100px"
          src="https://191n.mj.am/img/191n/3s/x0s.png"
        />
      </mj-column>
    </mj-section>
    <!-- Social icons -->
    <mj-section background-color="#e7e7e7">
      <mj-column>
        <mj-social>
          <mj-social-element name="instagram" />
        </mj-social>
      </mj-column>
    </mj-section>
  </mj-body>
</mjml>
```

## 运行我们的应用程序

现在我们已经完成了电子邮件的构建，我们可以继续对其进行编译以查看其外观。为此，我们在终端中键入以下内容：

```shell
mjml -r email.mjml -o .
```

- `-r`：允许 MJML 读取和编译我们的 `mjml` 文件
- `-o .`：告诉 MJML 将编译后的 `mjml` 输出保存到同一目录中

MJML 完成编译后，你现在应该在同一目录中看到一个 `email.html` 文件。 使用你喜欢的电子邮件客户端或浏览器打开它，它的外观应类似于下图：

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202103/mjml/1.png)

## 总结

正如我们刚才看到的，MJML 帮助我们生成跨多个浏览器和客户机响应的高质量、漂亮的 HTML 电子邮件。
