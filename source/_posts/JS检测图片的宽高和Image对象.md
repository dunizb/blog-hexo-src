---
title: JS检测图片的宽高和Image对象
date: 2016-01-04 22:15:50
categories:
- 前端开发
- JavaScript
tags:
- 总结
description: "在一些页面中我们需要限制图片的规格，也就是宽、高。我们可有用JS来实现，关键是得到图片的绝对地址src,还有JavaScript的Image()对象，给这个对象指定src属性即可获取这个图片的width、height，我们以下面这个图片为例"
---

在一些页面中我们需要限制图片的规格，也就是宽、高。我们可有用JS来实现，关键是得到图片的绝对地址src,还有JavaScript的Image()对象，给这个对象指定src属性即可获取这个图片的width、height，我们以下面这个图片为例（[本博客](http://www.mybry.com)LOGO图标）
![Paste_Image.png](http://ww3.sinaimg.cn/large/006tNc79ly1g5d7khpx2tj30dm05jdgj.jpg)

它的宽度为36，高度为39，代码如下：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>JS检测上传图片的规格</title>
</head>
<body>
    <script type="text/javascript">
    var image = new Image();
    var imgSrc = "http://2.duni.sinaapp.com/logo.png";
    image.src = imgSrc;
    image.onload = function(){
        var width = image.width;
        var height = image.height;
        console.info("width："+width+"，height："+height);
    }
    </script>
</body>
</html>
```
结果：

![Paste_Image.png](http://ww1.sinaimg.cn/large/006tNc79ly1g5d7kijty5j30bl04njrd.jpg)

### HTML DOM Image对象**
Image 对象代表嵌入的图像。 标签每出现一次，一个 Image 对象就会被创建。

### Image 对象的属性**
除了id、name、src、alt、width、height、border、className、title等标准属性外，下面列出Image对象的其他重要属性：

**align**
设置或返回与内联内容的对齐方式。可选值：left|right|top|middle|bottom

**complete**	
返回浏览器是否已完成对图像的加载。如果加载完成，则返回 true，否则返回 fasle。

**hspace**
设置或返回图像左侧和右侧的空白。hspace 属性可设置或返回图像的左边缘和右边缘的空白。hspace 和 vspace 属性可与 align 一同使用，来设置图像与周围文本的距

**isMap**
返回图像是否是服务器端的图像映射。

**longDesc**
设置或返回指向包含图像描述的文档的 URL。

**lowsrc**
设置或返回指向图像的低分辨率版本的 URL。

**useMap**
设置或返回客户端图像映射的 usemap 属性的值。

**vspace**
设置或返回图像的顶部和底部的空白。

### Image 对象的事件句柄

**onerror**
在装载图像的过程中发生错误时调用的事件句柄。

**onabort**
当用户放弃图像的装载时调用的事件句柄。

**onload**
当图像装载完毕时调用的事件句柄。

### onLoad() 事件处理器

像很多 JavaScript 的其它对象一样，Image() 对象也有一些事件处理器。其中最有用的一个肯定是 onLoad() 处理器，它在图像完全载入之后调用。这个事件处理器可以与一个自定义函数联系起来，以在图像完全载入之后执行一些特定的任务。下面的例子说明了这一点，在这个例子中，首先在图像载入时显示一个“please wait”屏幕，然后在载入完成时将浏览器转到一个新的 URL。
```html
<html> 
<head> 
<script language="JavaScript"> 
// 创建一个Image对象
var objImage =  new  Image(); 
// 图像加载完成就会调用 objImage.onLoad=imagesLoaded(); 
// 预加载图像文件
objImage.src="http://img0.sfht.com/cmsres/20151030/4704a64f-8d6d-4a82-ab11-62d914531a44.jpeg";
// 图像加载完的函数调用
objImage.onLoad = imagesLoaded();
function imagesLoaded() { 
      document.location.href="http://www.mybry.com/"; 
} 
</script> 
</head> 
<body> 
        请等待，正在加载图片.... 
</body> 
</html> 
```