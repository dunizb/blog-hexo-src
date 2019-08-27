---
title: Java I/O FTP同步代码及FileZilla Server设置
date: 2013-12-07 18:34:06
categories:
- 技术
tags:
- 后端
- ftp
- Java
---

假设现在有两台机器，一个是你本地开发的机器，一台是服务器，而你本地又有两个应用，需要从shopManage同步到fxShop,如下图.
<!-- more -->
![](//ww1.sinaimg.cn/large/006tNc79ly1g5d7y9z7v0j30ku08g3z0.jpg)

在2的时间节点还要同时同步到右边的服务器上，二本地两个应用之间使用I\O拷贝。

新建一个工具类如下：
第一个方法用IO流方式进行本地拷贝
第二个方法才是FTP方式
```java
public class FileSynchronousUtil {
    /**
     * 文件本机拷贝
     * @param localPath 源路径
     * @param yinPath 目标路径
     * @return
     */
    public static boolean copy(String localPath, String yinPath) {
        try {
            int bytesum = 0; 
            int byteread = 0;
            File oldfile = new File(localPath); 
            if (oldfile.exists()) { // 文件存在时 
                InputStream inStream = new FileInputStream(localPath); 
                String wenjianName = localPath.substring(localPath.lastIndexOf("\\"));
                System.out.println(wenjianName); 
                System.out.println(yinPath + wenjianName);
                // 读入原文件
                FileOutputStream fs = new FileOutputStream(yinPath + wenjianName);
                byte[] buffer = new byte[1444];
                while ((byteread = inStream.read(buffer)) != -1) {
                    bytesum += byteread; // 字节数 文件大小
                    System.out.println(bytesum);
                    fs.write(buffer, 0, byteread);
                }
                inStream.close();
           }
        } catch (Exception e) {
            System.out.println("复制单个文件操作出错");
            e.printStackTrace();
        }
        return true;
    }
 
    /**
     * 使用FTP方式同步文件到远程服务器
     * @param localPath 本地路径
     * @param FtpPath 远程FTP服务器路径
     * @param FtpPathName 远程FTP服务器登录名
     * @param FtpPathPass 远程FTP服务器登录密码
     * @param FtpIp 远程FTP服务器IP地址
     */
    public static void copy(String localPath, String FtpPath, String FtpPathName,
        String FtpPathPass, String FtpIp) {
        //链接FTP
        FTPClient ftpClient = new FTPClient();
        FileInputStream fis = null;
        int reply;
        try {
            ftpClient.connect(FtpIp, 21);
            ftpClient.login(FtpPathName, FtpPathPass);
            reply = ftpClient.getReplyCode();
            if (!FTPReply.isPositiveCompletion(reply)) {
                ftpClient.disconnect();
            }
            fis = new FileInputStream(localPath);
            ftpClient.setBufferSize(10240000);
            String wenjianName = localPath.substring(localPath.lastIndexOf("\\")+1); 
            ftpClient.makeDirectory(FtpPath);
            ftpClient.changeWorkingDirectory(FtpPath);
            ftpClient.setControlEncoding("GBK");
            ftpClient.setFileType(FTPClient.BINARY_FILE_TYPE);
            //被动模式传输
            ftpClient.enterLocalPassiveMode();
//          ftpClient.storeFile(new String(wenjianName.getBytes("GBK"), "iso-8859-1") , fis);
            ftpClient.storeFile(wenjianName, fis);
            fis.close();
        /*  String finalRemoteFileName = FtpPath.replaceAll(".ing", "");
            ftpClient.rename(FtpPath, finalRemoteFileName);*/
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (ftpClient.isConnected()) {
                try {
                    ftpClient.disconnect();
                } catch (IOException ioe) {
                    ioe.getMessage();
                }
            }
        }
    }
}
```

同时准备一个ftp.propertis文件，主要参数都在这里配置，需要读取这个文件：
```
#本地IP
localIp=127.0.0.1
##本地目标地址
local_targetPath=D\:\\tomcat\\apache-tomcat-6.0.16\\webapps\\fxShop
##远程目标地址
remote_targetPath=
#远程FTP服务器IP
remote_ftpIp=127.0.0.1
#远程FTP服务器登录名
remote_ftpServerName=test
#远程FTP服务器登录密码
remote_ftpServerPassWord=123
```
写一个主调（Main）方法，就可以进行调用测试了：
```java
public static void main(String[] args) {
        //读取配置文件，
        InputStream stream = null;
        try {
            //stream = new BufferedInputStream(new FileInputStream(new File("src/common.properties")));
            stream = NowFirst.class.getResourceAsStream("/common.properties");
            System.err.println(stream);
        } catch (Exception e) {
            System.out.println("配置文件未找到！");
        }
         Properties p = new Properties();
         try {
            p.load(stream);
        } catch (IOException e) {
            System.out.println("文件未加载");
        }
        String localPath=p.getProperty("localPath");
        String targetPath=p.getProperty("targetPath");
        String FtpOnePath=p.getProperty("FtpOnePath");
        System.err.println(FtpOnePath);
        String FtpOnePathName=p.getProperty("FtpOnePathName");
        String FtpOnePathPass=p.getProperty("FtpOnePathPass");
        String FtpTwoPath=p.getProperty("FtpTwoPath");
        String FtpTwoPathName=p.getProperty("FtpTwoPathName");
        String FtpTwoPathPass=p.getProperty("FtpTwoPathPass");
        String FtpOneIp=p.getProperty("FtpOneIp");
        String FtpTwoIp=p.getProperty("FtpTwoIp")；
        serviceDao dao=new serviceDao();
        //同步本地
        //dao.copy(localPath, targetPath);
        //同步Ftp
        //第一台
        dao.copy(localPath, FtpOnePath, FtpOnePathName, FtpOnePathPass,FtpOneIp);
        //第二台
        //dao.copy(localPath, FtpTwoPath, FtpTwoPathName, FtpTwoPathPass,FtpTwoIp);
}
```
当然了，是用FTP同步需要FTP服务器支持，你要建立一个FTP服务，比较方便的是使用FileZilla Server

FileZilla Server设置：
首先需要新建一个用户：

![](//ww4.sinaimg.cn/large/006tNc79ly1g5d7yayt55j30ku0azgri.jpg)

直接填一个用户名就好了：

![](//ww2.sinaimg.cn/large/006tNc79ly1g5d7ybrf16j308v060jrn.jpg)

然后账号设置这里，你可以给给他分配一个密码，也可以不写，去掉勾就好了：

![](//ww3.sinaimg.cn/large/006tNc79ly1g5d7ycqqpfj30i90c8gmc.jpg)

接下来就是制定一个共享目录，这个目录就是用来同步到的目标地址：

![](//ww1.sinaimg.cn/large/006tNc79ly1g5d7yd776oj30if0cf75a.jpg)

这里假设建在F盘的FTPTest目录下。
注意：这里的Read、Write必须勾选，否则FTP就没法写入文件了

![](//ww4.sinaimg.cn/large/006tNc79ly1g5d7ye3p4rj303y07b0sl.jpg)

还要设置这个文件夹为系统共享的，右键-属性-共享这个文件夹：

![](//ww1.sinaimg.cn/large/006tNc79ly1g5d7yephvlj30f20dp75h.jpg)

好了，就弄完了。

