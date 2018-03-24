---
title: Tomcat学习笔记
date: 2013-12-28 00:50:21
categories:
- 服务端开发
- Java
tags:
- tomcat
- 学习笔记
---

## 一、Web服务器及Tomcat介绍

Tomcat 服务器是一个免费的开放源代码的Web 应用服务器，它是Apache 软件基金会（Apache Software Foundation）的Jakarta 项目中的一个核心项目，由Apache、Sun 和其他一些公司及个人共同开发而成。

特点：免费,处理速度快。大应用及大访问量支持吃力。不支持EJB。 

## 二、Tomcat详细分析

**1、启动Tomcat**

将apache-tomcat-7.0.10-windows-x86.zip解压到磁盘根目录,如果不是根目录，建议不要有中文目录。

解压完成后,进入apache-tomcat-7.0.10，进入bin目录,找到startup.bat(如果是linux,则是startup.sh)双击，启动成功，浏览器输入:http://127.0.0.1:8080/

**2、Tomcat启动闪一下就没了问**

- 没有配置环境变量
  解决: 没有配置JAVA环境变量，添加如下变量:
  JAVA_HOME : JDK的安装目录
  PATH: JDK的安装目录\bin
- JDK版本太低，Tomcat7必须是jdk6之后的版本
- 8080端口被占用  

**3、omcat启动分析**

- startup.bat
- catalina.bat
- setclasspath.bat  `%JAVA_HOME%\bin\java.exe`

结论: 因为Tomcat是java写成的，Tomcat启动时需要找到JDK运行

**4、Tomcat目录结构分析**

bin:  启动和停止脚本文件  
conf: Tomcat的配置文件,最重要的文件就是server.xml  
lib:  Tomcat需要的jar文件  
logs: 日志文件  
temp: 临时文件  
webapps: web应用存放目录   
work:  Tomcat的工作目录(jsp)  

## 三、Web应用解释

web资源：包括html文件、css文件、js文件、动态web页面、java程序、支持jar包、配置文件等

web应用： Web应用是多个web资源的集合

## 四、使用Tomcat的webapps自动发布web应用

标准的web应用的目录结构:
```
web应用名称(文件夹)
	--WEB-INF(文件夹)
	  --web.xml(非常重要)
```

只要将web应用放在webapps文件夹下面,Tomcat会自动部署这个web应用，web应用名称就是文件夹的名称。

在webapps目录下新建一个文件夹，作为web应用的名称，在里面新建一个hello.html，再拷贝一个web.xml，启动Tomcat即可访问了：
http://localhost:8080/hello/hello.html      

## 五、使用虚拟目录方式发布Web应用
```xml
<Host name="localhost"  appBase="webapps" unpackWARs="true" autoDeploy="true">
```
appBase：应用的基准目录

autoDeploye : 自动部署

一个Host元素代表一个web站点,一个web站点下面可以建立多个虚拟目录(web应用)
```xml
<Context path="/hello" docBase="d:/hello" />
```
http://localhost:8080/fkhello/hello.html

Context元素：
1. unpackWARs ：Tomcat在运行web程序前将展开所有压缩的Web应用程序，默认值是true。
2. reloadable：Tomcat服务器在运行时，会监视WEB-INF/classes和WEB-INF/lib目录下类的改变，如果发现有类被更新，Tomcat会自动重新加载应用程序。
3. docBase：指定Web应用程序的文档基本目录或者WAR文件的路径名
4. path：指定Web应用程序的上下文路径

## 六、虚拟站点
http:/www.baidu.com

**6.1**
进入C:\Windows\System32\drivers\etc\hosts
加入127.0.0.1       www.baidu.com,目的是让机器能够找到www.baidu.com所代表的ip地址
输入:http://www.baidu.com:8080/hello/hello.html

**6.2**
修改服务器端口,将8080改为80，80是默认端口
```xml
<Connector port="8080" protocol="HTTP/1.1" connectionTimeout="20000" redirectPort="8443" />
```
http://www.baidu.com:80/hello/hello.html
http://www.baidu.com/hello/hello.html

**6.3**
修改path
```xml
<Context path="/" docBase="d:/hello" />
```
可以不输入web应用名称
http://www.baidu.com/hello.html

**6.4**
设置欢迎文件
```xml
 <welcome-file-list>
    <welcome-file>/hello.html</welcome-file>
</welcome-file-list>
```

如果不输入要访问的web资源文件,则默认访问/hello.html
http://www.baidu.com

## 七、什么是DNS？

DNS(Domain Name Service)DNS是电信内部的一个域名和IP地址的映射关系

在查询DNS之前，先查看本地操作系统对应的HOSTS文件，是否能找到对应的IP，如果能找到，不会查DNS了，只有在查找不到的情况下，再连网找DNS服务器 .

如果我们在hosts文件中配置了127.0.0.1       www.baidu.com，你就不能访问baidu的搜索引擎了。

上网时，输入的域名(www.baidu.com)，会首先进入到hosts文件中查找是否有对应的地址，如果有直接访问。如果没有，连到电信查询，通过DNS查找访问。

## 五、服务器是如何加载web项目的？
1. web服务器启动时会读取tomcat最重要的配置文件server.xml，然后加载所有的web项目。
2. 加载web项目的时候会读取每一个web项目的web.xml部署配置文件，将读取到得内容加载到内存当中。
3. 客户端发送请求http://localhost:8080/firstapp/hello
4. 服务器判断请求的url“/hello”，从内存中查找匹配的url-pattern，没有找到则404.
5. 找到匹配的url-pattern，获取`<servlet-name>`，查找对应的  `<servlet-class>org.fkjava.servlet.NowServlet</servlet-class>`
6. 确定处理请求的servlet之后，通过反射获取该servlet的实例，并调用该实例的service方法处理请求。
7. 客户端接收到返回的结果，渲染成可以识别的html，显示。