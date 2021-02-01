---
title: 小程序云开发和生成海报实践：编程日历小程序
date: 2021-01-25 20:26:57
img: http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202101/Programming-calendar-wxapp/shareImg.jpg
cover: true
categories:
  - 工具
tags:
  - 小程序开发
---

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202101/Programming-calendar-wxapp/1.png)

## 1、起源

朋友圈晒的很多的一本日历书《了不起的程序员 2021》，我也买了，很厚，纸质书嘛，现在已经很少看了，加上这是一本日历书，希望是每天都打开看。可实际上的情况是，要么忘记看今天的内容，要么一口气看了好几天的内容，然后剩下几天又不看了。

后来《了不起的程序员 2021》在 Github 开源了，

<!-- more -->

于是乎！我就想做一个小程序，因为手机每天打开的频率太高了，碎片时间也很多，加上小程序的不用安装用完即走的优点，使用方便，不会有压力感。

再加上自己还没有一款正儿八经的小程序作品，对现在很火的云开发也没怎么用过，特别是小程序云开发，他他到底用起来爽不爽呢？（很爽！）

于是乎！开干！

## 2、产品设计

这是最伤脑筋的部分，小程序到底要做成什么样，画个原型图？作为一个『资深』程序员，从来没正经画过原型和设计。手足无措，改用什么工具？虽然我知道有 Sketch 这个神器，还很多在线设计工具，比如磨刀，但从来没用过啊，最后硬着头皮用磨刀画了画原型，很简陋的原型，就是线框图级别。

如下是我画的原型....

磨刀链接：[https://modao.cc](https://modao.cc/app/c9f6d109bce5857e3cb0cb5e1969b3942392e574?simulator_type=device&sticky)

<iframe src="https://modao.cc/app/c9f6d109bce5857e3cb0cb5e1969b3942392e574/embed/v2" allowTransparency="true" 
style="width:640px;height:660px"
frameborder="0"></iframe>

这个过程不断有新的想法，所以改来该去，产品设计花了好几天，学习怎么画原型，实现脑子里乱七八糟的各种想法。

在这个过程中我不断的给自己家需求，一度增加了什么历史上的今天、知乎日历等等各种内容，最后还是被自己**狠心**一一毙掉了，只留下纯粹的编程日历内容。

小程序比《了不起的程序员 2021》更好的方面：

1. 真实配图，《了不起的程序员 2021》的配图都是手绘图，看起来少了点那种味儿。。。
2. 小程序支持收藏、分享，这是纸质书先天不具备的
3. 基于《了不起的程序员 2021》但不是完全一样，我做了一些小小的修改或增加一些内容。

## 3、开发

产品设计阶段和开发阶段占用的时间比大概是 **8:2** 左右，有了原型开发很快，毕竟也没什么复杂的东西。

下面重点说一下分享海报功能的实现吧。

### 3.1、选择海报分享方案

在开发分享海报功能之前我也看了下网上大致的方案，最后我选择了微信小程序自己的扩展组件：[wxml-to-canvas](https://developers.weixin.qq.com/miniprogram/dev/extended/component-plus/wxml-to-canvas.html)，小程序内通过静态模板和样式绘制 canvas ，导出图片，可用于生成分享图等场景。

我为什么不用其他方案：

- 手写 canvas，太麻烦
- 后端生成前端获取，太麻烦，我这个小程序很简单没必要
- 开源小程序海报组件，尝试过一个，感觉也不太好用，有些没文档用起来吃力

上图，是骡子是马拉出来遛遛，下图的的海报就是通过 wxml-to-canvas 动态绘制的。

<img src="http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202101/Programming-calendar-wxapp/haibao.png" width="320" />

### 3.2、引入 wxml-to-canvas 组件

wxml-to-canvas 的限制很多，第一次没经验的话觉得很难用，如果再让我做一次，我就快很多了。

官方的示例只单纯教你怎么生成海报，缺乏上下文和怎么整合进你的项目及逻辑，需要费一下脑子。

**Step1. npm 安装，参考 小程序 npm 支持**

```shell
npm install --save wxml-to-canvas
```

**Step2. JSON 组件声明**

```json
{
  "usingComponents": {
    "wxml-to-canvas": "wxml-to-canvas"
  }
}
```

**Step3. wxml 引入组件**

```html
<view class="share-image-container">
  <wxml-to-canvas
    id="canvas"
    width="{{canvasWidth}}"
    height="{{canvasHeight}}"
  ></wxml-to-canvas>
</view>
```

### 3.3、海报分享逻辑说明

点击编程日历小程序底部的海报分享按钮，在当前页面生成 canvas 预览图，然后再生成图片跳转到海报图片预览和保存页面。

上面的 `.share-image-container` 类如下：

```css
.share-image-container {
  border: 1px solid red;
  position: absolute;
  transform: translateY(-1000%);
  bottom: 0;
  z-index: 0;
}
```

即在页面外生成 canvas，也是在这里调试 wxml-to-canvas 组件效果的地方，去掉该类的样子如下：

<img src="http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202101/Programming-calendar-wxapp/6.png" style="width: 320px" />

### 3.4、js 获取实例

**Step4. js 获取实例**

```js
import RenderCodeToWXML from "./renderCodeWXML.js";

Page({
  data: {
    canvasWidth: 373,
    canvasHeight: 720,
    bannerImgHeight: 240,
    bannerImgWdith: 320,
  },
  renderToCanvas() {
    wx.showLoading({
      title: "处理中...",
    });
    this.canvas = this.selectComponent("#canvas");
    const {
      canvasWidth,
      canvasHeight,
      bannerImgWdith,
      bannerImgHeight,
    } = this.data;
    let renderToWXML = new RenderCodeToWXML(
      canvasWidth,
      canvasHeight,
      bannerImgWdith,
      bannerImgHeight
    );

    const wxml = renderToWXML.renderWXML();
    const style = renderToWXML.renderStyle();
    const p1 = this.canvas.renderToCanvas({ wxml, style });
    p1.then((res) => {
      // console.log('container', res.layoutBox)
      app.globalData.container = res;
      this.container = res;
      this.extraImage();
    }).catch((err) => {
      wx.hideLoading();
      console.log("err", err);
    });
  },
  extraImage() {
    const p2 = this.canvas.canvasToTempFilePath();
    p2.then((res) => {
      wx.hideLoading();
      // app.globalData.share = res
      wx.navigateTo({
        url: "../shareImage/shareImage",
        success: function(res2) {
          // 通过eventChannel向被打开页面传送数据
          res2.eventChannel.emit(
            "acceptDataFromOpenerPage",
            {
              share: res,
              container: app.globalData.container,
              tab: app.globalData.tab,
              date: app.globalData.dateInfo.strings,
            }
          );
        },
      });
    }).catch((err) => {
      wx.hideLoading();
      wx.showToast({
        title: err,
        icon: "none",
      });
    });
  },
});
```

这里主要就是从 `renderCodeWXML.js` 中获取 WXML 和 Style，然后调用 canvas 的 `renderToCanvas` 方法进行渲染：

```js
const wxml = renderToWXML.renderWXML();
const style = renderToWXML.renderStyle();
const p1 = this.canvas.renderToCanvas({ wxml, style });
```

最后在 `p1.then` 里调用 `this.extraImage();` 方法跳转到下一个页面，并通过 `eventChannel.emit` 方式传递参数。

来看看 `renderCodeWXML.js` 里面有什么：

```js
const app = getApp();

export default class RenderDataToWXML {
  constructor(
    canvasWidth,
    canvasHeight,
    imgWidth,
    imgHeight
  ) {
    this.canvasWidth = canvasWidth;
    this.canvasHeight = canvasHeight;
    this.imgWidth = imgWidth;
    this.imgHeight = imgHeight;
  }

  renderWXML() {
    const { dateInfo, data, userInfo } = app.globalData;
    const openId = wx.getStorageSync("openId");
    let pData = "";
    let pMore = "";
    let banner = "";

    if (data.data.event) {
      pData = data.data.event.join("");
    }
    if (data.data.coding) {
      pData = data.data.coding.join("");
    }
    if (data.data.landmark) {
      pData = data.data.landmark.join("");
    }

    if (data.data.more) {
      pMore = data.data.more[0];
    } else if (data.data.people) {
      pMore = data.data.people[0].split("：").join("，");
    } else {
      pMore = "";
    }

    if (data.data.img) {
      banner = `
      <view class="banner">
        <image class="banner-image" mode="aspectFit" src="${data.data.img.url}" />
      </view>`;
    }

    if (pData.length >= 156) {
      pData = pData.substring(0, 152) + "...";
    }
    if (pMore.length >= 50) {
      pMore = pMore.substring(0, 48) + "...";
    }

    let avatar = "";
    if (userInfo && userInfo.avatarUrl) {
      avatar = `<view class="avatar">
        <image class="avatar-image" src="${userInfo.avatarUrl}" />
        <text class="avatar-nikename">${userInfo.nickName}邀请你使用</text>
      </view>`;
    }

    let wxmlMore = pMore;
    if (wxmlMore) {
      wxmlMore = `
        <view>
          <text class="p-more">${pMore}</text>
        </view>
      `;
    }

    const wxml = `
      <view class="container">
        <view class="top">
          <view class="top-left">
            <view><text class="en">${dateInfo.date.monthEN}</text></view>
            <view><text class="cn">${dateInfo.lunarDate}</text></view>
          </view>
          <view><text class="top-center">${dateInfo.date.day}</text></view>
          <view class="top-right">
            <view><text class="en">${dateInfo.date.weekEN}</text></view>
            <view><text class="cn">${dateInfo.date.weekCN}</text></view>
          </view>
        </view>
        ${banner}
        <view class="middle">
          <view>
            <text class="p-data">${pData}</text>
          </view>
          ${wxmlMore}
        </view>
        <view class="qrcode">
          <view class="appinfo">
            ${avatar}
            <view><text class="appname">编程日历</text></view>
            <view><text class="appdesc">程序员专属日历，最极客日历</text></view>
          </view>
          <view class="qrcode-image">
            <image class="image" mode="aspectFit" src="https://7072-programming-calendar-3b8b7a7d082-1304448256.tcb.qcloud.la/qr/${openId}-qr.png?sign=b5a610dc6ae15c9427720ab617a2f18a&t=1609438339" />
          </view>
        </view>
      </view>
      `;
    return wxml;
  }

  // canvas样式
  renderStyle() {
    const contentWidth = this.canvasWidth - 50;
    const mainColor = "#1296db";
    const style = {
      container: {
        width: this.canvasWidth,
        height: this.canvasHeight,
        backgroundColor: "#fff",
      },
      top: {
        width: this.canvasWidth,
        height: 82,
        backgroundColor: mainColor,
        flexDirection: "row",
        justifyContent: "space-around",
        alignItems: "center",
      },
      topLeft: {
        width: this.canvasWidth / 3,
        height: 82,
        textAlign: "center",
        alignItems: "center",
      },
      topCenter: {
        width: this.canvasWidth / 3,
        height: 82,
        lineHeight: 82,
        fontSize: 72,
        textAlign: "center",
        color: "#ffffff",
      },
      topRight: {
        width: this.canvasWidth / 3,
        height: 82,
      },
      en: {
        width: this.canvasWidth / 3,
        height: 30,
        fontSize: 20,
        textAlign: "center",
        color: "#ffffff",
        marginTop: 15,
      },
      cn: {
        width: this.canvasWidth / 3,
        height: 30,
        textAlign: "center",
        color: "#ffffff",
      },
      banner: {
        width: this.canvasWidth,
        flexDirection: "row",
        justifyContent: "center",
        marginTop: 20,
      },
      bannerImage: {
        width: this.imgWidth,
        height: this.imgHeight,
      },
      middle: {
        flexDirection: "column",
        justifyContent: "center",
        alignItems: "center",
        marginTop: 20,
      },
      pData: {
        width: contentWidth,
        height: 170,
        lineHeight: "1.8em",
      },
      pMore: {
        width: contentWidth,
        height: 60,
        lineHeight: "1.8em",
      },
      qrcode: {
        height: 130,
        flexDirection: "row",
        justifyContent: "space-between",
        backgroundColor: "#CCE6FF",
        paddingLeft: 20,
        paddingTop: 20,
      },
      qrcodeImage: {
        width: 90,
        height: 90,
        marginRight: 20,
        borderRadius: 45,
        flexDirection: "row",
        justifyContent: "center",
        alignItems: "center",
        backgroundColor: "#fff",
      },
      image: {
        width: 90,
        height: 90,
        scale: 0.9,
        borderRadius: 45,
      },
      appinfo: {
        flexDirection: "column",
        justifyContent: "flex-start",
        alignItems: "flex-start",
        height: 80,
      },
      avatar: {
        flexDirection: "row",
        justifyContent: "flex-start",
        width: this.canvasWidth / 1.8,
        height: 30,
      },
      avatarImage: {
        width: 30,
        height: 30,
        borderRadius: 15,
        marginRight: 5,
      },
      avatarNikename: {
        width: this.canvasWidth / 1.8,
        height: 22,
        lineHeight: 22,
        marginTop: 5,
      },
      appname: {
        width: this.canvasWidth / 2,
        height: 23,
        fontSize: 16,
        color: "#0081FF",
        marginTop: 8,
        marginLeft: 35,
      },
      appdesc: {
        width: this.canvasWidth / 2,
        height: 20,
        fontSize: 14,
        marginLeft: 35,
      },
    };
    return style;
  }

  // 省略不相关代码
}
```

该文件就是我们画海报的地方，就是生成 WXML 和 Style 然后导出
。

### 3.5、wxml-to-canvas 组件的注意事项

wxml-to-canvas 组件对 wxml 模板支持有限 :

- 支持 `<view>`、`<text>`、`<image>` 三种标签，通过 `class` 匹配 `style` 对象中的样式。
- 文字必须用 `<text>` 标签包含，否则不显示。并且必须设置宽高。文字宽度必须先确定，超出则会自动截断。所以动态文字可以根据字数，动态设置宽度。

样式方面：

- 对象属性值为对应 wxml 标签的 **cass 驼峰形式**。**需为每个元素指定 width 和 height 属性，否则会导致布局错误**。
- 存在多个 className 时，位置靠后的优先级更高，子元素会继承父级元素的可继承属性。
- **元素均为 flex 布局**。left/top 等 仅在 absolute 定位下生效。

因为文字必须用 `<text>` 标签包含，并且必须设置宽高，文字宽度必须先确定，超出则会自动截断。所以动态文字可以根据字数，动态设置宽度。所以写布局非常麻烦，我推荐大家为每一个元素设置背景，这样可以看到元素渲染的范围和宽高。如下所示：

<img src="http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202101/Programming-calendar-wxapp/7.png" style="width:320px" />

`borderColor/marginBottom/marginTop` 可使用，虽然微信文档中没写。

### 3.6、海报预览和下载页面

生成 canvas 并调用接口生成图片后，我们携带参数跳转到下一个页面，先来看看 WXML，非常简单：

```html
<view>
  <view class="share-container">
    <image
      src="{{src}}"
      mode="widthFix"
      class="image"
      style="height: {{height}}px;"
    ></image>
  </view>
  <view class="save-button">
    <van-button
      bind:tap="saveImage"
      block
      round
      icon="down"
      size="large"
      type="info"
      >保存到手机</van-button
    >
  </view>
</view>
```

js 逻辑

```js
const app = getApp();
Page({
  data: {
    src: "",
    date: "",
    width: "",
    height: "",
  },
  onLoad() {
    const eventChannel = this.getOpenerEventChannel();
    eventChannel.on("acceptDataFromOpenerPage", (data) => {
      // console.log("data", data)
      this.setData({
        showPopup: true,
        date: data.date,
        src: data.share.tempFilePath,
        width: data.container.layoutBox.width,
        height: data.container.layoutBox.height,
      });
    });
  },
  getDatestr() {
    const { strings } = app.globalData.dateInfo;
    return strings;
  },
  saveImage() {
    wx.showLoading({
      title: "处理中...",
    });
    const _this = this;
    wx.getSetting({
      success(res) {
        if (!res.authSetting["scope.writePhotosAlbum"]) {
          wx.authorize({
            scope: "scope.writePhotosAlbum",
            success() {
              _this.save();
            },
            fail() {
              wx.showToast({
                title: "授权失败",
                icon: "none",
              });
            },
          });
        } else {
          _this.save();
        }
      },
    });
  },
  save() {
    wx.saveImageToPhotosAlbum({
      filePath: this.data.src,
      success() {
        wx.showToast({
          title: "保存成功",
          icon: "none",
        });
      },
      fail() {
        wx.showToast({
          title: "保存失败",
          icon: "none",
        });
      },
    });
  },
});
```

**以上就是我开发海报功能的逻辑和代码，仅供参考吧，如果你有相关经验欢迎讨论交流，留下你的真知灼见吧。**

## 编程日历小程序页面截图

最后，分几张小程序的页面截图

|                                            预览                                             |                                            预览                                             |
| :-----------------------------------------------------------------------------------------: | :-----------------------------------------------------------------------------------------: |
| ![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202101/Programming-calendar-wxapp/2.jpg) | ![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202101/Programming-calendar-wxapp/3.jpg) |
| ![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202101/Programming-calendar-wxapp/4.jpg) | ![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202101/Programming-calendar-wxapp/5.jpg) |
