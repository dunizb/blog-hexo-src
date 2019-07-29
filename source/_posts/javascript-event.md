---
title: JavaScript事件探秘
date: 2014-09-06 11:25:29
categories:
- 前端开发
tags:
- JavaScript
description: "事件流描述的是从页面中接收事件的顺序，IE的事件流是事件冒泡流，而Netscape的事件流是事件捕获流，事件冒泡，即事件最开始由最具体的元素(文档中嵌套层次最深的那个节点)接收，然后逐级向上传播至最不具体的节点(文档)。"
---

## 一、事件流

事件流描述的是从页面中接收事件的顺序。IE的事件流是事件冒泡流，而Netscape的事件流是事件捕获流

### 1、事件冒泡

事件冒泡，即事件最开始由最具体的元素(文档中嵌套层次最深的那个节点)接收，然后逐级向上传播至最不具体的节点(文档)。

如下图所示，如果你点击了按钮，那么也认为你点击了外面的div，最终会一直传递到document上，从小到大的传播，就好比水里鱼的冒泡，从小泡泡大道泡泡的过程。

![Paste_Image.png](//ww4.sinaimg.cn/large/006tNc79ly1g5d84u4340j30km0bldfx.jpg)

### 2、事件捕获

事件捕获跟事件冒泡恰好相反。它的思想是不太具体的节点应该更早接收到事件，而最具体的节点最后接收到事件。

![Paste_Image.png](//ww2.sinaimg.cn/large/006tNc79ly1g5d84v3p82j30pp09rwew.jpg)

### 3、事件的三个阶段

事件的三个阶段分别是：捕获阶段、目标阶段、冒泡阶段

![事件的三个阶段](//ww1.sinaimg.cn/large/006tNc79ly1g5d84w1g8aj30af06uaa7.jpg)

## 二、事件处理程序

### 1、HTML事件处理程序

所谓的HTML事件是指把JS直接写在HTML元素中，比如下面的代码：
[程序1]   
```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>事件流</title>
</head>
<body>
	<div id="box">
		<input type="button" value="按钮1" id="btn1" onclick="alert('hello')">
	</div>
</body>
</html>
```

或者
[程序2]   
```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>事件流</title>
</head>
<body>
	<div id="box">
		<input type="button" value="按钮1" id="btn1" onclick="showMsg()">
	</div>
	<script type="text/javascript">
	function showMsg(){
		alert("hello");
	}
	</script>
</body>
</html>
```

那么HTML事件处理程序有一种很明显的缺点：HTML与JS代码紧密的耦合在一起，这不是这一种好的程序设计。

### 2、DOM0级事件处理程序

DOM0级别事件处理程序是一种比较传统的，是指把一个行数赋值给一个事件处理程序的属性，也是用的比较多的方式。
他的优点是：简单，而且具有夸浏览器的优势。

[程序3]   
```html
<input type="button" value="按钮2" id="btn2">
var btn2 = document.getElementById("btn2");
btn2.onclick = function(){
     alert("这是通过DOM0级添加的事件！");
}
```

如果要需要这个事件，可以这样写：
[程序4]  
```js
btn2.onclick = null;
```

以上就是DOM0级事件处理方法。

### 3、DOM2级事件处理程序

DOM2级事件定义了两个方法：用于处理指定和删除事件处理程序的操作：addEventListener()和removeEventListener()。它们都接收三个参数：要处理的事件名、作为事件处理程序的函数和一个布尔值。

他的有点与DOM0级一样，还可以添加多个事件处理程序。
[程序5]
```html
<div id="box">
	<input type="button" value="按钮1" id="btn1" onclick="showMsg()">
	<input type="button" value="按钮2" id="btn2">
	<input type="button" value="按钮3" id="btn3">
</div>
<script type="text/javascript">
function showMsg(){
	alert("hello");
}
var btn2 = document.getElementById("btn2");
var btn3 = document.getElementById("btn3");
btn2.onclick = function(){
	alert("这是通过DOM0级添加的事件！");
}
btn2.onclick = null;
//DOM2级事件
btn3.addEventListener("click",function(){
	//获取按钮的文本
	alert(this.value);
},false);
//删除事件
btn3.removeEventListener("click",showMsg,false);
</script>
````

但是，在IE8-的浏览器中无法运行，不支持addEventListener，IE有IE独有的DOM2级事件处理程序。（IE11亲测可以，IE9、10没测试过）。

### 4、IE事件处理程序

在IE中也提供了类似的两个方法
- attachEvent()添加事件
- detachEvent()删除事件

这两个方法接收相同的两个参数：事件处理程序名称与事件处理函数

[程序6]  
```js
btn3.attachEvent("onclick",showMsg);
```

这这里又要给事件加上“on”，这与DOM2级事件处理程序想法。那么这就夸浏览器了。
那么怎么解决跨浏览器的问题？答案是，能力判断，即你支持attachEvent()的能力就给你这个，你有别的能力你就你其他的能力。

### 5、跨浏览器的事件处理程序

我们封装到一个对象
[程序7]    
```js
var eventUtil = {
	//添加句柄
	addHandle:function(element,type,handle){
		if (element.addEventListener) {
			element.addEventListener(type,handle,false);
		}else if(element.attachEvent){
			element.attachEvent("on"+type,handle);
		}else{
			element["on"+type] = handle;
		};
	},
    //删除句柄
	removeHandle:function(element,type,handle){
		if (element.removeEventListener) {
			element.removeEventListener(type,handle,false);
		}else if(element.dettachEvent){
			element.dettachEvent("on"+type,handle);
		}else{
			element["on"+type] = null;
		};
	}
}
//调用
eventUtil.addHandle(btn3,"click",showMsg);
```

这样一来，IE和Chrome都能很好的支持了。

## 三、事件对象

事件对象event

![Paste_Image.png](//ww4.sinaimg.cn/large/006tNc79ly1g5d84wxh6fj30gk07nq4j.jpg)

### 1、DOM中的事件对象

**type:获取事件类型**
[程序8]  
```js
function showMsg(event){
	alert(event.type);	//click
}
```

**target：事件目标**
[程序9] 
```js
function showMsg(event){
	alert(event.target.nodeName);	//input
}
```

**stopPropagation() 阻止事件冒泡**
我们已经知道了事件冒泡的概念，那么，当我点击按钮的时候我就是点击按钮，不让它再冒泡到div上了，那么我们可以在程序中加上
event.stopPropagation() 即可阻止事件的冒泡。

**preventDefault() 阻止事件的默认行为**
事件的默认行为，比如,`<a href="">跳转</a>`，他的默认行为就是跳转到某个链接，那么现在我们想要点击它不让它跳转，去执行我们给他的事件行为。那么可以这样写：`event.preventDefault()`

### 2、IE中的事件对象
**type:获取事件类型**  
**srcElement：事件目标**  
**cancelBubble=true阻止事件冒泡**  
**returnValue=false阻止事件的默认行为**  

[程序10]
```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>事件</title>
	<script src="js/event.js"></script>
</head>
<body>
	<a href="index.html" id="go">跳转</a>
	<script type="text/javascript">
	var go = document.getElementById("go");
	go.onclick = function(event){
		if(event && event.preventDefault){
			event.preventDefault();
		}else{
			window.event.returnValue = false;
		}
		alert("我阻止了你");
	}
	</script>
</body>
</html>
```

### 3、夸浏览器的事件对象
虽然 DOM 和 IE 中的 event 对象不同，但基于它们之间的相似性依旧可以拿出跨浏览器的方案来。IE 中 event 对象的全部信息和方法 DOM 对象中都有，只不过实现方式不一样。不过，这种对应关系让实现两种事件模型之间的映射非常容易。可以对前面介绍的 EventUtil 对象加以增强，添加如下方法以求同存异。

```js
getEvent: function(event){
    return event ? event : window.event;
},
getTarget: function(event){
    return event.target || event.srcElement;
},
preventDefault: function(event){
   if (event.preventDefault){
       event.preventDefault();
   } else {
       event.returnValue = false;
   }
},
stopPropagation: function(event){
    if (event.stopPropagation){
        event.stopPropagation();
    } else {
        event.cancelBubble = true;
    }
}
```

第一个是 getEvent()，它返回对 event对象的引用，在兼容 DOM 的浏览器中， event 变量只是简单地传入和返回。而在 IE 中， event 参数是未定义的（undefined），因此就会返回 window.event。

第二个方法是 getTarget()，它返回事件的目标。在这个方法内部，会检测 event 对象的 target属性，如果存在则返回该属性的值；否则，返回 srcElement 属性的值。

第三个方法是 preventDefault()，用于取消事件的默认行为。在传入 event 对象后，这个方法会检查是否存在preventDefault()方法，如果存在则调用该方法。如果 preventDefault()方法不存在，则将 returnValue 设置为 false。

第四个方法是 stopPropagation()，其实现方式类似。首先尝试使用 DOM 方法阻止事件流，否则就使用 cancelBubble 属性。
