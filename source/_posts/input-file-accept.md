---
title: 【小技巧】上传文件时，如何在资源管理器中过滤非指定格式的文件
date: 2018-08-19 19:51:18
categories:
- 技术
tags:
- 前端
- 小技巧
---
在我们做文件上传功能时，比如说导入Excel功能，通常需要限定用户只能导入Excel文件，对其他文件要进行过滤，一种做法是用户在选择文件时可以选择任何文件，只是在选择后触发`change`事件后再判断文件后缀格式，这种方法的麻烦之处在于要自己写代码判断。如何在打开资源管理器中就过滤非Excel文件呢？如下图所示。
<!-- more -->
![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/input-file-accept/1.png)

上面在打开的文件选择资源管理器中，只显示了Excel文件，其他文件格式都不能选择。

实现这种效果的关键，就是应用 `input`的`accept`属性。accept 属性只能与 `<input type="file">` 配合使用。它规定能够通过文件上传进行提交的文件类型，它的取值是用逗号隔开的 MIME 类型列表。下面就是上述功能的实现：

```html
<input type="file" id="upFile" accept="application/vnd.ms-excel,application/vnd.openxmlformats-officedocument.spreadsheetml.sheet">
```

也可以过滤图片，如

```html
<input type="file" name="pic" id="pic" accept="image/gif, image/jpeg" />
```

上面的写法是只能选择gif和jpeg格式的图片。更多用法请阅读[MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/Input/file)

这种方式很方便，省去了麻烦的文件格式校验，然而，accept 属性并不会验证选中文件的类型. 他只是为开发这提供了一种引导用户做出期望行为的方式而已, 用户还是有办法绕过浏览器的限制。因此, 在服务器端进行文件类型验证是必不可少的。

<div style="text-align:center;">【 全 文 完 】</div>

