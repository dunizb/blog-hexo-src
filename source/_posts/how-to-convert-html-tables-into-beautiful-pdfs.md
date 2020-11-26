---
title: 如何将HTML表格转换成精美的PDF
date: 2020-11-18 19:56:30
img: https://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/convert-html-tables-pdfs/banner.png
categories:
  - 技术
tags:
  - JavaScript
  - 翻译
  - 开源推荐
---

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/convert-html-tables-pdfs/banner.png)

作为开发人员，如何让 PDF 输出看起来更专业？大多数免费的在线 PDF 导出器实际上只是将 HTML 内容转换为 PDF，而不进行任何额外的格式化，这会使数据难以阅读。如果你也能添加诸如页眉和页脚、页码或重复的表列标题等内容呢？像这样的小点缀，对把一份看起来很业余的文件变成一份优雅的文件有很大的帮助。

<!-- more -->

最近，我探索了几种生成 PDF 的解决方案，并建立了这个[Demo 程序](http://tylerhawkins.info/html-to-pdf-demo/)来展示结果。所有的代码也可以在[Github](https://github.com/thawkin3/html-to-pdf-demo)上找到。让我们开始吧！

## Demo 程序概述

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/convert-html-tables-pdfs/1.png)

我们的 Demo 程序包含一个冗长的样式表和四个将表导出为 PDF 的按钮。该应用是用基本的 HTML、CSS 和 JavaScript 构建的，但你可以使用你的 UI 框架或选择的库轻松创建相同的输出。

每个导出按钮都使用不同的方法生成 PDF。从右到左查看，第一个使用原生浏览器打印功能，第二个使用名为[jsPDF](https://github.com/MrRio/jsPDF)的开源库，第三个使用另一个名为[pdfmake](http://pdfmake.org/)的开源库，最后，第四个使用名为[DocRaptor](https://docraptor.com/)的付费服务。

让我们一一探讨每个解决方案。

## 原生浏览器打印功能

首先，我们考虑使用浏览器的内置工具导出 PDF。在查看任何网页时，你可以通过右键单击任意位置，然后从菜单中选择“打印”选项来轻松地打印页面。这将打开一个对话框，供你选择打印设置。但是，你实际上不必打印文档。对话框还提供了将文档保存为 PDF 的选项，这就是我们要做的。在 JavaScript 中 `window` 对象公开了一个 `print` 方法，所以我们可以写一个简单的 JavaScript 函数，并将其附加到我们的一个按钮上，就像这样：

```javascript
function downloadPDFWithBrowserPrint() {
  window.print();
}
document
  .querySelector("#browserPrint")
  .addEventListener("click", downloadPDFWithBrowserPrint);
```

以下是 Google Chrome 浏览器的输出：

![使用内置打印功能和Chrome浏览器导出的PDF](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/convert-html-tables-pdfs/2.png)

我对这里的输出感到惊喜，虽然它并不华丽——内容只是黑白色的，但主要的表格样式却被完整地保留了下来。此外，这七个页面中的每一个都包含表列标题和页脚，我认为浏览器可以智能地获取这些信息，这是由于我在构建结构合理的表时选择了语义 HTML。

然而，我不喜欢浏览器在 PDF 中包含的额外页面元数据。靠近顶部，我们看到日期和 HTML 页面标题。在页面的底部，我们看到了打印这篇文章的网站以及页码。

如果我保存这个文档的唯一目的是为了看数据，那么 Chrome 浏览器做得很好。不过，文档顶部和底部多出的几行文字虽然有用，但并没有让它看起来很专业。

另外需要注意的是，不同浏览器的原生打印功能是不一样的。如果我们用 Safari 浏览器打印同样的文档呢？输出如下：

![使用内置打印功能和Safari浏览器导出的PDF](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/convert-html-tables-pdfs/3.png)

你会注意到表格看起来大致相同，页面页眉和页脚内容也是如此。但是，表列标题和表脚不重复！这是没有帮助的，因为当你忘记任何给定列包含什么数据时，你需要返回到第一页。第一页的表格底部也有点被切断，因为浏览器试图在创建下一页之前尽可能多地挤进内容。

如此看来，浏览器的输出并不理想，会因用户选择的浏览器不同而不同。

## jsPDF

接下来让我们考虑一个名为 jsPDF 的开源库。这个库已经存在了至少 5 年，每周从 NPM 的下载量持续超过 20 万次。可以说这是一个很受欢迎的、经过实战检验的库。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/convert-html-tables-pdfs/4.png)

jsPDF 的使用也相当简单。你可以创建一个新的 jsPDF 类的实例，给它一个你想导出的 HTML 内容的引用，然后提供任何其他附加的设置，如页边距大小或文档标题。

```javascript
function downloadPDFWithjsPDF() {
  var doc = new jspdf.jsPDF("p", "pt", "a4");

  doc.html(document.querySelector("#styledTable"), {
    callback: function(doc) {
      doc.save("MLB World Series Winners.pdf");
    },
    margin: [60, 60, 60, 60],
    x: 32,
    y: 32,
  });
}

document
  .querySelector("#jsPDF")
  .addEventListener("click", downloadPDFWithjsPDF);
```

在内部，jsPDF 使用了一个名为[html2canvas.](https://html2canvas.hertzen.com/)的库。顾名思义，html2canvas 接收 HTML 内容，并将其转化为存储在 HTML `<canvas>` 元素上的图像，然后 jsPDF 接收该画布内容并将其保存。

让我们看一下使用 jsPDF 的输出：

![使用jsPDF导出的PDF](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/convert-html-tables-pdfs/5.png)

乍一看，这看起来还不错！ PDF 包含我们漂亮的蓝色标题和条纹表行背景。它不包含浏览器打印方法所包含的任何多余页面元数据。

但是，请注意在第一页和第二页之间发生了什么。表格一直延伸到第一页的底部，然后在第二页的顶部直接接上。没有应用额外的边距，而且表文本内容有可能被切成两半。

该 PDF 也不包括重复的表列标题或表脚，这与我们在 Safari 的打印功能中看到的问题相同。

虽然 jsPDF 是一个强大的库，但当导出的内容只能容纳在一个页面上时，这个工具似乎效果最好。

## pdfmake

让我们看一下我们的第二个开源库 pdfmake。NPM 每周下载量超过 30 万次，寿命长达 7 年，这个库甚至比 jsPDF 更受欢迎，更资深。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/convert-html-tables-pdfs/6.png)

在为我的 demo 程序构建导出功能时，pdfmake 的配置要比 jsPDF 难得多。原因是 pdfmake 使用你提供的数据从头开始构建 PDF 文档，而不是将页面上现有的 HTML 内容转换为 PDF。这意味着，我必须为它提供 PDF 表格的页眉、页脚、内容和布局的数据，而不是为 pdfmake 提供一个对我的 HTML 表格的引用。这导致我的代码有很多重复，我先在 HTML 中写了表格，然后用 pdfmake 为 PDF 导出重新建表。

代码如下：

```javascript
function downloadPDFWithPDFMake() {
  var tableHeaderText = [
    ...document.querySelectorAll(
      "#styledTable thead tr th"
    ),
  ].map((thElement) => ({
    text: thElement.textContent,
    style: "tableHeader",
  }));

  var tableRowCells = [
    ...document.querySelectorAll(
      "#styledTable tbody tr td"
    ),
  ].map((tdElement) => ({
    text: tdElement.textContent,
    style: "tableData",
  }));
  var tableDataAsRows = tableRowCells.reduce(
    (rows, cellData, index) => {
      if (index % 4 === 0) {
        rows.push([]);
      }

      rows[rows.length - 1].push(cellData);
      return rows;
    },
    []
  );

  var docDefinition = {
    header: {
      text: "MLB World Series Winners",
      alignment: "center",
    },
    footer: function(currentPage, pageCount) {
      return {
        text: `Page ${currentPage} of ${pageCount}`,
        alignment: "center",
      };
    },
    content: [
      {
        style: "tableExample",
        table: {
          headerRows: 1,
          body: [tableHeaderText, ...tableDataAsRows],
        },
        layout: {
          fillColor: function(rowIndex) {
            if (rowIndex === 0) {
              return "#0f4871";
            }
            return rowIndex % 2 === 0 ? "#f2f2f2" : null;
          },
        },
      },
    ],
    styles: {
      tableExample: {
        margin: [0, 20, 0, 80],
      },
      tableHeader: {
        margin: 12,
        color: "white",
      },
      tableData: {
        margin: 12,
      },
    },
  };
  pdfMake
    .createPdf(docDefinition)
    .download("MLB World Series Winners");
}

document
  .querySelector("#pdfmake")
  .addEventListener("click", downloadPDFWithPDFMake);
```

在我们完全否定 pdfmake 之前，我们先来看看输出。

![使用pdfmake导出的PDF](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/convert-html-tables-pdfs/7.png)

不是太寒酸！我们可以为表包含样式，这样我们仍然可以复制蓝色列标题和条纹表行背景。我们还得到了重复的表列标题，以便于跟踪我们在每个页面的每个列中看到的数据。

pdfmake 还允许我加入页眉和页脚，所以很容易添加页码。但你会注意到，第一页和第二页之间的表格内容仍然没有完全分开。分页符将 2002 年的一行部分地分割在两页之间。

总体看来，pdfmake 最大的优势在于从头开始构建 PDF。例如，如果你想根据某些订单数据生成发票，而你实际上并没有在 web 应用程序的页面上显示发票，那么 pdfmake 将是一个很好的选择。

## DocRaptor

最后一个我们要考虑的选项是 DocRaptor。DocRaptor 与我们探讨的前三个选项不同的是，它是一种付费服务。它使用 Prince HTML-to-PDF 引擎来生成其 PDF 输出。该服务也通过 API 使用，因此你的代码会碰到一个外部 API 端点，然后该端点会返回 PDF 文档。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/convert-html-tables-pdfs/8.png)

DocRaptor 的基本配置相当简单，你向它提供你的文档名称，你要创建的文档类型（在我们的例子中是 `’pdf'`），以及要使用的 HTML 内容。根据你的需要，还有数百种不同配置的选择，但基本配置是一个很好的起点。

这是我使用的：

```javascript
function downloadPDFWithDocRaptor() {
  DocRaptor.createAndDownloadDoc("YOUR_API_KEY_HERE", {
    test: true, // test documents are free, but watermarked
    type: "pdf",
    name: "MLB World Series Winners",
    document_content: document.querySelector("html")
      .innerHTML,
  });
}

document
  .querySelector("#docRaptor")
  .addEventListener("click", downloadPDFWithDocRaptor);
```

让我们看一下 DocRaptor 生成的 PDF 导出：

![使用DocRaptor导出的PDF](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/convert-html-tables-pdfs/9.png)

现在有一个好看的文档了! 我们可以保留我们漂亮的表格样式。表格的列头和表脚在每一页上都是重复的，表格的行数不会被切掉，而且页面四面都有适当大小的边距，每个页面的页眉也是重复的，每个页面底部的页码也是重复的。

要创建页眉和页脚文本，DocRaptor 建议你使用一些 CSS 与 `@page` 选择器，就像这样。

```css
@page {
  margin-top: 80px;
  margin-bottom: 80px;
  @top {
    content: "MLB World Series Winners";
  }
  @bottom {
    /* 具有计数器功能的页脚可插入页面计数器 */
    content: "Page " counter(page) " of " counter(pages);
  }
}
```

在 PDF 输出方面，DocRaptor 无疑是赢家。

## 总结

那么，你的应用要选择哪种方案呢？如果你想要最简单的解决方案，而且不需要专业的文档，那么原生浏览器的打印功能应该就可以了。如果你需要对 PDF 输出进行更多的控制，那么你就需要使用一个库。

当涉及到基于 UI 中显示的 HTML 生成的单页内容时，jsPDF 就会大放异彩。pdfmake 在从数据而不是 HTML 中生成 PDF 内容时效果最好。DocRaptor 是其中功能最强大的一款，它拥有简单的 API 和漂亮的 PDF 输出。但同样，与其他不同的是，它是一项付费服务。然而，如果你的业务依赖于优雅、专业的文档生成，DocRaptor 是非常值得的。

---

原文：[https://levelup.gitconnected.com/](https://levelup.gitconnected.com/how-to-convert-html-tables-into-beautiful-pdfs-eac2ce4c77de)

作者：Tyler Hawkins
