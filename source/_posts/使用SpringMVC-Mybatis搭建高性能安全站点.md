---
title: 使用SpringMVC+Mybatis搭建高性能安全站点
date: 2016-11-01 23:29:08
categories:
- 后端
tags:
- Java
- Spring
- Mybatis
description: "XSS又称CSS，全称Cross Site Script，跨站脚本攻击，是Web程序中常见的漏洞，XSS属于被动式且用于客户端的攻击方式，所以容易被忽略其危害性。其原理是攻击者向有XSS漏洞的网站中输入(传入)恶意的HTML代码，当其它用户浏览该网站时，这段HTML代码会自动执行，从而达到攻击的目的。如，盗取用户Cookie、破坏页面结构、重定向到其它网站等。"
---

## 一、网站安全

### 1.1 XSS跨站脚本

XSS又称CSS，全称Cross Site Script，跨站脚本攻击，是Web程序中常见的漏洞，XSS属于被动式且用于客户端的攻击方式，所以容易被忽略其危害性。其原理是攻击者向有XSS漏洞的网站中输入(传入)恶意的HTML代码，当其它用户浏览该网站时，这段HTML代码会自动执行，从而达到攻击的目的。如，盗取用户Cookie、破坏页面结构、重定向到其它网站等。

一个简单的例子，页面名字叫test.jsp：
```html
<html>   
   <head>     
      <title>XSS测试</title>   
   </head>   
   <body>     
      页面内容：<%=request.getParameter("content")%>   
   </body>
</html>
```

请求下面链接就会出现问题了：
```html
//www.domain.com/test.jsp?content=<script>alert('XSS注入');</script>
```

### 1.2 SQL注入

SQL Injection，就是通过把SQL命令插入到Web表单递交或输入域名或页面请求的查询字符串，最终达到欺骗服务器执行恶意的SQL命令。

典型例子：
国内最大的程序员社区CSDN网站的用户数据库被黑客公开发布，600万用户的登录名及密码被公开泄露。

一个简单的例子，比如请求连接获取用户信息，请求连接为：`//www.example.com/user?userId=1`，通常代码中会执行SQL语句：
```sql
select * from table_user where userId=${userId}
```

假如SQL注入，请求连接为：`//www.example.com/user?userId=1 or 1=1`，SQL会执行：
```sql
select * from table_user where userId=1 or 1=1
```

## 二、解决之道 ─ 使用HttpServletRequestWrapper类
定义一个Filter,使用HttpServletRequestWrapper类截获并处理参数：

- `<` --> `&lt;`
- `>` --> `&gt;`
- `(` --> `&#40;`
- `)` --> `&#41;`
- `'` --> `&#39;`
- `eval((.*))`  --> `空`
- `[\"\'][\s]*javascript:(.*)[\"\']`  --> `空`
- `script`  --> `空`

### 2.1 定义XssFilter过滤器类
```java
package com.dunizb.demo.common;

import java.io.IOException;
import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;

public class XssFilter implements Filter {
    @Override
    public void destroy() {
        // TODO Auto-generated method stub
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response,
            FilterChain chain) throws IOException, ServletException {
        chain.doFilter(request, response);
    }

    @Override
    public void init(FilterConfig arg0) throws ServletException {
        // TODO Auto-generated method stub
    }
}
```

### 2.2 在web.xml中配置过滤器

```xml
<filter>
  <filter-name>XssFilter</filter-name>
  <filter-class>com.jikexueyuan.demo.common.XssFilter</filter-class>
</filter>
<filter-mapping>
  <filter-name>XssFilter</filter-name>
  <url-pattern>*.html</url-pattern>
</filter-mapping>
```  

### 2.3 使用HttpServletRequestWrapper拦截请求

```java
package com.dunizb.demo.common;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletRequestWrapper;

public class XssHttpServletRequestWraper extends HttpServletRequestWrapper {
    public XssHttpServletRequestWraper(HttpServletRequest request) {
        super(request);
    }
    
    @Override
    public String getParameter(String name) {
        return clearXss(super.getParameter(name));
    }
    
    @Override
    public String getHeader(String name) {
        return clearXss(super.getHeader(name));
    }
    
    @Override
    public String[] getParameterValues(String name) {
        String[] values = super.getParameterValues(name);
        String[] newValues = new String[values.length];
        
        for(int i =0; i< values.length; i++){
            newValues[i] = clearXss(values[i]);
        }
        return newValues;
    }

    /**
     * 处理字符转义
     * @param value
     * @return
     */
    private String clearXss(String value){
        if (value == null || "".equals(value)) {
            return value;
        }
        value = value.replaceAll("<", "&lt;").replaceAll(">", "&gt;");
        value = value.replaceAll("\\(", "&#40;").replace("\\)", "&#41;");
        value = value.replaceAll("'", "&#39;");
        value = value.replaceAll("eval\\((.*)\\)", "");
        value = value.replaceAll("[\\\"\\\'][\\s]*javascript:(.*)[\\\"\\\']", "\"\"");
        value = value.replace("script", "");
        return value;
    }
}
```

## 三、提升性能：使用Mybatis的二级缓存策略

一级缓存是SqlSession级别的缓存，MyBatis默认开启一级缓存。

二级缓存是mapper级别的缓存，是多个SqlSession共享的，其作用域是mapper的同一个namespace，MyBatis默认没有开启二级缓存，需要在setting全局参数中配置开启二级缓存。如果缓存中有数据就不用从数据库中获取，大大提高系统性能。

### 3.1 配置缓存
mybaits的二级缓存是mapper范围级别，除了在SqlMapConfig.xml设置二级缓存的总开关，还要在具体的mapper.xml中开启二级缓存。

Mybatis缓存使用起来非常方便，三个配置就搞定：

**第一个需要配置config.xml ，开启缓存**
```xml
<setting name="cacheEnabled" value="true" />
```

**第二个需要在M apper文件头指定使用缓存**
```xml
<cache readOnly="true" size="500" flushInterval="120000" eviction="LRU"/>
```

其中：
**readOnly="true"**则所有相同的SQL语句返回的是同一个对象，这样有助于提高性能，但并发操作同一条数据时可能不安全，如果设置为false，则相同的SQL语句访问的是cache中的副本。

**size=500**是指缓存多少个对象，默认值是1024。

**flushInterval="120000"**指定缓存过期的时间，单位为毫秒，默认是空，也就是说只要容量足够永远不会过期。

**eviction="LRU"**是缓存的淘汰算法，默认算法是最近最少使用算法，也就是LRU。

第三个配置，在具体的SQ L语句处指定使用缓存，默认开启

```xml
<select id="selectCount" useCache="true">   
    ...
</select>
```

### 3.2 缓存策略

Mybatis有4个最常见的缓存策略：
- LRU，最近最少使用，移除最长时间不被使用的对象，默认策略
- FIFO，先进先出，按对象进入缓存的顺序来移除它们
- SOFT，软引用，移除基于垃圾回收器状态和软引用规则的对象
- WEAK，弱引用，更积极地移除基于垃圾收集器状态和弱引用规则的对象

## 四、在Linux服务器上部署项目

需要Linux操作系统具备如下环境：
1. JDK7+
2. Tomcat7+
3. MySQL5
4. Nginx

部署过程：
1. 导出项目为war包，重命名为ROOT.war
2. 上传到服务器Tomcat的webapps目录
3. 把本地的MySQL数据库导入到服务器上，把xxx.sql上传到服务器
4. 创建数据库：
 - 连接数据库：`mysql -uroot -p`
 - 显示数据库：`show database;`
 - 创建数据库：`create database 数据库名`
 - 打开创建的数据库：`use 数据库名`
 - 执行xxx.sql文件：`source /expost/tomcat/webapps/xxx.sql`
 - 显示数据库中的表：`show tables;`
 - 退出MySQL：`exit`
 
5. 定义Tomcat端口号为808：`vi config/server.xml`
6. 退出vi编辑器：`:wq`
7. 启动Tomcat：`./bin/startup.sh`,观察日志：`tail -f logs/catalina.out`