---
title: 【小技巧】移动端网页调试神器Eruda的使用技巧
date: 2017-10-16 21:25:52
categories:
  - Web技术
tags:
  - 移动Web
  - 小技巧
---

做移动端 Web 开发的一大痛点就是，在真机运行下无法查看 console.log 日志和其他信息如网络请求、显示本地存储等信息。如果网页是运行在手机浏览器中还算好，可以把网址在电脑上打开查看 console 信息，但是如果是做 APP 的内嵌 H5 页面，那就只能靠开发阶段在浏览器模拟环境中尽量没有 Bug，但是，一旦 H5 上线后报错那就比较麻烦了，而且还依赖 APP 环境才能跑的网页，更加难以查找问题。如果让移动端也拥有类似 Chrome DevTools 工具那岂不是很愉快么？

<!-- more -->

vConsole 便是这样一款很棒的移动端 DevTools 工具，由大厂企鹅出品。但本文把他定位为男二号，今天的主角男一号是：**Eruda**！vConsole 的同类。如果你不知道怎么在移动端调试网页，那么本篇文章对你很有帮助，如果你是 vConsole 的用户，那么你也可尝试一下 Erdua，如果你是移动端网页开发骨灰级玩家，那么可以选择低调的忽略本文。

**Eruda 是谁？**
大家好，给大家介绍一下，这是我的.....。Eruda 是一个专为手机网页前端设计的调试面板，类似 DevTools 的迷你版，其主要功能包括：捕获 console 日志、检查元素状态、显示性能指标、捕获 XHR 请求、显示本地存储和 Cookie 信息、浏览器特性检测等等。

GitHub 地址为：[https://github.com/liriliri/eruda](https://github.com/liriliri/eruda)，颜值和技能都很棒的 Erdua：
![Erdua](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201710/erdua/1.jpeg)
详细介绍可以戳[这里](https://github.com/liriliri/eruda/blob/master/doc/README_CN.md)产看，我就不赘述了

**使用技巧**

这才是本文重点。

Erdua 的基本使用方法推荐使用 CDN 和可配置参数的形式，在页面引入如下代码：

```javascript
(function() {
  var src = "//cdn.bootcss.com/eruda/1.2.4/eruda.min.js";
  if (
    !/eruda=true/.test(window.location) &&
    localStorage.getItem("active-eruda") !== "true"
  )
    return;
  document.write(
    "<scr" + 'ipt src="' + src + '"></scr' + "ipt>"
  );
  document.write(
    "<scr" + "ipt>eruda.init();</scr" + "ipt>"
  );
})();
```

`eruda.init();`里面可以传入要初始化哪些面板，默认加载所有。

这样使用方式没有错，但是如果 Eruda 要跟着发布到线上的话，那我们要删除这段代码？因为这样会不管你需不需要调试 Eruda 都会立即加载，在页面出现 Eruda 的图标。我的目标是，Eruda 可以保留在页面里，无论什么环境，只要我们想呼唤它出现的时候它才出现，不需要它的时候它只是一段不会生效的普通代码。

我想到的办法是：首先把上述引入代码的`src`放到`if`里，同时把`localStorage`改为`sessionStorage`，`active-eruda`参数也可以改个更短的名字，改后的代码如下：

```js
(function() {
  if (
    !/eruda=true/.test(window.location) ||
    sessionStorage.getItem("eruda") !== "true"
  )
    return;
  var src = "//cdn.bootcss.com/eruda/1.2.4/eruda.min.js";
  document.write(
    "<scr" + 'ipt src="' + src + '"></scr' + "ipt>"
  );
  document.write(
    "<scr" + "ipt>eruda.init();</scr" + "ipt>"
  );
})();
```

这段代码的意思是如果 URL 中有参数`eruda=true`或者 sessionStorage 中`eruda`的值为 true 才加载 Eruda。这样的好处是，我们需要调试的时候可以在网页 URL 后面加个参数即可，不需要调试的它不会加载。

然而，这在开发阶段可以这样做比较好，但是在线上环境可能要加 URL 参数比较麻烦。于是我想到了在页面中点击某个元素 10 次再加载 Eruda，点击 10 次或者更多次，然后在 sessionStorage 中写入`eruda=true`，然后刷新当前页。相反，再点击 10 次关闭 Eruda。用这样比较隐藏的方式开启或关闭 Eruda，这样线上环境也可以自由开启或关闭 Eruda 了。

例子：假如有这样的一个页面，里有一个标题文字

```html
<h2>——规则详情——</h2>
<div>
  .....
</div>
```

那么我们可以在`h2`标签上绑定一个`click`事件，加入方法名叫`showEruda`

```html
<h2 onclick="showEruda">——规则详情——</h2>
<div>
  .....
</div>

<script>
  var count = 0;
  function showEruda() {
    if (count >= 10) {
      var erdua = sessionStorage.getItem("erdua");
      if (!erdua || erdua === "false") {
        sessionStorage.setItem("eruda", "true");
      } else {
        sessionStorage.setItem("eruda", "false");
      }
      location.reload();
    }
    count++;
  }
</script>
```

这样点击规则详情 10 次就可以唤起 Eruda 了，再点击 10 次就关闭 Eruda 了，反正我觉得这样挺好的。

不知道大家都是怎么用的呢？

<div style="text-align:center;">【 全 文 完 】</div>

---

关注公众号，第一时间接收最新文章。如果对你有一点点帮助，可以点喜欢点赞点收藏，还可以小额打赏作者，以鼓励作者写出更多更好的文章。

![关注公众号](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/关注名片-大礼包_横版二维码_2020-01-01-0.jpg)
