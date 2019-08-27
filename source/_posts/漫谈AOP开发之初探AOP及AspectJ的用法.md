---
title: 漫谈AOP开发之初探AOP及AspectJ的用法
date: 2014-10-26 13:39:55
categories:
- 后端
tags:
- Java
- Spring
description: "AOP是一个很成熟的技术，假如项目中有方法A、方法B、方法C……等多个方法，如果项目需要为方法A、方法B、方法C……这批方法增加具有通用性质的横切处理。"
---

## 一、为什么需要AOP技术

AOP 是一个很成熟的技术。

假如项目中有方法A、方法B、方法C……等多个方法，

如果项目需要为方法A、方法B、方法C……这批方法增加具有通用性质的横切处理。

下图可以形象的说明具有通用性质的横切处理的思想：

![](file:///var/folders/xc/nyykbxjj4w938ntjcjymx0rm0000gn/T/WizNote/d46a5cab-2910-4fed-abf3-94bf91c31a10/index_files/wpsD3C5.tmp3cfdfc77-43e2-4d1c-ac14-ffbf40c41c12.png)

**在以前传统的做法是**
1. 先定义一个Advice方法，该方法实现这个通用性质的横切处理。
2. 打开方法A、方法B、方法C……的源代码修改，使得方法A、方法B、方法C……去调用Advice方法。

客户电话： 为每个方法都增加日志。  
客户电话： 为每个方法前都增加权限控制。  
客户电话： 为每个方法都加……  
….  

如果使用AOP，可以做到程序员无需修改方法A、方法B、方法C……，但又可以为方法A、方法B、方法C增加调用Advice方法。

**面向切面编程（AOP）是作为面向对象编程（OOP）的补充**
AOP框架具有如下两个特征：
1. 各步骤之间的良好隔离性。
2. 源代码无关性。

## 二、AOP的功能

保证程序员不修改方法A、方法B、方法C……的前提下，可以为方法A、方法B、方法C……增加通用处理。

AOP的本质：依然要去【修改】方法A、方法B、方法C……

—— 只是这个修改由AOP框架完成，程序员不需要改。

AOP要求去修改，到底怎么去修改方法A、方法B、方法……

**AOP的实现方式有两种**

AOP框架在编译阶段，就对目标类进行修改，得到的class文件已经是被修改过的。生成静态的AOP代理类（生成*.class文件已经被改掉了，需要使用特定的编译器）。以AspectJ为代表 —— 静态AOP框架。

AOP框架在运行阶段，动态生成AOP代理（在内存中动态地生成AOP代理类），以实现对目标对象的增强。它不需要特殊的编译器。以Spring AOP为代表。—— 动态AOP框架。

上面两种，哪种性能更好？很明显静态的AOP框架更好。

下面我们进入AspectJ的学习

## 三、实战AspectJ

AspectJ是一个基于Java语言的AOP框架，提供了强大的AOP功能，其他很多AOP框架都借鉴或采纳其中的一些思想。

**下载和安装AspectJ**
1. 运行、下载得到的安装JAR包。在命令行窗口启动下载得到的jar文件：java -jar aspectj-1.6.10.jar，在弹出的安装界面会先让你选择Java，选择你安装的Java目录 即可。将该软件(绝对绿色）安装到指定目录下（笔者安装在C盘）。
2. 系统还应该将`C:\Java\aspectj1.6\bin`路径添加到PATH环境变量中，将C:\Java\aspectj1.6\lib\aspectjrt.jar和aspectjtools.jar添加到 CLASSPATH环境变量中。

成功安装了AspectJ之后，将会在`E:\Java\AOP\aspectj1.6`路径下（AspectJ的安装路径）看到如下文件结构：
- bin：该路径下存放了aj、aj5、ajc、ajdoc、ajbrowser等命令，其中ajc命令最常用，它的作用类似于javac，用于对普通Java类进行编译时增强。
- docs：该路径下存放了AspectJ的使用说明、参考手册、API文档等文档。
- lib：该路径下的4个JAR文件是AspectJ的核心类库。
- 相关授权文件。

打开DOS窗口，输入ajc命令，可以看到输出ajc命令的所有信息，即可知安装和环境变量配置成功：

![](file:///var/folders/xc/nyykbxjj4w938ntjcjymx0rm0000gn/T/WizNote/d46a5cab-2910-4fed-abf3-94bf91c31a10/index_files/fd543e0f-a031-46d6-8db3-525fa893f15f.png)

**使用AspectJ**

接下来我们模拟一个普通程序：
UserService：
```java
package com.mybry.aop.service;
public class UserService{
	public int addUser(){
		System.out.println("模拟添加用户的方法。");
		return 20;
	}
	public void validateLogin(){
		System.out.println("验证用户登录。");
	}
}
```

BookServce：
```java
package com.mybry.aop.service;
public class BookService{
	public int addBook(String name,int price){
		System.out.println("正在添加图书，书名是："+name+",价格是："+price);
		return 100;
	}
}
```

编译运行结果：

![](file:///var/folders/xc/nyykbxjj4w938ntjcjymx0rm0000gn/T/WizNote/d46a5cab-2910-4fed-abf3-94bf91c31a10/index_files/wps784F.tmp27792296-746a-45d1-aa46-4813d419e942.png)

这两个类正好相当于我们的方法A，方法B.....

假如客户现在要求在每个方法前面增加权限检查功能，那么该如何做呢？下面我们就是用AspectJ来实现这个功能。

现在我们在这个模拟程序基础上增加这个AOP功能

我们先写一个权限检查的Aspectj类:

实例1，AuthAspect：
```java
package com.mybry.aop.aspectj;
public aspect AuthAspect{
	// Advice
	// execution(* com.mybry.aop.service.*.*(..)执行 任意返回值 改包下的任意类的任意方法形参不限
	before():execution(* com.mybry.aop.service.*.*(..)){
		// 对原来方法进行修改、增强。
		System.out.println("----------模拟执行权限检查----------");
	}
}
```

注意这个类色声明类型：aspect，没错，这是写Aspect必须声明的类型，只有AspectJ编译器可以识别。
再用`ajc –d *.java`命令编译执行：

![](file:///var/folders/xc/nyykbxjj4w938ntjcjymx0rm0000gn/T/WizNote/d46a5cab-2910-4fed-abf3-94bf91c31a10/index_files/wps4E5.tmp27877a68-a55f-4960-982c-588848d8ac56.png)

太开心了，搞定了！

假如此时客户又要求在每个方法中增加事物处理呢？也好办，下面是事物处理类：
实例1，TxAspect：
```java
package com.mybry.aop.aspectj;
public aspect TxAspect{
    //around的意思就是在方法的前面和后面都加
	Object around():execution(* com.mybry.aop.service.*.*(..)){
		// 对原来方法进行修改、增强。
		System.out.println("====模拟开启事务====");
		Object rvtVal = proceed();
		System.out.println("====模拟结束事务====");
		return rvtVal;
	}
}
```

好了，我们再来编译运行它：

![](file:///var/folders/xc/nyykbxjj4w938ntjcjymx0rm0000gn/T/WizNote/d46a5cab-2910-4fed-abf3-94bf91c31a10/index_files/wps4B96.tmp1d5122e1-9afe-4446-9cbe-76464a2ee9f1.png)

OK!我们的Aspect AOP程序到此为止！