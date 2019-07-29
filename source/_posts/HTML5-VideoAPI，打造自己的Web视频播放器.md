---
title: HTML5 VideoAPI，打造自己的Web视频播放器
date: 2016-08-04 12:53:00
categories:
- 前端开发
tags:
- JavaScript
- 实战
---

> 本文将使用HTML5提供的VideoAPI做一个自定义的视频播放器，需要用到HTML5提供的video标签、以及HTML5提供的对JavascriptAPI的扩展。
<!-- more -->
![video.jpg](//ww3.sinaimg.cn/large/006tNc79ly1g5d89orce0j30ll0dj0sy.jpg)

一、基础知识
---------------------------
### 1.用法
```
<video src="./video/mv.mp4"></video>
```
注意：audio和video元素必须同时包含开始和结束标签，不能使用`<audio />`这样的空元素语法形式。

### 2.重要HTML属性
**controls**：ontrol：如果出现该属性，则向用户显示控件，比如播放按钮。每个浏览器中的播放控件都不太一样，但用途都一样，都可以控制开始和结束，跳到新位置和调节音量
**autoplay**：autoplay：如果出现该属性，则视频在就绪后马上播放。如果不设置autoplay属性，必须是用户单击播放按钮才会播放音频文件。
**loop**：loop：(循环播放)告诉浏览器在音频到达末尾时，再从头开始重新播放
**preload**：auto、mete、none：告诉浏览器如何下载音频  
   + auto：让浏览器下载整个文件，以便用户单击播放按钮时就能播放。当然，下载过程是后台进行的，网页访客不必等待下载完成，而且仍然可以随意查看网页。
   + meta：告诉浏览器先获取音频文件开头的数据块，从而足以确定一些基本信息（比如音频的总时长） 
   + none：**告诉浏览器不必预先下载。恰当地利用这些值，可以节省带宽。

如果没有设置preload属性，浏览器就自己决定是否预先下载了。对这一点，不同浏览器的处理方式也不一样。多数浏览器将auto作为默认值，但Firefox的默认值是metadata。不过，也请大家注意，这个preload属性也不是必须严格执行的规则，而只是你对浏览器的建议。根据具体情况，浏览器可以忽略你的设置。（有些旧版本浏览器根据不会在意preload属性。）

### 3.常用事件

事件名称 ： 解释
**oncanplay**：当文件就绪可以开始播放时运行的脚本（缓冲已足够开始时）。
**ontimeupdate**： 当播放位置改变时（比如当用户快进到媒介中一个不同的位置时）运行的脚本。    
**onended**：当媒介已到达结尾时运行的脚本（可发送类似“感谢观看”之类的消息）。    

### 4.常用方法
方法名称 ：解释
**play()**：开始播放音频/视频    
**pause()**：暂停当前播放的音频/视频    

### 5.常用API属性
属性名称 ： 解释        
**duration**：返回当前音频/视频的长度（以秒计）    
**paused**：设置或返回音频/视频是否暂停    
**currentTime**：设置或返回音频/视频中的当前播放位置（以秒计）
**ended**：返回音频/视频的播放是否已结束    

更多属性、事件、方法请查看[w3school](//www.w3school.com.cn/tags/html_ref_audio_video_dom.asp)

二、打造自己的播放器
---------------------------
我们使用JavaScript控制播放控件的行为（自定义播放控件），实现如下功能：
+ 利用HTML+CSS制作一个自己的播放控件条，然后定位到视频最下方
+ 视频加载loading效果
+ 播放、暂停
+ 总时长和当前播放时长显示
+ 播放进度条
+ 全屏显示

### 1.播放控件
```html
<body>
<figure>
    <figcaption>视频播放器</figcaption>
    <div class="player">
        <video src="./video/mv.mp4"></video>
        <div class="controls">
            <!-- 播放/暂停 -->
            <a href="javascript:;" class="switch fa fa-play"></a>
            <!-- 全屏 -->
            <a href="javascript:;" class="expand fa fa-expand"></a>
            <!-- 进度条 -->
            <div class="progress">
                <div class="loaded"></div>
                <div class="line"></div>
                <div class="bar"></div>
            </div>
            <!-- 时间 -->
            <div class="timer">
                <span class="current">00:00:00</span> /
                <span class="total">00:00:00</span>
            </div>
            <!-- 声音 -->
        </div>
    </div>
</figure>
```

上面是全部HTML代码，`.controls`类就是播放控件HTML，引用CSS代码：
```html
<link rel="stylesheet" href="./css/font-awesome.css">
<link rel="stylesheet" href="./css/player.css">
```

为了显示播放按钮等图标我使用了字体图标

### 2.视频加载loading效果
一开始先隐藏视频，用一个背景图片替代，等到视频加载完成可以播放时在显示视频
CSS：
```css
.player {
    width: 720px;
    height: 360px;
    margin: 0 auto;
    background: #000 url(../images/loading.gif) center/300px no-repeat;
    position: relative;
}
 
video {
    display: none;
    height: 100%;
    margin: 0 auto;
}
```

### 3.播放功能
让我们开始写javascript代码吧，首先我们先获取要用到的DOM元素：
```js
var video = document.querySelector("video");
var isPlay = document.querySelector(".switch");
var expand = document.querySelector(".expand");
var progress = document.querySelector(".progress");
var loaded = document.querySelector(".progress > .loaded");
var currPlayTime = document.querySelector(".timer > .current");
var totalTime = document.querySelector(".timer > .total");
```

当视频可以播放时，显示视频
```js
//当视频可播放的时候
video.oncanplay = function(){
      //显示视频
      this.style.display = "block";
      //显示视频总时长
      totalTime.innerHTML = getFormatTime(this.duration);
};
```

### 4.播放、暂停
点击播放按钮时显示暂停图标，在播放和暂停状态之间切换图标
```js
//播放按钮控制
isPlay.onclick = function(){
        if(video.paused) {
            video.play();
        } else {
            video.pause();
        }
        this.classList.toggle("fa-pause");
};
```

### 5.总时长和当前播放时长显示
前面代码中其实已经设置了相关代码，此时我们只需要把获取到的毫秒数转换成我们需要的时间格式即可，提供`getFormatTime()`函数：
```js
function getFormatTime(time) {
        var time = time  0;
 
        var h = parseInt(time/3600),
            m = parseInt(time%3600/60),
            s = parseInt(time%60);
        h = h < 10 ? "0"+h : h;
        m = m < 10 ? "0"+m : m;
        s = s < 10 ? "0"+s : s;
 
        return h+":"+m+":"+s;
    }
```

### 6.播放进度条
```js
//播放进度
video.ontimeupdate = function(){
    var currTime = this.currentTime,    //当前播放时间
    duration = this.duration;       // 视频总时长
    //百分比
    var pre = currTime / duration * 100 + "%";
    //显示进度条
    loaded.style.width = pre;
 
     //显示当前播放进度时间
    currPlayTime.innerHTML = getFormatTime(currTime);
};
```

这样就可以实时显示进度条了，此时，我们还需要点击进度条进行跳跃播放，即我们点击任意时间点视频跳转到当前时间点播放：
```js
//跳跃播放
progress.onclick = function(e){
    var event = e  window.event;
    video.currentTime = (event.offsetX / this.offsetWidth) * video.duration;
};
```

### 7.全屏显示
这个功能可以使用HTML5提供的全局API：`webkitRequestFullScreen`实现，跟`video`无关：
```js
//全屏
expand.onclick = function(){
     video.webkitRequestFullScreen();
};
```

[在线示例](http://demo.dunizb.com/list/HTML5%E8%A7%86%E9%A2%91API%E7%AE%80%E5%8D%95%E4%BD%BF%E7%94%A8/)
[github](https://github.com/dunizb/CodeTest/tree/master/HTML%26CSS/html5/%E8%A7%86%E9%A2%91%E6%92%AD%E6%94%BE%E5%99%A8)

经测试在firefox、IE下全屏功能不可用，这样正常了，全屏API是针对webkit内核的。