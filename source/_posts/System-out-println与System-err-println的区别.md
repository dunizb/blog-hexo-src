---
title: System.out.println与System.err.println的区别
date: 2013-10-29 15:17:21
categories:
- 服务端开发
- Java
tags:
- javase
---

`System.out.println`能重定向到别的输出流，这样的话你在屏幕上将看不到打印的东西了， 而`System.err.println`只能在屏幕上实现打印，即使你重定向了也一样。
<!-- more -->
当向控制台输出信息时，开发者有两个选择：`System.out`和`System.err`。使用者更倾向于输出的是`System.out`，而如果是`System.err`则输出“error”。尽管这看起来是显而易见的，但很多开发者都不了解为什么出错和调试时使用`System.err`。

当输出一个流时，JVM和操作系统共同决定何时输出这个流。也就是说，尽管开发者键入了：
```java
System.out.print("Test   Output:");
```

JVM和操作系统的组合体并不会立即输出这个流。相反，它将保持等待状态直到将要输出的东西达到一定的量。   

假设输入以下指令：
```java
System.out.println("Debugging Info."); 
```

JVM可能同意输出；然而，操作系统可能决定暂不输出。   

由于这个原因，在调试程序时想要发现出错的位置就有可能成为问题。考虑以下的程序：
```java
for(int   i=0;   i<56;   i++)   {   
    System.out.println(i);   
    ...   
    //   containing   an   error   
}
```

错误可能出现在i等于54时，但是可能JVM在i等于49时就结束输出了。50到54仍然存在于缓存中，结果也就丢失了。使用`System.err`来报告错误、调试程序就可以避免这种情况出现，它将使每一次操作的结果都输出出来。例如以下程序：
```java
for(int   i=0;   i<56;   i++)   {   
     System.err.println(i);   
     ...   
     //   containing   an   error   
} 
```

在每一次i等于54时都将显示错误信息。

`System.out.println`可能会被缓冲,而`System.err.println`不会.

`System.err和System.out`就是错误输出和标准输出。如果你用LOG4J记录日志的话,且设定错误等级的话，`System.err`的输出是将记录到日志中。

输出设备是一样的所以你看到的是一样的`System.setErr()`，`System.setOut()`是重定向两个流的方法。

`System.err.println()`是要缓冲的,所以优先级会高点,而`System.out.println()`是不需要缓冲的,所以优先级会低点.

另外，特别的，当你使用MyEclipse和Tomcat6以上时，输出`System.err.println("aaaa")`到控制台是红色显示的，在控制台很显眼，一下就能找到，非常适合输出调试信息，这个我个人比较喜欢用，比如：
```java
public static void main(String[] args) {
    System.err.println("aaaaaaaaaaaaaaaa");
}
```
![](http://ww3.sinaimg.cn/large/006tNc79ly1g5d8fnskgwj30au03ljr6.jpg)
