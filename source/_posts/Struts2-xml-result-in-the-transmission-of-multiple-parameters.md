---
title: Struts2.xml的result里传多个参数
date: 2013-12-08 19:56:39
categories: 
- 服务端开发
- Java
tags:
- Struts2
description: "当使用`type＝“redirectAction`” 或`type＝“redirect”`提交到一个action并且需要传递一个参数时。这里是有区别的"
---

```java
<action name="update" class="com.ideal.webapp.action.image.ImageManageAction" method="updateIndexPicList"> 
  <interceptor-ref name="loginStack" />
  <result type="redirectAction">showUpdateIndexPicList?shoppingPicList.id=${shoppingPicList.id}&amp;flag=product&amp;flag2=image&amp;message=success</result>
  <result name="invalid.token">/WEB-INF/jsp/image/bindProduct.jsp</result>
  <result name="input">/WEB-INF/jsp/image/bindProduct.jsp</result>
</action>
```

**注意：**
1、`&`不允许使用需要转义`&amp;`即多个参数之间用`&amp;`隔开
2、不能使用`chain`类型

当使用`type＝“redirectAction`” 或`type＝“redirect”`提交到一个action并且需要传递一个参数时。这里是有区别的：
- 使用`type＝“redirectAction”`时，结果就只能写Action的配置名，不能带有后缀:`.action`
- 使用`type＝“redirect”`时，结果应是action配置名＋后缀名 