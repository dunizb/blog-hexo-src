---
title: 实战|这个炫酷的播放粒子效果，你也可以学会！使用Web动画API制作
date: 2020-04-04 20:12:55
categories:
  - 技术
tags:
  - 前端
  - React
---

**又一期实战教程来了，本次实战教你创建一个粒子魔术效果，跟着我做，包你也能学会和理解。**

当谈到运动和动画时，可能没有什么比粒子更让我喜欢了，这就是为什么每次我探索新技术时，我总是以尽可能多的创建粒子来演示。

<!-- more -->

在本文中，单击按钮时，我们将使用[Web Animations API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Animations_API)创建烟花效果，从而制作更多的粒子魔术。 

效果如下
![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/Web-Animations-API/1.gif)

本文演示和完整代码已经放在我的博客[小码](https://coding.zhangbing.site/view.html?url=./list/PlayingWithParticles.html)页面

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/Web-Animations-API/xiaoma.png)

让我们开始吧！

****

## 浏览器支持

在我撰写本文时，除了Safari和Internet Explorer（IE是全民公敌、Safari是新时代的IE）之外，所有主流浏览器至少部分支持Web动画API。Safari支持可以在“实验性特性”开发人员菜单中启用。

这个浏览器支持的数据来自[Caniuse](https://caniuse.com/#feat=web-animation)。数字表示浏览器支持该版本及以上的功能。

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/Web-Animations-API/2.png)

## HTML设置

该演示不需要太多的HTML，我们将使用一个 `<button>` 元素，但它可以是另一种类型的标签元素。如果我们真的想的话，我们甚至可以听到页面上的任何点击声，让粒子从任何地方弹出。

```html
<button id="button">Click on me</button>
```

## CSS设置

由于每个粒子都有一些共同的CSS属性，我们可以在页面的全局CSS中设置它们。因为您可以在HTML中创建自定义标签元素，所以我将使用 `<particle>` 标签名称来避免使用语义标签。但事实是，您可以为 `<p>`，`<i>` 或您选择的任何标记设置动画。

```css
particle {
  border-radius: 50%;
  left: 0;
  pointer-events: none;
  position: fixed;
  top: 0;
}
```

这里要注意几件事：

- 粒子不应与页面布局相互作用，因此我们设置了一个 `fixed` 位置，`top` 和 `left` 分别为 `0px`。
- 我们还将删除指针事件，以避免HTML粒子在屏幕上时与用户的任何交互。

因为样式化按钮和页面布局并不是本文的真正目的，所以我将把它放在一边。

## JavaScript设置

这是我们将在JavaScript中执行的六个步骤：
- 监听按钮上的点击事件
- 创建30个 `<particle>` 元素并将其附加到 `<body>`
- 为每个粒子设置随机的宽度，高度和背景
- 使每个粒子淡出时，将其从鼠标位置动画化到随机位置
- 动画完成后，从DOM中删除 `<particle>`

### 步骤1：点击事件

```javascript
// 我们首先检查浏览器是否支持Web Animations API
if (document.body.animate) {
  // 如果支持，我们在按钮上添加一个点击监听器
  document.querySelector('#button').addEventListener('click', pop);
}
```

### 步骤2：粒子

```javascript
// 每次点击都会调用 pop() 函数
function pop(e) { 
  // 循环一次生成30个粒子
  for (let i = 0; i < 30; i++) {
    // 我们将鼠标坐标传递给 createParticle() 函数
    createParticle(e.clientX, e.clientY);
  }
}
function createParticle(x, y) {
  // 创建自定义粒子元素
  const particle = document.createElement('particle');
  // 将元素添加道body中
  document.body.appendChild(particle);
}
```

### 步骤3：粒子宽度，高度和背景

```javascript
function createParticle (x, y) {
  // [...]
  // 计算从5px到25px的随机大小
  const size = Math.floor(Math.random() * 20 + 5);
  // 将大小应用于每个粒子
  particle.style.width = `${size}px`;
  particle.style.height = `${size}px`;
  // 在蓝色/紫色调色板中生成随机颜色
  particle.style.background = `hsl(${Math.random() * 90 + 180}, 70%, 60%)`;
}
```

### 步骤4：为每个粒子添加动画

```javascript
function createParticle (x, y) {
  // [...]
  // 在距离鼠标75像素的范围内生成随机的x和y目标
  const destinationX = x + (Math.random() - 0.5) * 2 * 75;
  const destinationY = y + (Math.random() - 0.5) * 2 * 75;

  // 将动画存储在变量中，因为稍后我们将需要它
  const animation = particle.animate([
    {
      // 设置粒子的原点位置
      // 我们将粒子偏移一半大小，以使其围绕鼠标居中
      transform: `translate(${x - (size / 2)}px, ${y - (size / 2)}px)`,
      opacity: 1
    },
    {
      // 我们将最终坐标定义为第二个关键帧
      transform: `translate(${destinationX}px, ${destinationY}px)`,
      opacity: 0
    }
  ], {
    // 将随机持续时间设置为500到1500ms
    duration: 500 + Math.random() * 1000,
    easing: 'cubic-bezier(0, .9, .57, 1)',
    // 将每个粒子延迟从0ms到200ms的随机值
    delay: Math.random() * 200
  });
}
```

因为我们有一个随机的延迟，所以等待开始动画的粒子在屏幕的左上角可见，为了防止这种情况，我们可以在全局CSS中为每个粒子设置零不透明度。

```css
particle {
  /* 和之前的一样 */
  opacity: 0;
}
```

### 步骤5：动画完成后删除粒子

从DOM中删除粒子元素很重要，因为我们每次点击都会创建30个新元素，所以浏览器的内存很快就会被填满，导致出现问题。我们可以这样做：

```javascript
function createParticle (x, y) {
  // 和前面的相同
  // 动画结束后，从DOM中删除元素
  animation.onfinish = () => {
    particle.remove();
  };
}
```

## 最终结果

把前面所说的所有的代码整合在一起，我们就得到了我们想要的：一个丰富多彩的粒子爆炸效果。 

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/Web-Animations-API/3.gif)

## 发挥创造力

因为所有这些都是使用CSS，所以修改粒子样式非常简单，下面这五个使用各种形状甚至字符的示例！ 

![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/Web-Animations-API/4.gif)

> 该效果请访问：https://codepen.io/Mamboleoo/pen/cc349d73caa7b1d5cee1e6ad1571db55

****
[![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/imglink/react-hook-table.png)](http://mp.weixin.qq.com/s?__biz=MzI0MDIwNTQ1Mg==&mid=2676492788&idx=1&sn=28b116e49895bb27aaaa2fb082da079b&chksm=f362c217c4154b01fe0d613a31be5a9825ec82ee80fdb778cda5d9cea0463d69728b172ef2df&mpshare=1&scene=23&srcid=&sharer_sharetime=1585990665650&sharer_shareid=a1cefbdd3e6712df8aaf4240a0d50b87#rd)

[![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/imglink/%E5%B0%8F%E7%A8%8B%E5%BA%8F1.png)](http://mp.weixin.qq.com/s?__biz=MzI0MDIwNTQ1Mg==&mid=2676492523&idx=1&sn=4ceae0cd799bc4049bafb0d4c32f68e0&chksm=f362c308c4154a1e42e1ff8c5f8a871b11b829a5cf27d1aecac18674f2221ea594b7a7b09824&mpshare=1&scene=23&srcid=0404umpBFRkCLU3jSHhxjonB&sharer_sharetime=1585991611597&sharer_shareid=a1cefbdd3e6712df8aaf4240a0d50b87#rd)

[![](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/imglink/%E5%B0%8F%E7%A8%8B%E5%BA%8F2.png)](http://mp.weixin.qq.com/s?__biz=MzI0MDIwNTQ1Mg==&mid=2676492529&idx=1&sn=d3818874f04cf309d2eee94f6d24ca8c&chksm=f362c312c4154a04c51267a95215b0eeac4e8bec78dc06a8c644f46a017a579af1b67f7cca55&mpshare=1&scene=23&srcid=0404RwAFa3XZ2hG08c8IU9XT&sharer_sharetime=1585992149331&sharer_shareid=a1cefbdd3e6712df8aaf4240a0d50b87#rd)
