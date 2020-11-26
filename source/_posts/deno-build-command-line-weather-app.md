---
title: 在Deno中构建一个命令行天气预报程序
date: 2020-08-21 22:47:15
img: https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202008/deno-build-weather-app/2.png
categories:
  - 技术
tags:
  - 前端
  - Deno
  - 实战
---

在本文中，我们将通过安装 Deno 运行时，并创建一个命令行天气程序，该程序将把一个城市名称作为参数，并返回未来 24 小时的天气预报。

<!-- more -->

要为 Deno 编写代码，我强烈建议将 Visual Studio Code 与官方的[Deno 插件](https://github.com/denoland/vscode_deno)一起使用。为了使事情更有趣，我们将使用 TypeScript 编写应用程序。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202008/deno-build-weather-app/deno.jpg)

## 安装 Deno

首先，让我们把 Deno 安装到本地，这样我们就可以开始编写脚本了。这个过程很简单，因为三大操作系统都有安装脚本。

### Windows

在 Windows 上，你可以从 PowerShell 安装 Deno：

```powershell
iwr https://deno.land/x/install/install.ps1 -useb | iex
```

### Linux

在 Linux 终端上，可以使用以下命令：

```sh
curl -fsSL https://deno.land/x/install/install.sh |  sh
```

### macOS

在 Mac 上，可以将 Brew 与 Deno 一起安装：

```sh
brew install deno
```

## 安装后

安装过程完成后，你可以通过运行以下命令检查 Deno 是否正确安装。

```sh
deno --version
```

你现在应该看到类似以下内容：

```sh
deno 1.2.0
v8 8.5.216
typescript 3.9.2
```

让我们为我们的新项目创建一个文件夹（在你的 home 文件夹内，或者任何你喜欢保存代码项目的地方）并添加一个 `index.ts` 文件。

```sh
mkdir weather-app
cd weather-app
code index.ts
```

> 注意：正如我上面提到的，我在本教程中使用 VS Code。如果你使用的是不同的编辑器，请替换上面最后一行。

## 获取用户输入

我们的程序将检索给定城市的天气预报，因此在运行该程序时，我们需要接受城市名称作为参数。提供给 Deno 脚本的参数以 `Deno.args` 的形式存在。让我们把这个变量记录到控制台，看看它是如何工作的。

```javascript
console.log(Deno.args);
```

现在，使用以下命令运行脚本：

```sh
deno run index.ts --city 杭州
```

你应该看到以下输出：

```javascript
["--city", "杭州"];
```

尽管我们可以自己解析此参数数组，但 Deno 的标准库包括一个名为[flags](https://deno.land/std/flags)的模块，它将为我们解决这一问题。要使用它，我们要做的就是在文件顶部添加一个 `import` 语句：

```javascript
import { parse } from "https://deno.land/std@0.61.0/flags/mod.ts";
```

> 注意：标准库模块的文档中的例子会给你一个未版本化的 URL(如https://deno.land/std/flags/mod.ts)，它将始终指向最新版本的代码。在你的导入中指定一个版本是一个很好的做法，以确保你的程序不会被未来的更新所破坏。

让我们使用导入的函数将参数数组解析为更有用的内容：

```javascript
const args = parse(Deno.args);
```

我们还将修改脚本来打印新的 `args` 变量，看看是什么样子的。所以现在你的代码应该是这样的：

```javascript
import { parse } from "https://deno.land/std@0.61.0/flags/mod.ts";

const args = parse(Deno.args);

console.log(args);
```

现在，如果使用与以前相同的参数运行脚本，则应该看到以下输出：

```sh
Download https://deno.land/std@0.61.0/flags/mod.ts
Download https://deno.land/std@0.61.0/_util/assert.ts
Check file:///home/njacques/code/weather-app/index.ts
{ _: [], city: "杭州" }
```

每当 Deno 运行脚本时，它都会检查新的 import 语句。任何远程托管的导入都将被下载，编译和缓存以备将来使用。`parse` 函数为我们提供了一个对象，该对象具有包含我们输入内容的 `city` 属性。

> 注意：如果你因为任何原因需要为一个脚本重新下载导入，你可以运行 deno cache --reload index.ts。

我们还应该增加对 `city` 参数的检查，如果没有提供城市参数，则以错误信息退出程序。

```javascript
if (args.city === undefined) {
  console.error("No city supplied");
  Deno.exit();
}
```

## 使用天气 API

我们将从 tianqiapi.com 获取预报数据。你需要注册一个免费账户，以获得一个 API 密钥。我们将使用他们的专业七日天气接口，传递一个城市名称作为参数。

![https://www.tianqiapi.com/index/doc](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202008/deno-build-weather-app/1.png)

让我们添加一些代码以获取天气预报并将其打印到控制台，以查看得到的结果：

```javascript
import { parse } from "https://deno.land/std@0.61.0/flags/mod.ts";

const args = parse(Deno.args);

if (args.city === undefined) {
  console.error("No city supplied");
  Deno.exit();
}

const appid = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";
const appsecret = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

const res = await fetch(
  `https://yiketianqi.com/api?version=v9&appid=${appid}&appsecret=${appsecret}`
);
const data = await res.json();

console.log(data);
```

Deno 尝试在可能的情况下支持许多浏览器 API，因此在这里我们可以使用 `fetch` 而不必导入任何外部依赖项。我们还利用了对 await 的支持：通常，我们必须将所有使用 `await` 的代码包装在 `async` 函数中，但是 TypeScript 并没有使我们这样做，这使得代码变得更好了。

如果你现在尝试运行此脚本，则会遇到错误消息：

```
Compile file:///Users/zhangbing/github/CodeTest/Deno/weather-app/index.ts
error: Uncaught PermissionDenied: network access to "https://yiketianqi.com/api?version=v9&appid=xxxxxxxx&appsecret=xxxxxxx", run again with the --allow-net flag
    at unwrapResponse ($deno$/ops/dispatch_json.ts:43:11)
    at Object.sendAsync ($deno$/ops/dispatch_json.ts:98:10)
    at async fetch ($deno$/web/fetch.ts:296:27)
    at async file:///Users/zhangbing/github/CodeTest/Deno/weather-app/index.ts:13:13
```

默认情况下，所有 Deno 脚本都在安全的沙箱中运行：它们无权访问网络，文件系统或诸如环境变量之类的东西。需要为脚本明确授予对其需要访问的系统资源的权限。在这种情况下，错误消息将帮助我们了解所需的权限以及如何启用它。

让我们再次调用脚本，并带有正确的标志：

```sh
deno run --allow-net index.ts --city 杭州
```

这次，我们应该从 API 取回 JSON 响应：

```json
{
  cityid: "101210101",
  city: "杭州",
  cityEn: "hangzhou",
  country: "中国",
  countryEn: "China",
  update_time: "2020-08-13 16:51:27",
  data: [
    {
      day: "13日（星期四）",
      date: "2020-08-13",
      week: "星期四",
      wea: "多云",
      wea_img: "yun",
      wea_day: "多云",
      wea_day_img: "yun",
      wea_night: "多云",
      wea_night_img: "yun",
      tem: "37",
      tem1: "38",
      tem2: "28",
      humidity: "40%",
      visibility: "暂缺",
      pressure: "1002",
      win: [ "西南风", "无持续风向" ],
      win_speed: "4-5级转<3级",
      win_meter: "小于12km/h",
      sunrise: "05:24",
      sunset: "18:43",
      air: "35",
      air_level: "优",
      air_tips: "空气很好，可以外出活动，呼吸新鲜空气，拥抱大自然！",
      alarm: {
        alarm_type: "高温",
        alarm_level: "橙色",
        alarm_content: "杭州市气象台2020年8月13日9时05分发布高温橙色预警信号：受副热带高压控制，预计今天主城区和钱塘新区最高气温将达38℃左右，请注意做好防暑降温等工作。（预警信息来源：国家预警信息发布中心）"
      },
      hours: [
        [Object], [Object],
        [Object], [Object],
        [Object], [Object],
        [Object], [Object],
        [Object], [Object],
        [Object], [Object],
        [Object], [Object],
        [Object], [Object]
      ],
      index: [ [Object], [Object], [Object], [Object], [Object], [Object] ]
    },
    ... ...
  ]
}
```

返回字段的含义可以查看[API 文档](https://www.tianqiapi.com/index/doc?version=v9)。`data` 数组就是每日数据列表，1-7 日，共 7 组。每个对象中包含气象预警(`alarm`)、小时预报(`hours`)、生活指数(`index`)、空气质量指数(`aqi`) 数据。

为了简单起见，我们只获取几个简单的数据：日期、天气、实时温度空和空气质量等级四个数据，为此需要遍历数组：

```javascript
const forecast = data.data.map((item) => [
  item.day, // 日期
  item.wea, // 天气
  item.tem, // 实时温度
  item.air_level, // 空气质量等级
]);
```

如果我们现在尝试运行脚本，我们会得到一个编译错误（如果你使用像 VS 代码这样的 IDE，在键入代码时也会得到这个错误）：**参数 ' item’ 隐式地具有一个 ‘any' 类型。**

TypeScript 要求我们告诉它该 `item` 是什么类型的变量，以便知道我们是否对它做了任何可能在运行时导致错误的事情。让我们添加一个接口，以描述 `item` 的结构：

```typescript
interface forecastItem {
  day: string;
  wea: string;
  tem: string;
  air_level: string;
}
```

让我们将新类型添加到 map 回调中：

```typescript
const forecast = data.data.map((item: forecastItem) => [
  item.day, // 日期
  item.wea, // 天气
  item.tem, // 实时温度
  item.air_level, // 空气质量等级
]);
```

如果你使用的 IDE 支持 TypeScript，它应该能够在你输入时自动完成 `item` 的类型，这要感谢我们提供的接口类型。

现在运行结果输入如下：

```javascript
forecast[
  (["13日（星期四）", "多云转晴", "36", "优"],
  ["14日（星期五）", "晴", "37", ""],
  ["15日（星期六）", "晴", "37", ""],
  ["16日（星期日）", "多云转晴", "37", ""],
  ["17日（星期一）", "晴", "37", ""],
  ["18日（星期二）", "晴", "37", ""],
  ["19日（星期三）", "晴", "38", ""])
];
```

## 格式化输出

现在我们有了所需的数据集，接下来让我们来看一下如何格式化它以便显示给用户。让我们使用 [ascii_table](https://deno.land/x/ascii_table) 模块将其显示在整洁的小表中：

```typescript
import AsciiTable from 'https://deno.land/x/ascii_table/mod.ts';

...

const table = AsciiTable.fromJSON({
  title: `${args.city}七日天气预报`,
  heading: [ '日期', '天气', '实时温度', '风', '空气质量', '天气预警'],
  rows: forecast
})

console.log(table.toString())
```

保存并运行脚本，现在我们应该已经对接下来的 7 日内的所选城市进行了格式化并给出了预报：

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202008/deno-build-weather-app/2.png)

## 完整代码清单

这是一个紧凑的脚本，但是下面是完整的代码清单，也可去我们的 [Github](https://github.com/dunizb/CodeTest/tree/master/Deno/weather-app)。

```typescript
import { parse } from "https://deno.land/std@0.61.0/flags/mod.ts";
import AsciiTable from "https://deno.land/x/ascii_table/mod.ts";

const args = parse(Deno.args);

if (args.city === undefined) {
  console.error("No city supplied");
  Deno.exit();
}

// 你自己的API密钥
const appid = "xxxxxxxx";
const apiKey = "xxxxxxxxx";

const res = await fetch(
  `https://yiketianqi.com/api?version=v9&appid=${appid}&appsecret=${apiKey}`
);
const data = await res.json();

interface forecastItem {
  day: string;
  wea: string;
  tem: string;
  air_level: string;
}

const forecast = data.data.map((item: forecastItem) => [
  item.day, // 日期
  item.wea, // 天气
  item.tem, // 实时温度
  item.air_level, // 空气质量等级
]);

const table = AsciiTable.fromJSON({
  title: `${args.city}七日天气预报`,
  heading: ["日期", "天气", "温度", "空气质量"],
  rows: forecast,
});

console.log(table.toString());
```

## 总结

现在，你有了自己的正在运行的 Deno 命令行程序，该程序将为你提供接下来 7 日天气预报。通过遵循本教程，你现在应该熟悉如何启动新程序，从标准库和第三方导入依赖项以及授予脚本权限。

那么，在尝到了为 Deno 编写程序的甜头之后，接下来你应该去哪里呢？你觉得 Deno 如何？
