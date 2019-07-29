---
title: Spring：一切都是Bean(二)
date: 2014-07-27 18:49:22
categories:
- 服务端开发
- Java
tags:
- Spring
description: "无论是静态工厂方法，还是实例工厂方法，都需要增加一个factory-method属性。实例工厂方法，还需要指定factory-bean属性"
---

## 实例化Bean的3种方式

- 通过bean元素来驱动调用有参数或无参数的构造器。
- 通过驱动静态工厂方法来创建Bean
  - 使用静态工厂方法创建Bean实例时，class指定创建Bean实例的静态工厂类。
  - 使用factory-method属性来指定静态工厂方法名，Spring将调用静态工厂方法（可能包含一组参数），来返回一个Bean实例。
  - 使用`<constructor-arg.../>`元素为工厂方法指定参数值。
- 通过驱动实例工厂方法来创建Bean
  与静态工厂方法创建Bean的配置几乎是相同的。唯一的区别是，如果使用静态工厂方法创建Bean，只要用class属性指定静态工厂类的类名即可。
  如果使用实例工厂方法创建Bean，只要用factory-bean属性指定工厂bean的名字即可。

**结论：无论是静态工厂方法，还是实例工厂方法，都需要增加一个factory-method属性。实例工厂方法，还需要指定factory-bean属性。**

## Bean继承与子Bean

作用是简化配置文件，提高应用的可维护性。

如果Spring配置文件中有多个Bean，它们的部分配置信息是重复的
1. 配置就很臃肿。
2. 如果有天需要修改，就需要同时修改多个地方，不利于维护。  

Spring允许先为一批具有大致相同配置的Bean配置一个Bean模板——将这批Bean中相同的配置信息配置成Bean模板，Bean模板通常配成抽象Bean——就是配置Bean模板。

Bean模板配置完成后，将实际的Bean实例配置成该Bean模板的子Bean即可。子Bean定义可以从父Bean继承实现类、构造器参数、属性值等配置信息，除此之外，子Bean配置可以增加新的配置信息，并可指定新的配置信息覆盖父Bean的定义。

如果有一批Bean配置的大量配置信息完全相同，只有少量配置不同。

可以将这批Bean中大量相同的配置信息抽取成Bean模板（抽象Bean）。各子Bean则可以只需继承该Bean模板，即可获得父Bean的大量配置参数。
```xml
<bean id="dog" class="com.idealsh.test.service.Dog" parent="dogCatTemplate"
p:bark="旺，旺!!" />
<bean id="cat" class="com.idealsh.test.service.Cat" parent="dogCatTemplate"
p:bark="苗，苗~~" />
<!-- 因为Bean模板只是一些通用配置，
因此要通知Spring容器不需要创建Bean模板的实例，因此要增加abstract=“true”
 -->
<bean id="dogCatTemplate" abstract="true" p:name="旺财" p:age="3" />
```

使用`abstract=”true”`，该bean配置不会被创建实例。如果程序试图获取抽象Bean，就会包异常。

子Bean，使用parent=”父Bean”即可。子Bean就会从父Bean那里继承得到大量的配置信息。

## FactoryBean ： 标准工厂Bean接口

- 他是一个接口，该接口里有三个方法。
- 如果一个Bean实现类实现了该接口，并在容器中配置了该Bean。
- 接下来程序获取Bean时，实际返回的只是该Bean的getObject方法的返回值。
- 由于getObject方法由程序员自己实现，因此我们想要在这个方法里做什么操作就可以做什么操作。

## 获取Bean配置ID

在有些时候，程序需要在写Bean类时，就能【预知】Bean的配置ID，此时就可借助于BeanNameAware接口。

和ApplicationContextAware有点相似，实现BeanNameAware接口，必须实现setBeanName方法。

setBeanName方法由Spring调用，Spring调用该方法时会将该Bean的配置id作为参数传入。

## Bean的生命周期行为

对于singleton Bean来说，容器可以跟踪该Bean的产生和销毁。

开发者可以定制Bean的生命周期行为。

Bean完成所有依赖关系的注入后有2种方式可以管理Bean的行为：
1. 使用init-method或default-init-method属性配置。
  init-method所指定的方法，会在Bean实例化、所有属性被注入之后，被Spring自动调用。
2. 实现InitializingBean接口
  InitializingBean接口中定义的方法，会在Bean实例化、所有属性被注入之后，被Spring自动调用(调用该接口的afterPropertiesSet方法)。

**注意：上面两种方式只需要使用一种即可，但是我们推荐使用第一种方式更好。第二种方式会侮辱代码。**

Bean销毁前也可以通过2种方式来管理Bean：
1. 配置destroy-method/default-destory-method属性；
2. 实现DisposableBean接口；    

在JavaSE项目中，Spring容器销毁地太快，容器中的Bean来不及调用生命周期方法回收自己。**需要为Spring容器注册关闭钩子，保证Spring容器以优雅的方式销毁自己**,——容器中的Bean都会调用生命周期方法来执行回收操作。

在Java Web项目中，Spring容器默认就会以优雅的方式销毁。

![](//ww3.sinaimg.cn/large/006tNc79ly1g5d7v5ouubj30j508ntad.jpg)

## 作用域不同步的Bean

当singleton Bean依赖propotype Bean时，singleton Bean会把propotype Bean也变成singleton作用域。

为了解决该问题，解决思路是：
- 放弃依赖注入。
  自定义一个方法，改方法
  1. 获取Spring容器
  2. 获取、并返回prototype Bean
- 使用lookup方法注入。
  1. 将Bean类定义成一个抽象类。
  2. 配置`<lookup_method.../>`子元素让Spring去实现抽象Ban类。
