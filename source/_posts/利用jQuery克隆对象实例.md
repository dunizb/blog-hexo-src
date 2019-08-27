---
title: 利用jQuery克隆对象实例
date: 2014-12-15 13:56:24
categories:
- 技术
tags:
- 前端
- jQuery
description: "当点击右边元素的X时，向左边DIV添加一个，同时右边被设为`disabled`。当点击左边被加入的元素时，删除该元素，同时右边被`disabled`的元素恢复。利用jQuery的对象克隆，轻轻松松一行代码实现。"
---

实现如下图所示的效果：

![图片描述][1]

当点击右边元素的X时，向左边DIV添加一个，同时右边被设为`disabled`。当点击左边被加入的元素时，删除该元素，同时右边被`disabled`的元素恢复。利用jQuery的对象克隆，轻轻松松一行代码实现。

HTML：
```html
<body>
	<div class="div" id="leftDiv">
		<ul id="leftUl">
		</ul>
	</div>
	<div class="div" id="rightDiv">
		<ul id="rightUl">
		    <li id="item1"><label for="item01"><input type="checkbox" id="item01"/>元素01</label><span>X</span></li>
			<li id="item2"><label for="item02"><input type="checkbox" id="item02"/>元素02</label><span>X</span></li>
			<li id="item3"><label for="item03"><input type="checkbox" id="item03"/>元素03</label><span>X</span></li>
			<li id="item4"><label for="item04"><input type="checkbox" id="item04"/>元素04</label><span>X</span></li>
			<li id="item5"><label for="item05"><input type="checkbox" id="item05"/>元素05</label><span>X</span></li>
			<li id="item6"><label for="item006"><input type="checkbox" id="item006"/>元素06</label><span>X</span></li>
			<li id="item7"><label for="item007"><input type="checkbox" id="item007"/>元素07</label><span>X</span></li>
			<li id="item8"><label for="item008"><input type="checkbox" id="item008"/>元素08</label><span>X</span></li>
			<li id="item9"><label for="item009"><input type="checkbox" id="item009"/>元素09</label><span>X</span></li>
			<li id="item10"><label for="item010"><input type="checkbox" id="item010"/>元素10</label><span>X</span></li>
			<li id="item11"><label for="item011"><input type="checkbox" id="item011"/>元素11</label><span>X</span></li>
			<li id="item12"><label for="item012"><input type="checkbox" id="item012"/>元素12</label><span>X</span></li>
			<li id="item13"><label for="item013"><input type="checkbox" id="item013"/>元素13</label><span>X</span></li>
			<li id="item14"><label for="item014"><input type="checkbox" id="item014"/>元素14</label><span>X</span></li>
			<li id="item15"><label for="item015"><input type="checkbox" id="item015"/>元素15</label><span>X</span></li>
			<li id="item15"><label for="item015"><input type="checkbox" id="item015"/>元素16</label><span>X</span></li>
			<li id="item15"><label for="item015"><input type="checkbox" id="item015"/>元素17</label><span>X</span></li>
			<li id="item15"><label for="item015"><input type="checkbox" id="item015"/>元素18</label><span>X</span></li>
		</ul>
	</div>
</body>
```

先为他加上一些样式：

CSS：
```css
.div{width: 200px;height: 350px;border: 2px solid #3385ff;margin-right: 10px;display: block;float: left;overflow: auto;}
.div ul{margin: 2px 0 0 1px;padding: 0;list-style-type: none;}
.div ul li{border: 1px solid green;margin: 1px 2px;padding: 2px;border-left: 0;border-right: 0;border-top: 0;}
ul li:hover{background-color: #e6e6e6;}
li span{font-weight: 700;margin-left: 10px;background-color: #abcdef;padding: 0 8px;height: 12px;line-height: 12px;}
li span:hover{cursor: pointer;color: red;}
.disabled{background-color: #e6e6e6;}
```

JS的话，克咯对象的核心代码就下面这一行：
```js
$(obj).parent("#id").clone(true).appendTo("#id")
```

下面我们来给右边的元素批量添加单击事件：
```js
$(function(){
	/** 自动批量添加单击事件 */
	$("#rightUl").find("span").attr("onclick","delRightLi(this)");
        //$("#leftUl").find("span").attr("onclick","delLeftLi(this)");
});
```

实现单击事件的delRightLi（this）方法：
```js
var delRightLi = function(obj) {
	var cloneObj = $(obj).parent("li").clone(true);
	$(cloneObj).find("span").attr("onclick","delLeftLi(this)");
	cloneObj.appendTo("#leftUl");
	$(obj).hide().parent("li").addClass("disabled");
	/** 
	 * ajax后台操作.....
	 * 
	 */
}
```

以上就可以实现点击右边的元素克隆一个元素到左边的框中，并且为克隆的元素绑定了“delLeftLi（this）”单击事件方法。
关键是第2行的cloneObj变量，该变量是临时保存被克隆的对象，用以在这一步加上单击事件，不然克隆过去的话会把元对象的单击事件方法也克隆过去。

delLeftLi(this)方法：
```js
var delLeftLi = function(obj){
	var id = $(obj).parent("li").attr("id");
	$(obj).parent("li").remove();
	$("#rightUl li#"+id).removeClass("disabled").find("span").show();
	/** 
         * ajax后台操作.....
	 * 
	 */
}
```
完毕，以上就是全部代码！


  [1]: http://img.imooc.com/56a62a0f0001028b05570369.png