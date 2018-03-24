---
title: Struts2的视图转发类型总结
date: 2013-09-21 12:51:21
categories:
- 服务端开发
- Java
tags:
- Struts2
- 总结
description: "struts2中转发类型有很多，即内部请求转发，类似于forward的方，比如：dispatcher方式、redirect方式、redirectAction、chain等等，有常用的也有不常用的"
---

## dispatcher方式

这种方式是struts2中默认的转发类型，即内部请求转发，类似于forward的方式。用于指定使用JSP作为视图的结果类型。

## redirect方式

用于直接跳转到其他URL的结果类型。这种结果类型与dispatcher结果类相对，dispatcher结果类型是将请求forword（转发）到指定的JSP资源；而redirect结果类型，则意味着将请求redirect（重定向）到指定的视图资源。

dispatcher与redirect的差别就是重定向和转发的差别：重定向会丢失所以的参数、请求属性--当然也就丢失了Action的出来结果。

使用redirect结果类型的效果是，系统将调用`HttpServletResponse`的s`endRedirect（String）`方法来重定向指定视图资源，这种重定向的效果就是重新产生一个请求，因此所有的请求参数、请求属性、Action实例和Action中封装的属性全部丢失。地址栏的URL会发生改变。

配置一个redirect类型结果，可以指定如下两个参数。
  - location：改参数指定Action处理完用户请求后跳转的地址。
  - parse：改参数指定是否允许在location参数值中使用表达式，改参数默认为true。与前面的类似，通常无需指定parse属性值。

使用这个类型也可以指定跳到一个Action，只是需要添加`.action`后缀，已达到redirectAction结果类型效果。

```java
<action name="redirect">
    <result type="redirect">/add.jsp</result>
</action>

<action name="redirect">
    <result type="redirect">addUser.action</result>
</action>
```

## redirectAction

这种方式可以简单的理解成转向到另一个Action。这种配置往往在下面的情况下需要用到：例如，当管理员添加完一个用户后，系统自动跳转到用户列表的界面。

这种结果类型与redirect类型非常相似，一样是重新生成一个全新的请求。但与redirect结果类型区别在于：redirectAction使用ActionMapperFactory提供的ActionMapper来重定向请求。

配置一个redirect类型的结果，可以指定如下两个参数。（针对不在同一个配置文件中）
- actionName：该参数指定重定向的Action名。
- namespace：该参数需要指定需要重定向的Action所在命名空间。

```java
<action name="redirectAction">
   <result type="redirectAction">listAction</result>
</action>
```

该action必须和redirectAction处在同一个package下面。那么如果不在同一个包下，则需进行如下配置：
```java
<result type="redirectAction">
   <param name="actionName">XXX</param>
   <param name="namespace">YYY</param>
</result>
```

使用redirectAction结果类型时，系统将重新生成一个请求，只是改请求的URL不是一个具体的视图资源，而是另一个Action。因此前一个Action处理结果、请求参数、请求属性会全部丢失。

## chain

这个视图类型也用了跳转到另一个Action，与前面的不同的是，他的请求参数和属性都可以保留，比如，系统中的删除功能，回到之前查询的action，要保存页码回到之前删除记录的当前页，用`chain`就可以做到，几乎几乎就是前面两种的增强版。推荐开发中直接忽略redirect和redirectAction类型，直接用这个chain就好了。但很奇怪，在MyEclipse8.6下会报错，这个可能就比较坑爹了，令人郁闷！但并不影响运行结果。可能是Myeclipse8.6的Bug吧。

## plainText

这种方式一般来说使用的比较少，可能用到的情况：原样输出源代码。配置如下：
```java
<action name="abc" >
   <result type="plainText">
       <param name="location">/index.jsp</param>
       <param name="charSet">UTF-8</param>
   </result>
</action>
```

这时，index.jsp的源代码则会以文本方式显示在浏览器中。

## freeMarker、Velocity

用于指定使用FreeMarker、Velocity模板作为视图结果类型。
```java
<result name="success"type="freemarker">foo.ftl</result> 
```

## HttpHeader

用来控制特殊的Http行为。
```java
<result name="success"type="httpheader">   
       <paramname="status">204</param> 
       <paramname="headers.a">a custom header value</param>  
       <paramname="headers.b">another custom header value</param>  
</result>  

<result name="proxyRequired"type="httpheader">   
       <paramname="error">305</param> 
      <paramname="errorMessage">this action must be accessed through aprozy</param>  
</result>  
```

## Stream

向浏览器发送InputSream对象，通常用来处理文件下载。
```java
<result name="success"type="stream">   
      <paramname="contentType">image/jpeg</param>  
      <paramname="inputName">imageStream</param>  
      <paramname="contentDisposition">attachment;filename="document.pdf"</param>   
      <paramname="bufferSize">1024</param>  
</result>  
```

## xslt

用于与XML/XSLT整合的结果类型。

以上这么多，对于普通的项目来说，一般开发中直接用chain类型就好了，其他的本人认为几乎就是鸡肋。。。。




