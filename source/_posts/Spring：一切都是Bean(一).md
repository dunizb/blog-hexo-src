---
title: Spring：一切都是Bean(一)
date: 2014-07-24 22:20:50
categories:
- 服务端开发
- Java
tags:
- Spring
---

## Bean的别名

何谓别名？ 就是外号。

Spring容器中的Bean，为什么要有别名？

XML配置文件中id属性值必须是一个有效的标识符， id属性值不能有特殊字符，否则XML就报错了；但有些时候，程序需要【为Bean起一个包含特殊字符的名字】。此时用id就不行了，只能起别名。
<!-- more -->
起别名的两种方式：
1. 在`<bean .../>`元素中通过name属性来指定别名，多个别名之间用逗号隔开。
2. 专门用`<alias.../>`元素来起别名。

```xml
<bean id="us" name="aa" class="com.idealsh.test.service.UserService"/>
<alias name="us" alias="bb"/>
```

上面的配置下面的配置将会让us对象有三个名字：us、aa、bb

## Bean获取Spring容器

在某些时候，当Bean要实现某个功能时，该Bean必须借助于Spring容器才能完成。

比如说，某个Bean需要提供国际化支持，这就必须通过Spring容器来获得国际化支持。

此时，该Bean就必须获取它所处的容器。

方法： **让Bean实现ApplicationContextAware接口即可。**

实现该接口后，必须实现setApplicationContext方法。

> Spring会通过反射检测所有Bean的实现类是否实现ApplicationContextAware接口，只要实现了该接口，Spring会自动调用该Bean的setApplicationContext方法，并将容器自身作为参数传入。

## Bean的作用域

通俗的的讲就是说Bean活多久，通过scope属性来指定。Bean的作用域，一共有如下几个：
- singleton：单例，所有从Spring容器中取出的总是同一个实例。Spring默认采用该模式。

“与天地同寿，与日月同庚”
  - 只要容器不死，singleton Bean就不死。
  - 容器创建之后，singleton Bean默认会被预初始化

- prototype：不会预初始化。每次通过Spring容器中获取实例，Spring都会new一个新的实例返回。对于prototype Bean而言，Spring每次都new一个实例，new出来之后根本不管这个prototype Bean。
- request：对应web应用中的request作用域。只对Web应用才有效，在一次请求中相当于singleton。
- session：对应web应用中的session作用域。只对Web应用才有效，在一次用户会话中相当于singleton。
- global session：仅在使用portlet context的时候才有效。

一般来说，singleton Bean的性能更好，因为singleton Bean创建成本低，内存开销小。

## beans元素的属性与bean元素的属性的规律

假如bean元素有abc属性，beans元素通常可能有default-abc属性。

如果在bean元素中指定的属性，仅对该Bean起作用；但在beans元素上指定的属性，对所有Bean都起作用。如果他们起冲突了，范围小的胜利。  