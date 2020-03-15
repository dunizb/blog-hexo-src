---
title: CSS变量实现暗黑模式，我的小铺页面已经支持
date: 2020-03-15 16:09:30
categories:
  - 技术
tags:
  - 前端
  - CSS
---

最近微信被苹果逼的开发了**暗黑模式**，越来越多的网站和应用开始支持了暗黑模式，许多人也喜欢为网站选择暗模式，也许他们更喜欢这样的外观，或者他们想让自己的眼睛免受疲劳。这篇文章将告诉你如何实现一个自动的 CSS 暗模式，根据你的访客的主题来改变。

我在自己的博客页面[我的小铺](https://store.zhangbing.site)页面实践了一下用 `CSS变量` 和 `@media查询` 实现暗黑模式。

<!-- more -->

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/202003/css-dark-mode/0.png)

## CSS Dark Mode

我定义了变量以设置主题的颜色，我建议你也这样做，因为这样会使这个过程容易得多。我的默认模式的颜色变量如下:

```css
:root {
  --accent: #226997;
  --main: #333;
  --light: #666;
  --lighter: #f3f3f3;
  --border: #e6e6e6;
  --bg: #ffffff;
}
```

如果你想在你的样式表中使用这些变量，你可以这样做:

```css
p {
  color: var(--main);
}
```

这样，如果您想更改主题的颜色，则只需修改定义的变量，所有使用该变量的内容都会更新。

现在我们需要定义一组新的变量，这些变量将在调用 CSS 暗模式时使用。

```css
/* 定义 dark 模式的颜色 */
:root {
  --accent: #3493d1;
  --main: #f3f3f3;
  --light: #ececec;
  --lighter: #666;
  --border: #e6e6e6;
  --bg: #333333;
}
```

## 添加 Dark 式支持

现在，我们定义了两组变量，剩下要做的惟一一件事就是将 `preferences -color-scheme` 媒体查询添加到我们的 `dark` 变量中。

带上 Dark 颜色变量并在下面添加 `@media 查询`：

```css
/* 定义 dark 模式的颜色 */
@media (prefers-color-scheme: dark) {
  :root {
    --accent: #3493d1;
    --main: #f3f3f3;
    --light: #ececec;
    --lighter: #666;
    --border: #e6e6e6;
    --bg: #333333;
  }
}
```

就是这样！如果有人使用深色操作系统主题并访问您的网站，您的网站现在将自动切换到黑暗模式。

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/202003/css-dark-mode/1.png)

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/202003/css-dark-mode/2.png)

## 测试

我相信您会希望测试这种更改是否有效。为此，您可以简单地在操作系统上启用一个 dark 主题，例如 iOS dark 主题。

或者，如果你不想在你的操作系统主题上浪费时间，你可以在 Firefox 中强制执行这个测试。方法如下：

1. 打开 Firefox，然后在地址栏中键入 `about:config`，然后按 Enter。

2. 你将被要求承担风险，接受它。

3. 在搜索栏中，搜索 `ui.systemUsesDarkTheme`。

4. 将复选框更改为 `number` 并单击 `+` 符号。

5. 将值更改为 `1` 并单击 tick 按钮。

6. 现在页面应该变黑。

7. 回到您的网站，主题应该已自动更新为黑暗模式。

8. 如果您想要测试它是否切换回来，请将值更改为 `0`。

9. 完成测试后，单击垃圾桶删除该选项。

---

现在，您应该拥有一个网站，该网站不仅在移动界面方面具有响应能力，而且在主题方面也具有响应能力。我敢肯定，您的深夜访客或只喜欢深色主题网站的访客会感谢您。

---

> 参考原文：https://kevq.uk/how-to-add-css-dark-mode-to-a-website/
>
> 翻译：本博客
