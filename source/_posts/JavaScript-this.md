---
title: JavaScript中的this妙用
date: 2014-04-16 17:15:58
categories:
- 前端开发
- JavaScript
tags:
- 面向对象
---

JavaScript关键字this始JS脚本能够根据使用这个关键字的上下文将值传递给函数。

我们先来看如下一个网页，在用户单击链接之后，弹出一个alert框，然后再转到href属性所指的网页
<!-- more -->
HTML：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>JavaScript中的this妙用</title>
    <script type="text/javascript" src="js/script.js"></script>
</head>
<body>
    <p style="text-algin:center;">
        你好，点击这里去<a id="redirect" href="http://duni.sinaapp.com/obook/">旧物商店</a>
    </p>
</body>
</html>
```

JS：

```js
window.onload = initAll;
function initAll(){
    document.getElementById("redirect").onclick = initRedirect;
}
function initRedirect(){
    alert("这是我创建的旧物商城，欢迎访问！");
    window.location = this;
    return false;
}
```
[在线演示][1]

你可能会主要到，代码中并没有引用特定的网页——这是this关键字的作用之一。this替我们完成的工作之一是从HTML链接获得URL（也就是a标签的href属性）。由于采用这种方式，如果以后脚本改为指向其他的页面而不是旧物商店页面，就不必修改JS。实际上，可以让WEB站点上的所有链接都调用这个相同的JS代码，这一行代码都会自动获得相应的href值。
这样写还有一个好处：如果用户的浏览器不理解JavaScript（比如禁用了JS）,那么它只会加载HTML页面，而不显示alert提示，当他们点击链接时，会像一般情况下那样加载页面，不会发生错误，没有任何问题。

我们在来看一个switch/case例子，创建如下页面：

![switch/case例子][2]

HTML：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>JavaScript中的this妙用</title>
    <script type="text/javascript" src="js/script.js"></script>
</head>
<body>
    <h2>闲置二手图书</h2>
    <form action="#">
        <input type="button" id="Java" value="Java" />
        <input type="button" id="JavaScript" value="JavaScript" />
        <input type="button" id="MySQL" value="MySQL" />
        <input type="button" id="Html" value="HTML5" />
    </form>
</body>
</html>
```
JS：

```js
window.onload = initAll;
function initAll(){
    document.getElementById("Java").onclick = viewDetail;
    document.getElementById("JavaScript").onclick = viewDetail;
    document.getElementById("MySQL").onclick = viewDetail;
    document.getElementById("Html").onclick = viewDetail;
}
function viewDetail(){
    console.log("this.id="+this.id);
    switch(this.id){
        case "Java" : 
            alert("《Java程序员基本功》这本书是李刚写的，在我的商店售价30元！");
            break;
        case "JavaScript" : 
            alert("《JavaScript语言精粹》这本书是Yahoo的一位工程师写的，在我的商店售价15元！");
            break;
        case "MySQL" : 
            alert("《MySQL入门很简单》这本书附带关盘，这个年代其实没什么卵用了，它在我的商店售价28元！");
            break;
        case "Html" : 
            alert("《HTML5秘籍》这本书是图灵系统的图书，非常值得拥有，它在我的商店售价25元，卖的非常好！");
            break;
        default : 
            alert("没有这本书");
    }
}
```
[在线演示][3]

直接用this.id作为switch的参数也是可以的。

[更多文章>>][4]


  [1]: http://sandbox.runjs.cn/show/mp2pnrda
  [2]: http://img.imooc.com/56a628240001481209180251.png
  [3]: http://sandbox.runjs.cn/show/wflrmken
  [4]: http://dunizb.com/
