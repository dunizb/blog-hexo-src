---
title: 手把手教你GlassFish与Eclipse的集成安装、配置
date: 2012-08-02 10:54:52
categories:
  - 后端

tags:
  - Java
  - 服务器
  - 其它
---

不说开场白介绍了，不太会说，直接进入主题

<!-- more -->

## 一、GlassFish 安装配置之前需要先安装配置好 JDK 和 Ant。

下面先介绍 JDK 和 Ant 的下载、安装和配置

### JDK 安装

这一步没什么好说的，基础的东西

[下载地址]：java.sun.com/javase/downloads/index.jsp 这里有各个版本的 JDK 的下载，选择相应适合的版本下载， 下载完成是.exe 格式文件，直接安装即可。注意：安装路径最好不要有空格。
![](//ww4.sinaimg.cn/large/006tNc79ly1g5d87z2vy1j308g0513z0.jpg)

配置环境：Windows 下 Java 用到的环境变量主要有 3 个，JAVA_HOME,CLASSPATH,path

> JAVA_HOME 设置：指向 JDK 的安装路径，这里假设是 D:\JDK6
> path 设置：保留原来的 path 内容，在其最后加上 %JAVA_HOME%\bin,别忘了中间用 ; 隔开。
> CLASSPATH 设置：".;%JAVA_HOME%\lib\dt.jsr;%JAVA_HOME%\tools.jar;%JAVA_HOME%\bin"

最前面的`.`是告诉 JDK 搜索 class 时先查找当前目录的 class 文件，至于 classpath 后面制定的具 体文件是由 Java 语言的 import 机制和 jar 机制决定的

此时本人机器的配置如下：
![](//ww1.sinaimg.cn/large/006tNc79ly1g5d886453ej30ak0btgn1.jpg)

classpath 的值：`.;%JAVA_HOME%\lib;%JAVA_HOME%\lib\tools.jar`

### 配置 Ant ，很简单

[下载]apache-ant-1.8.3-bin 的下载请看附件 2
[配置环境]:

- 解压 ant 包到本地目录
- 设置 ANT_HOME=（实际解压缩的目录）
- 设置 path，保留 path 原来的内容在其最后加上%ANT_HOME%\bin

如上图 ANT_HOME 的值

## 二、下面就真正开始 GlassFish 安装

[下载地址]：GlassFish.dev.java.net/public/downloadsindex.html （如不能下载或下载不能用请下载附件 4）

下载的 GlassFish 是一个 jar 包，例如：GlassFish-v2ur2-b04-windows.jar，下载后放在某个目录下，在设置好 JDK 和 Ant 相关的环境变量后转到命令行状态，

**解压：执行以下命令**

```shell
java -Xmx256m -jar "目标文件"  即：java -Xmx256m -jar GlassFish-v2ur2-b04-windows.jar
```

此步骤进行解压缩操作。会弹出一个确认对话框，窗口可能出现在最底层，现实桌面会发现窗口。另外，你需要拖动下滚动条才让您点击同意。或者一段时间后才可以下一步。

正常解压情形：
![](//ww3.sinaimg.cn/large/006tNc79ly1g5d88dmacwj30il0c642s.jpg)

成功后会在当前目录生成一个 glassFish 文件夹

**安装**
进入 GlassFish 主目录，会发现有一个 setup.xml 文件，继续执行以下命令：

```shell
D:\>glassfish>ant -f setup.xml
```

此命令完成 GlassFish 的安装 ,如图：
![](//ww4.sinaimg.cn/large/006tNc79ly1g5d88kyd3ij30il0c6434.jpg)

**启动 GlassFish 服务**
进入 GlassFish 目录下的 bin 目录，执行以下命令

```shell
C:\user>d:
D:\>cd glassfish\bin\
D:\>glassfish\bin>asadmin start-domain
```

如果执行成功，如下图：
![](//ww3.sinaimg.cn/large/006tNc79ly1g5d88s3rbvj30ik0c70yd.jpg)

如果这里出现问题，请到安装目录下查看，在 bin 同目录下是否有 domains 这个文件夹。如果没有，需要手动创建一个服务域， 代码如下：

```shell
C:\user>asadmin create-domain --adminport 4848 domain1
```

端口是 4848，建议用户名是：admin，密码是 adminadmin 和默认的统一。

**测试**
GlassFish 默认管理端口为 4848，默认管理员为：admin，默认口令为：adminadmin,在浏览器上输入 localhost:4848,就会出现管理控制台。输入http://localhost:4848

![](//ww2.sinaimg.cn/large/006tNc79ly1g5d88z5cdhj30ku0brdhg.jpg)

如果能成功登陆，即这一步 OK！接下里继续往下看：  
停止 GlassFish 服务，同样进入 GlassFish 目录下的 bin 目录，执行以下命令  
asadmin stop-domain  
![](//ww4.sinaimg.cn/large/006tNc79ly1g5d8966uv8j30hs021aa6.jpg)

GlassFish 是通过 ant 来安装的，安装在脚本 setup.xml 下，在里面可以修改一下配置，比如端口等等
在 setup.xml 中主要有以下设置：

```xml
<property name="domain.name" value="domain1"/>域名<property name="instance.name" value="server"/>
<property name="admin.user" value="admin"/>管理员用户名
<property name="admin.password" value="adminadmin"/>管理员密码
<property name="admin.port" value="4848"/>管理平台端口
<property name="instance.port" value="8080"/>实例端口，也就是通过这个端口来访问web应用<property name="orb.port" value="3700"/>
<property name="imq.port" value="7676"/>
<property name="https.port" value="8181"/>https端口
```

根据需要修改以上设置，执行：ant -f setup.xml,如果系统没有安装 ant，在 GlassFish\lib\ant 下有一个 ant，安装结束后，进入 GlassFish/bin 下，在控制台下面命令启动 GlassFish

asadmin start-domain domain1 //domain1 是上面设置的 domain.name,系统默认 domain1

启动结束后，asadmain stop-domain domain1 //停止服务器

## 三、在 GlassFish 中部署 web 应用

有 3 种方式

第一种，可以直接将 war 或 ear 放在 GlassFish/domain/autodeploy 目录下，GlassFish 启动后会自动部署

第二是通过命令 asadmain deploy 部署应用，另外 asadmain updeploy 可以卸载应用

通过 asadmain deploy --help 和 asadmain undeploy --help 获得更多帮助

第三是通过 GlassFish 管理控制台

以上 3 种我讲讲第 2 种，用命令行的方式：

```
进入glassfish的主目录下的bin目录：d: -->cd glassfish\bin
启动glassfish：asadmin start-domain
部署项目：asadmin deploydir --name 你项目名 你项目的WebContent的绝对路径
```

例如，我本人的情况，项目名 li555（可以随便取，只是个标识）
![](//ww2.sinaimg.cn/large/006tNc79ly1g5d899fdpej30hx02mwf1.jpg)

这样，你的 glassfish 服务器就开启了，项目可以运行了，你也可以在浏览器的管理控制台上看到和启动你的项目：
刷新你的管理控制台，在左边找到 “Web Applications”
![](//ww1.sinaimg.cn/large/006tNc79ly1g5d89bas4pj30ku06vab3.jpg)

不好意思，说错了，Action 里的 launch 才是启动项目

## 四、GlassFish 与 Eclipse 的集成

打开你的 Eclipse，访问菜单 新建一个 Server
![](//ww2.sinaimg.cn/large/006tNc79ly1g5d89c6ownj30ku03q3yo.jpg)

下载雾服务器适配链接
![](//ww3.sinaimg.cn/large/006tNc79ly1g5d89d7lfsj30ek0eujsy.jpg)

找到 GlassFish，其实我这个 Eclipse 还是没有的，我就截个图让你们看看，解决你的 Eclipse 获取不到 GlassFish 的方法再这节后面再讲，可能是 Eclipse 的版本问题，我的干开始很郁闷的，希望你们没我悲催
![](//ww2.sinaimg.cn/large/006tNc79ly1g5d89e18dyj30ej0gv0v1.jpg)

接下来就可以在新建 Serv 里的看到 GlassFish 了，点一下部去配置
![](//ww4.sinaimg.cn/large/006tNc79ly1g5d89ezkmqj30eh0ev0ul.jpg)

设置 Dmain 目录，指定你的 GlassFish\domains,
![](//ww3.sinaimg.cn/large/006tNc79ly1g5d89fg8i4j30ee0f0gmk.jpg)

下一步（添加项目到服务器），也可以直接 Finis 完成了，我点完成，在 Serv 视图总就可一看到如图
![](//ww4.sinaimg.cn/large/006tNc79ly1g5d89gds7vj30ku0480sy.jpg)

Ok！大功告成，恭喜你做到最后一部！点小虫子就可以启动了，不需要在 DOS 窗口用命令行了，就和用 Tomcat 一样的了。

## 五、热部署

目标就是像 MyEclipse 一样，有 redeploy 功能。不用手动重新部署。

我这里只写步骤，遇到问题了请参考//www.iteye.com/topic/141589，如果需要用到 touch.exe 文件，请到附件里去下载。

1. 在 Eclipse 里，把项目的编译的.class 输出到 WebContent/WEB-INF 目录下。具体做法在项目右击到 properties--Java Build Path -- source --Default output folder，选择 WebContent/WEB-INF，在里面建一个 classes，把.class 保存在里面。
2. 按照目录部署，让 glassfish 启动，目录部署命令如下，asadmin deploydir --name 你项目名 你项目的 WebContent 的绝对路径
3. 在 WebContent 下新建一个.reload 文件， 把 touch.exe 也拷贝到这个目录下。
4. 在 Eclipse 里 Run--External Tools -- External tools configuration
   Main 里的 location:\${project_loc}/WebContent/touch.exe

> 如：D:\ExamWorkspace\li555\WebContent\touch.exe

Working directory:\${project_loc}/WebContent  
Arguments:.reload  
Common 里的 Display in Favorites menu 选中 Extends tools。  
以后每次修改.java 文件都可以通过 touch.exe 直接热部署。  
![](//ww2.sinaimg.cn/large/006tNc79ly1g5d89hisz0j30gq0hjacz.jpg)

## 六、GlassFish 配置 jdbc 数据源

对于 Java EE 应用，经常需要事先设定数据源，否则部署时会报：javax.naming.NameNouFoundException  
配置方法是进入 Resources -> JDBC ,会看到 JDBC Resources 和 Connection pools

先设定 Connection pools，以 MySql 为例，点击 New，命名为 MySqlPools,ResourceType 选择 javax.sql.ConnectionPoolDataSource, Database vendor 肯定选择 mysql，然后点击 next 进入下一页面

最主要是设定 Additional Properties,也就是 jdbc 连接配置，设定好 url,user,password,其他保持默认值，也可以根据需要自己添加属性。
设定好连接池后，接着设定 JDBC Resources，新建一个 JDBC，名称要和 web 应用里的持久化单元采用的数据源的名称一致。

然后再次部署 web 应用，就会正常运行了。

以上是我查询网上的资料，然后根据自己的动手实践整理的，希望对大家有用。

下面来说说前面配置你可能会出现的错误：

- 你的 Eclipse 在新建 Server 的时候，可能并不能获取到 GlassFish,也就是说 Download additional server adapters 获取不到 Glassfish,其他常规方法还是不行，我试过了，我的 Eclipse 就怎么也不行，后来我下载了另一个 Eclipse 的版本，叫 JUNO(朱诺)，我机器上之前是 HELOIS（太阳神）

Eclipse Juno 的下载地址：//www.eclipse.org/downloads/
![JUNO(朱诺)](//ww2.sinaimg.cn/large/006tNc79ly1g5d89ibyvoj30cl08ggml.jpg)

![HELIOS(太阳神)](//ww1.sinaimg.cn/large/006tNc79ly1g5d89j5r7xj30cq08a0tp.jpg)

让后用它去获取，能获取的到，在打开你的 Eclipse Helios，很奇怪，此时 helios 自动出来了。
