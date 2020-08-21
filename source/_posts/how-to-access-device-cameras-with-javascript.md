---
title: 如何使用JavaScript访问设备摄像头（前后）
date: 2020-06-08 22:24:58
categories:
  - 技术
tags:
  - 前端
  - JavaScript
  - 实战
---

在这篇文章中，我将向您展示如何通过 JavaScript 在网页上访问设备的摄像头，并支持多种浏览器，而无需外部库。

<!-- more -->

## 如何使用相机 API

要访问用户的相机（或麦克风），我们使用 JavaScript **[MediaStream API](https://developer.mozilla.org/en-US/docs/Web/API/Media_Streams_API)**。该 API 允许通过流访问这些设备捕获的视频和音频。

第一步是检查浏览器是否支持此 API：

```javascript
if ("mediaDevices" in navigator && "getUserMedia" in navigator.mediaDevices) {
  // ok, 浏览器支持它
}
```

在现代浏览器中，支持是不错的（当然没有 Internet Explorer）。

## 捕获视频流

要捕获由摄像机生成的视频流，我们使用 `mediaDevices` 对象的 `getUserMedia` 方法。这个方法接收一个对象，其中包含我们要请求的媒体类型（视频或音频）和一些要求。首先，我们可以通过 `{video: true}` 来获取摄像机的视频。

```javascript
const videoStream = await navigator.mediaDevices.getUserMedia({ video: true });
```

此调用将询问用户是否允许访问摄像机。如果用户拒绝，它将引发异常并且不返回流。因此，必须在 `try/catch` 块内完成处理这种情况。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202006/cameras-with-javascript/2.png)

请注意，它返回一个 Promise，因此您必须使用 `async/await` 或 `then` 块。在 Mac OS 系统上还会弹出授权

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202006/cameras-with-javascript/3.png)

点击“好”，就可以访问电脑摄像头了，控制台输出的 `videoStream` 对象如下

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202006/cameras-with-javascript/4.png)

## 视频规格（requirements）

我们可以通过传递有关所需分辨率以及最小和最大限制的信息来改善视频的要求：

```javascript
const constraints = {
  video: {
    width: {
      min: 1280,
      ideal: 1920,
      max: 2560,
    },
    height: {
      min: 720,
      ideal: 1080,
      max: 1440,
    },
  },
};

const videoStream = await navigator.mediaDevices.getUserMedia(constraints);
```

这样，流以正确的宽度和高度比例进入，如果它是处于纵向模式的手机，则需要进行尺寸反转。

## 在页面上显示视频

既然有了流，我们该如何处理？

我们可以在页面上的 `video` 元素中显示视频：

```javascript
// 页面中有一个 <video autoplay id="video"></video> 标签
const video = document.querySelector("#video");
const videoStream = await navigator.mediaDevices.getUserMedia(constraints);
video.srcObject = videoStream;
```

请注意 `video` 标签中的自动播放属性 `autoplay`，没有它，你需要调用 `video.play()` 才能真正开始显示图像。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202006/cameras-with-javascript/5.png)

## 访问手机的前后摄像头

默认情况下，`getUserMedia` 将使用系统默认的视频录制设备。如果是有两个摄像头的手机，它使用前置摄像头。

要访问后置摄像头，我们必须在视频规格中包括 `faceModeMode:"environment"`：

```javascript
const constraints = {
  video: {
    width: { ... },
    height: { ... },
    facingMode: "environment"
  },
};
```

默认值为 `faceingMode:"user"`，即前置摄像头。

需要注意的是，如果你想在已经播放视频的情况下更换摄像机，你需要先停止当前的视频流，然后再将其替换成另一台摄像机的视频流。

```javascript
videoStream.getTracks().forEach((track) => {
  track.stop();
});
```

## 截屏

你可以做的另一件很酷的事情是捕获视频的图像（屏幕快照）。

你可以在 canvas 上绘制当前视频帧，例如：

```javascript
// 页面中有一个 <canvas id="canvas"></canvas> 标签
const canvas = document.querySelector("#canvas");
canvas.width = video.videoWidth;
canvas.height = video.videoHeight;
canvas.getContext("2d").drawImage(video, 0, 0);
```

你还可以在 `img` 元素中显示画布内容。

在本教程创建的示例中，我添加了一个按钮，该按钮可从画布动态创建图像并将其添加到页面：

```javascript
const img = document.createElement("img");
img.src = canvas.toDataURL("image/png");
screenshotsContainer.prepend(img);
```

## 完整示例和代码

在线效果及源代码：https://coding.zhangbing.site/view.html?url=./list/camera-api/index.html

PC 效果：
![PC](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202006/cameras-with-javascript/6.png)

手机 QQ 中浏览效果：
![手机QQ中浏览效果](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202006/cameras-with-javascript/7.jpg)

---

**本文首发于公众号《前端全栈开发者》ID：by-zhangbing-dev，第一时间阅读最新文章，会优先两天发表新文章。关注后私信回复：大礼包，送某网精品视频课程网盘资料，准能为你节省不少钱！**
