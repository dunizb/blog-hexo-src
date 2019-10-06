---
title: JavaScript面向对象程序设计基础
date: 2014-09-02 22:53:34
categories:
- 技术
tags:
- 前端
- JavaScript
description: "JavaScript的简单数据类型包括数字、字符串、布尔值、null值和undefined值。其他所有的值都是对象。数字、字符串和布尔值“貌似”对象，因为它们拥有方法，但它们是不可变的。JavaScript中的对象是可变的键控集合（Keyed collections）。在JavaScript中，数组时对象，函数是对象，正在表达式是对象，当然，对象自然也是对象。"
---

## 一、对象

JavaScript的简单数据类型包括数字、字符串、布尔值、null值和undefined值。其他所有的值都是对象。数字、字符串和布尔值“貌似”对象，因为它们拥有方法，但它们是不可变的。JavaScript中的对象是可变的键控集合（Keyed collections）。在JavaScript中，数组时对象，函数是对象，正在表达式是对象，当然，对象自然也是对象。
<!-- more -->
对象是属性的集合，每一个属性具有一个名称和一个值。数学的名字可以是包括空字符串在内的任意字符串。属性值可以是除undefined值之外的任何值。

JavaScript里的对象是无类型的（class-free）。它对新属性的名字和值是没有限制的。对象适合用于汇集和管理数据。对象可以保护其他对象，所以它们可以很容易的表示成树状或图形结构。

### 1、创建（Create）

可以采用两种方法来实例化对象。

**第一种方法是使用new关键字**
```js
var myObject = new Object();
```

new 关键字调用构造函数，或者更通用的说法是构造器。构造器将初始化新创建的对象。下面的代码演示了一个名为Zombie（僵尸）对象的构造器，构造器初始化该对象的name属性，然后使用new关键字实例化一个Zombie对象。this关键字用于引用当前对象，不能对它进行赋值，但可以将this关键字的值赋给另外一个变量。
```js
//构造函数
function Zombie( name ) {
    this.name = name;
}
var smallZombie = new Zombie( "Booger");
```

**使用对象字面量创建对象**
另外一种实例化新对象的方法更加方便：使用对象字面量，这种方式创建的对象更像其他语言中的散列（hash）或关联数组。
```js
var myObject = { };
var webSite= {
    "url": "www.mybry.com",
    "siteName": "读你"
};
```

在属性列表的最后一项值的末尾，请不要使用结尾逗号。不同的浏览器对此的解析并不一致。

### 2、检索（Retrieval）
既可以使用方括号来访问对象的属性，也可以使用点操作符来访问。下面的代码示例了者两种方式：
```js
webSite['url'];
"www.mybry.com"
webSite.siteName;

"读你"
```

采用方括号方式访问属性时，可以使用JavaScript的关键字作为属性名，但不推荐这样做，使用点操作符方式访问属性时则不能使用。使用点操作符方式，代码更加简短。JSLint鼓励开发人员使用点操作符方式。因为属性也可以是对象，可以在任意层次上嵌套对象。 

下面假设有一个父亲father对象，他有自己的名字name和年龄age，他有两个儿子childrenOne和childrenTwo，两个孩子当然也是对象，也有名字name个年龄age，然后我们访问孩子childrenOne的名字：
```js
var father = {
  'name': 'zhangsan',
  'age': 50,
  'childrenOne': {
    'name': 'zhangsi',
    'age': 22
  },
  'childrenTwo': {
    'name': 'zhangwu',
    'age': 12
  }
};

console.log(father.childrenOne.name);
//输出：zhangsi
```

JavaScript是一种动态的语言，因此更新一个属性只需要重新对属性赋值即可。要从对象中移除一个属性，只需要使用delete操作符。删除一个不存在的属性并不会造成任何危险。要遍历对象的所有属性，可以使用for...in循环，比如下面的代码块：
```js
var obj1 = {
  'properties1': 1,
  'properties2': 2
};

var i;
for( i in obj1 ){
  console.log( i );
}

properties1
properties2
```

### 3、原型（Prototype）

JavaScript使用原型继承（prototype inheritance），对象直接从其他对象继承，从而创建新的对象。简而言之，对象继承了另外一个对象的属性。JavaScript中没有类，这是与Java和C#等语言相比一个较大的差别。原型就是其他对象的模型（model）。

每个对象都连接到一个原型对象，并且它可以从中继承属性。所有通过对象字面量创建的对象都连接到Object.prototypr，它是JavaScript中的标配对象。

当你创建一个新对象时，你可以选择某个对象作为它的原型。JavaScript提供的实现机制杂乱而复杂，但其实可以被明显地简化。我们将给Object对象增加一个create方法。这个方法创建一个使用原型对象作为其原型的新对象。
```js
if (typeof Object.beget !== 'function') {
  Object.create = function (o) {
    var F = function () { };
    F.prototype = o;
    return new F();
  };
}
var another = Object.create( webSite );
console.log(another);
```    
在firebug中输出如下结果，正是一个对象。

原型连接在更新时是不起作用的。当我们对某个对象做出改变时，不会触及该对象的原型，原型连接只有在检索值的时候才会被用到。如果我们尝试去获取对象的某个属性值，但该对象没有此属性名，那么JavaScript会试着从原型对象中获取属性值。如果那个原型对象也没有该属性，那么再从他的原型中寻找，以此类推，直到该过程最后到达终点Object.prototype。如果想要的属性完全不存在与原型链中，那么结果就是undefined值。这个过程称为委托。

原型关系是一种动态关系。如果我们添加一个新的属性到原型中，该属性会立即对所有基于该原型创建的对象可见。（更多关于原型链的内容后续文章将会介绍）

### 4、引用（Reference）
引用是一个指向对象实例位置的指针。Object是引用类型，由于所有对象都是通过引用传递的，，它们永远不会被复制：
```js
var x = myObject;
x.nickname = '这世间唯有梦想和好姑娘不可辜负~~~';
var nick = myObject.nickname;
//因为x和stooge是指向同一个对象的引用，所以nick为‘zhangsan’

console.log(nick); //这世间唯有梦想和好姑娘不可辜负~~~

//因为a、b和c每个都引用一个不同的空对象
var a = { }, b = { }, c = { };
//a、b和c都引用同一个空对象
a = b = c = { };
```
修改绑定于一个原型的属性，将修改基于该原型的所有其他对对象的原型。

对象是自知的，或者说对象知道自己的属性。要检查某个属性是否存在，可以使用hasOwnProperty()方法，该方法将返回一个布尔值。

### 5、减少全局变量污染
很多程序员认为，应该避免使用全局变量。有一些办法可以避免扰乱全局名称空间，一种办法是使用单个全局变量作为顶层对象，包含程序或框架中的所有方法和属性。按照惯例，名称空间的字母全部大写，但值得注意的是常量通常也大写格式。
```js
ZOMBIE_GENERATOR = {};
ZOMBIE_GENERATOR.Zombies = {
    smallZombie : 'Booger',

    largeZombie : 'Bruce'

}
```
采用上面的代码定义名称空间之后，Zombies就不会与全局名称空间中的任何其他变量冲突。另外一种减少冲突的方式是使用闭包。

### 6、反射（Reflection）
检查对象并确定对象有什么属性时很容易的事情，只要试着去检索该属性并验证取得值。typeof操作符对确定属性的类型很有帮助：
```js
typeof father.name;                      // 'string'
typeof father.age;                         // 'number'
typeof father.childrenOne;         // 'object'
typeof father.xxx;                         // 'undefined'

```

请注意原型链中任何属性都会产生值：
```js
typeof father.toString;            // 'function'
typeof father.constructor;      // 'function'
```

有两种方法去处理掉这些不需要的属性。第一个是让你的程序做检查并丢弃掉值为函数的属性。一般来说，当你想让对象在运行时动态获取自身信息时，你关注更多的时数据，而你应该意识到一些值可能是函数。

另一个方法时使用hasOwnProperty方法，如果对象拥有独有的属性，它会返回true。hasOwnProperty方法不会检查原型链。
```js
father.hasOwnProperty('name');                // true
father.hasOwnProperty('constructor');     // false
```

### 7、枚举（Enumeraton）

`for...in`语句可以用来遍历一个对象中的所有属性名。该枚举过程将会列出所有的属性——包括函数和你可能不关心的原型中的属性——所以有必要过滤那些你不想要的值。最为常用的过滤时hasOwnPropery方法，一级使用typeof来排除函数。

属性名出现的顺序是不确定的，因此要对任何可能出现的顺序有所准备。如果你想要确保属性以特定的顺序出现，最好的办法就是完全避免使用for in语句，而是创建一个数组，在其中以正确的顺序包含属性名：
```js
var properties = [
  'first-name',
  'middle-name',
  'last-name',
  'profession'
];
for(var i = 0; i < properties.length; i++){
  console.info(properties[i]);
}
```

通过使用for而不是for in，可以得到我们想要的属性，而不必担心可能发掘出的原型链中的属性，并且我们按正确的顺序取得了它们的值。

## 二、函数

函数是一个代码块，它封装了一系列的JavaScript语句。函数可以接受参数，也可以返回值。如果一个函数没有返回一个特定的值，则它返回一个undefined值。下面的代码示例定义了一个没有返回值的函数。该函数依然执行了函数体中的操作，将变了x的值乘以2，但由于没有使用return语句返回值，因此在控制台中输出函数的返回值时，返回值为undefined。
```js
var x = 2;
function calc( ){
  x = x * 2;
}
console.log( calc() );// undefined
```

这正是函数有趣的地方，与Java不同，JavaScript中的函数时第一类对象。这意味着可以像处理其他JavaScript对象一样处理JavaScript函数。可以将函数赋予给一个变量，或者保存在另外一个数据结构中（比如一个数组或对象）；可以将函数作为参数传递给其他函数；可以将函数作为另一个函数的返回值；函数还可以采用匿名函数的形式：即根本没有绑定于函数名标识符的函数。下面代码定义了一个函数表达式，并将其赋值给一个变量。
```js
var calc = function(x){
  return x * 2;
}

calc(5);
// 10
```

给函数名使用圆括号，将执行该函数并返回函数的返回值，而不是返回对函数的引用。
```js
var calc = function(x){
  return x * 2;
}
var calcValue = calc( 5 );
console.log( calcValue );

// 10
```

第一类函数的另外两个特性时非常重要的。第一个特性就是将函数作为参数传递给其他函数。第二个特性就是匿名函数。下面代码演示了JavaScript中函数的重要特性。reporter函数接收一个函数作为参数，并输出执行该参数函数的返回值。另外两个例子则演示了匿名函数。第一个函数是一个根本不包含任何语句的匿名函数，但根据前面的介绍，该函数将返回一个undefined。第二个函数时一个返回字符串的匿名函数。
```js
function reporter( fn ) {
  console.log( "这个返回值是你传递过来的函数：" + fn() );
}
reporter( function(){}); 
//这个返回值是你传递过来的函数：undefined
reporter( function() { return "这是一个简单的字符串" });
//这个返回值是你传递过来的函数：这是一个简单的字符串
function calc(){
  return 2 * 4;
}
reporter( calc );
// 这个返回值是你传递过来的函数：8
```

这是你会看到控制台还输出了一个undefined值，这是reporter函数本身的返回值，也就是说执行任何函数本身，它都会返回一个undefined值。

关于匿名函数，一个特别重要的变体就是立即调用的函数表达式，或称为自执行匿名函数。无论称为“立即调用的函数表达式”还是“自执行匿名函数”，这一模式的本质就是将一个函数表达式包装在一对圆括号中，然后立即调用该函数。这一技巧非常简单，将函数表达式包装在一对圆括号中，将迫使JavaScript引擎将function(){}块识别为一个函数表达式，而不是一个函数语句的开始。下面的代码示例描述了这一模式，在这个简单的例子中，函数表达式接受两个值并简单地将二者相加。
```js
(function( x,y ){
  console.log( x+y );
})( 5,6);
// 11
```

由于这样的函数表达式将被立即调用，因此该模式用于确保代码块的执行上下文按照预期的效果执行，这是这种模式最重要的用于之一。通过将参数传入函数，在执行时就可以捕获函数所创建闭包中的变量。闭包就是这样的一个函数：它处在一个函数体中，并引用了函数体当前执行上下文中的变量。闭包是一个极为强大的功能，下面的代码描述了闭包的基本应用。在下面的例子中还引入了JavaScript函数另外一个有趣的特性，即在函数中可以将另外一个函数作为返回值返回。

在下面的例子中，将自执行匿名函数赋给一个变量message。message返回另外一个函数，该函数只是简单的输出变量x的值。有趣的是，当我们把变量x的初始值作为参数传入函数时，可以在函数执行时所创建的闭包中捕获变量x的值。无论在外部作用域中的x的值发生了什么变化，闭包将记住函数执行时变量x的值。
```js
var x = 42;
console.log(x);    // 42
var message = (function ( x ){
  return function(){
    console.log( "x is " + x);
  }
})( x );
message(); //x is 42
x = 12;
console.log( x ); // 12
message(); //x is 42

```
即使只介绍了这个简单的例子，也应该看到JavaScript函数的强大功能。

## 三、作用域和闭包

当讨论作用域时，考虑定义变量的位置和变量的生存期时非常重要的。作用域指的是在什么地方可以访问该变量。在JavaScript中，作用域维持在函数级别，而并非块级别。因此，参数以及使用var关键字定义的变量，仅在当前函数中可见。

除了不能访问this关键字和参数之外，嵌套函数可以访问外部函数中定义的变量。这一机制时通过闭包来实现的，它是指：即使在外部函数结束执行之后，内部嵌套的函数继续保持它对外部函数的引用。闭包还有助于减少名称空间的冲突。

每次调用一个包裹的函数时，虽然函数的代码并没有改变，但是JavaScript将为每一次调用创建一个新的作用域。下面的代码说明了这一行为。
```js
function getFunction(value){
  return function(value){
    return value;
  }
}

var a = getFunction(),
    b = getFunction(),
    c = getFunction();

console.log(a(0)); // 0

console.log(b(1)); // 1
console.log(c(2)); // 2
console.log(a === b); // false
```

当定义一个独立函数时（即不绑定于任何对象）时，this关键字绑定雨全局名称空间。作为一个最直接的结果，**当在一个方法内创建一个内部函数时，内部函数的this关键字将绑定于全局名称空间，而不是绑定于该方法**。为了解决这一问题，可以将包裹方法的this关键字简单的赋值给一个名为that的中间变量。
```js
obj = {};
obj.method = function(){
  var that = this;
  this.counter = 0;
  
  var count = function(){
    that.counter += 1;
    console.log(that.counter);
  }
  count();
  count();
  console.log(this.counter);
}
obj.method();
// 输出：
1
2
2
```

## 四、访问级别

在JavaScript中并没有官方的访问级别语法，JavaScript没有类似于Java语言中的private或protected这样的访问级别关键字。默认情况下，对象中所有的成员都是公开和可访问的。但在JavaScript中可以实现与私有或专有属性类似的访问级别效果。要实现私有方法或属性，请使用闭包。
```js
function TimeMachine(){
  //私有成员
  var destination = 'ShangHai CAOHEJING';
  //公有成员
  this.getDestination = function(){
    return destination;
  };
}

var zhangsan = new TimeMachine();
console.log(zhangsan.getDestination());
// ShangHai CAOHEJING
console.log(zhangsan.destination);
//undefined
```

getDestination方法是一个专有方法，它可以访问TimeMachine（时间机器）中的私有成员。另外，变量destination 是“私有”的，它只能通过专有方法getDestination进行访问。

## 五、模块

于私有和专有访问级别类似，JavaScript没有内置的包语法。模块模式是一种简单而流行的方式，用于创建自包含的、模块化的代码。要创建一个模块，只需要声明一个名称空间、将有关函数绑定在该名称空间，并定义私有成员和专有成员即可，下面将使用模块重写TimeMachine对象。
```js
//创建名称空间对象
TIMEMACCHINE = {};
TIMEMACCHINE.createZhangsan = (function(){
  //私有成员
  var destination = '';
  var model = '';
  var fuel = '';
  
  //公有访问方法
  return {
    //设置器
    setDestination: function(dest){
      this.destination = dest;
    },
    setModel: function(model){
      this.model = model;
    },
    setFuel: function(fuel){
      this.fuel = fuel;
    },
    //访问器
    getDestination: function(){
      return this.destination;
    },
    getModel: function(){
      return this.model;
    },
    getFuel: function(){
      return this.fuel;
    },
    //其他成员
    toString : function(){
      console.log(this.getModel() + ' - Fuel Type：' +
                 this.getFuel() + ' - Headed：' + this.getDestination());
    }
  };
})();

var myTimeMachine = TIMEMACCHINE.createZhangsan;
myTimeMachine.setModel('顺丰号');
myTimeMachine.setFuel('钚');
myTimeMachine.setDestination('漕河泾');

myTimeMachine.toString();
// 顺丰号 - Fuel Type：钚 - Headed：漕河泾
```

该模块具有一个工厂函数，它返回一个带有public/privateAPI的对象。

## 六、数组

数组是一种特殊类型的对象，他作为一个有序的值的集合，这些值称为数组元素。在一个数组内，每一个元素都具有一个索引，或称为一个数字位置。在一个数组中可以存储相同类型或不同类型的元素。不要求数组元素全都具有相同的数据类型。于对象和函数类似，数组也可以任意嵌套。

与普通对象一样，可以采用两种方式来创建数组。第一种方法就是使用Array()构造函数：
```js
var array1 = new Array();    //空数组
array1[0] = 1;                        //在索引为0 的位置添加一个数组元素
array1[1] = 'a string';           //在索引为1的位置添加一个字符串
```

更常用的是第二种方式，即使用一个数组字面量来创建数组，它由一对方括号括起来，其中包含了0个或多个一逗号分隔的值。
```js
var niceArray = [1, 2, 3,];
```

在数组字面量中混合使用对象字面量，可以提供非常强大的结构，他是JavaScript对象表示法JSON的基础。JSON是一种流行的数据交换格式，它可以用于多种语言而不仅仅时JavaScript语言。
```js
var myData = {
    'root' : {
         'numbers' : [1, 2, 3],
             'letters' : ['a', 'b', 'c'],
             'mirepoix' : ['tomatoes', 'carrots', 'potatoes']   
     }
}
```

数组具有一个length属性，它的值总是等于数组中元素的个数减11。在数组中添加新元素将改变数组length属性。要从数组中移除元素，请使用delete操作符。
```js
var list = [1, '2', 'blah', {}];
delete list[0];
console.log(list);
// [undefined, "2", "blah", Object {}]
console.log(list.length);
// 4
```

删除一个元素并不会改变数组的长度，只是在数组中留下一个undefined值的元素（空元素）。

## 七、扩展类型

JavaScript支持将方法和其他属性绑定到内置类型。比如字符串、数值和数组类型。于其他任何对象类似，String对象也具有prototype属性。开放人员可以为String对象扩充一些便利的方法。例如，String没有将字符串false或true转换为布尔值的方法。开放人员可以使用下面的代码
为String对象添加该功能：
```js
String.prototype.boolean = function(){
  return "true" == this;
}

var t = 'true'.boolean();
var f = 'false'.boolean();

console.info(t);    //true
console.info(f);    //false
```

显然，每次想为制定类型添加方法时反复输入prototype显得有点累赘》以下提供一段简洁的代码，他使用一个名为method的方法扩充了Function.prototype。下面就是该方法的代码。
```js
Function.prototype.method = function(name,func){
  this.prototype[name] = func;
  return this;
}
```

接下来就可以重写String的boolean方法：
```js
String.method('boolean',function(){
  return "true" == this;
});
"true".boolean();
```

对于编写工具方法库并将其包含在项目中，这一技术非常有用。

## 八、JavaScript最佳实践

下面列出了在进行JavaScript开发时，一些应该注意或应该避免的事项：
* 使用parseInt()函数将字符串转换为整数，但请确保总是指定基数。更好的办法时使用一元操作符(+)将字符串转化为数值。
  好的写法：parseInt("020",10);    //转化为十进制而不是八进制
  更好的写法：console.log(+ "010");    //简单而又高效
* 使用等同（===）操作符来比较两个值。避免意料之外的类型转换。
* 在在定义对象字面量时，在最后一个值的结尾不要使用逗号。例如：
```js
var o = {
    "p1" : 1,
    "p2" : 2,
    //非常糟糕！
}
```

* eval()函数可以接收一个字符串，并将其视为JavaScript代码执行。应该限制eval()函数的使用，它很容易在代码中引入各种各样严重的安全问题。
* 使用分号作为语句的结尾，当精简代码时这特别有用。
* 应该避免使用全局变量。应该总是使用var关键字来声明变量。将代码包裹在匿名函数中，以避免命名冲突，请使用模块来阻止代码。
* 对函数名使用首字母大写表示该函数将作为new操作符的构造函数，但请不要对其他任何函数的函数名使用首字母大写。
* 不要使用with语句。
* 在循环中创建函数应该谨慎小心。这种方式非常低效。

> 最佳摘自Cesar Otero,Rob Larsen《jQuery高级编程》
