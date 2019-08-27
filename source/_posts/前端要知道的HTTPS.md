---
title: 前端要知道的HTTPS
date: 2019-08-03 23:13:48
categories:
- 技术
tags:
- 读书与学习
- 前端
- 网络协议
description: "简单的说，HTTPS 就是 HTTP 的安全版本。SSL（Secure Sockets Layer）以及继任者 TLS（Transport Layer Security）是一种安全协议，为网络通信提供来源认证、数据加密和报文完整性检测，保障通信的保密性和可靠性。HTTPS协议的 URL 都以 “https://”开头，在访问某个 Web 页面时，客户端会打开一条到服务器 443 端口的连接。"
---
![](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201908/https/banner.png)

## 概述
HTTPS（HTTP Secure）是一种构建在 SSL 或 TLS 上的HTTP协议。

简单的说，HTTPS 就是 HTTP 的安全版本。SSL（Secure Sockets Layer）以及继任者 TLS（Transport Layer Security）是一种安全协议，为网络通信提供来源认证、数据加密和报文完整性检测，保障通信的保密性和可靠性。HTTPS协议的 URL 都以 “https://”开头，在访问某个 Web 页面时，客户端会打开一条到服务器 443 端口的连接。

![HTTP和HTTPS](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201908/https/1.png)

之所以说HTTP不安全，是由于以下3个原因导致的：
1. 数据以明文传递，有被窃听的风险
2. 接收到的报文无法证明是发送时的报文，不能保障完整性，因此报文有被篡改的风险
3. 不验证通信两端的身份，请求或响应有被伪造的风险

![HTTP的风险](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201908/https/2.png)

## HTTPS的组成
### 加密
在密码学中，加密是指将明文转换为难以理解的密文；解密与之相反，是把密文换回明文。加密和解密都是由两部分组成：算法和密钥。

加密算法可以分为两类：对称加密和非对称加密。

### 对称加密

对称加密在加密和解密的过程中只是用一个密钥，这个密钥叫对称密钥，也叫共享密钥，如下图。

![对称加密](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201908/https/3.png)

对称加密的优点就是计算速度快，缺点也很明显，就是通信两端需要分享密钥。客户端和服务器在进行对话前，要先将对称密钥发送给对方，在传输的过程中密钥有被窃取的风险，一旦被窃取，那么密文就能轻松翻译成明文，加密保护形同虚设。

### 非对称加密
非对称加密在加密的过程中使用公开密钥（public key），在解密的过程中使用私有密钥（private key）。加密和解密的过程也可以反过来。

![非对称加密](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201908/https/4.png)

非对称加密的缺点时计算速度慢，但它很好的解决了对称加密的问题，避免了信息泄露。通信两端如果都使用非对称加密，那么各自都会生成一对密钥，私钥留在身边，公钥发送给对方，公钥在传输途中即使被人窃取，也不用担心，因为没有私钥就无法轻易解密。在交换公钥后，就可以用对方的公钥把数据加密，开始密文对话。

HTTPS采用混合加密机制，将两种加密算法组合使用，充分利用各自的优点，博采众长。在交换公钥阶段采用非对称加密，在传输报文阶段使用对称加密。

### 数字签名
数字签名是一段由发送者生成的特殊加密校验码，用于确定报文的完整性，数字签名的生成涉及两种技术：非对称加密和数字摘要。

数字摘要可以将变长的报文提取为定长的摘要，报文内容不同提取的摘要也将不同，常用的摘要算法有 MD5 和 SHA。签名和校验的过程总共分为 5 步，如下如。
1. 发送方用摘要算法对报文进行提取，生成一段摘要
2. 然后用私钥对摘要进行加密，加密后的摘要作为数字签名附加在报文上，一起发送给接收方
3. 接收方收到报文后，用同样的摘要算法提取出摘要
4. 再用接收到的公钥对报文中的数字签名进行解密
5. 如果两个摘要相同，那么就能证明报文没有被篡改

![数字签名的校验过程](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201908/https/5.png)

### 数字证书
数字证书相当于网络上的身份证，用于身份识别，由权威的数字认证机构（CAlendar）负责颁发和管理、数字证书的格式普遍遵循 X.509 国际标准，证书的内容包括：
1. 有效期
2. 颁发机构
3. 颁发机构的签名
4. 证书所有者的名称
5. 证书所有者的公开密钥
6. 版本号和唯一序列号

客户端（如浏览器）会预先植入一个受信任的颁发机构列表，如果收到的证书来自陌生的机构，那么会弹出一个安全警报对话框，如下图时IE浏览器的安全警报。

![IE的安全警报](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201908/https/6.png)

一般数字证书都会被安装在服务器处，当客户端发起安全请求时，服务器就会返回数字证书。客户端从受信机构列表中找到相应的公开密钥，解开数字证书。然后验证数字证书中的信息，如果验证通过，就说明请求来自意料之中的服务器；如果不通过，就说明证书被冒用，来源可疑，客户端立刻发出警告。下图描绘了这个认证过程。

![数字证书的认证过程](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201908/https/7.png)

### 安全通信机制
客户端和服务器将通过好几个步骤建立起安全连接，然后开始通信，下面时精简过的步骤：
1. 客户端发送 Client Hello 报文开始 SSL 通信，报文中还包括协议版本号、加密算法等信息
2. 服务器发送 Server Hello 报文作为应答，在报文中也会包括协议版本号、加密算法等信息
3. 服务器发送数字证书，数字证书中包括服务器中的公开密钥
4. 客户端解开并验证数字证书，验证通过后，生成一个随机密码串（premaster secure）,再用收到的服务器公钥进行解密，发送给服务器
5. 客户端再次发送 Change Cipher 报文，提示服务器在此条报文之后，采用刚刚生成的随机密码串进行数据加密
6. 服务器也发送 Change Cipher Spec 报文
7. SSL 连接建立完成，接下来就可以开始传输数据了

下图描绘了这些步骤

![HTTPS通信](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201908/https/8.png)


## 常见笔试面试题
### HTTPS有哪些缺点？
HTTPS有如下4个缺点
1. 通信两端都需要进行加密和解密，会消耗大量的CPU、内存等资源，增加了服务器的负载
2. 加密运算和多次握手降低了访问速度
3. 在开发阶段，加大了页面调试难度。由于信息都被加密了，所以用代理工具的话，需要先解密然后才能看到真实信息。
4. 用 HTTPS 访问的页面，页面内的外部资源都得用HTTPS请求，包括脚本中的AJAX请求

### 什么是运营商劫持？有什么办法预防？
运营商是指提供网络服务的ISP（Internet Service Provider），例如三大基础运营商:中国电信、中国移动和中国联通。

运营商为了牟取经济利益，有时候会劫持用户的HTTP访问最明显的特征就是在页面上植入广告，有些是购物广告，有些却是淫秽广告，非常影响界面体验和公司形象。为了避免被劫持，可以让服务器支持 HTTPS HTTPS 协议，HTTPS传输的数据都被加密过了，运营商就无法再注入广告代码，这样页面就不会再被劫持。

*******
**相关推荐：**
- [写给前端的HTTP知识](https://blog.dunizb.com/2017/12/08/You-have-to-know-the-basic-HTTP-concepts/)
- [前端要知道的RESTful API架构风格](https://blog.dunizb.com/2019/07/28/%E5%89%8D%E7%AB%AF%E8%A6%81%E7%9F%A5%E9%81%93%E7%9A%84RESTful-API%E6%9E%B6%E6%9E%84%E9%A3%8E%E6%A0%BC/)

*******
本文摘抄自《前端程序员面试笔试宝典》，媛媛之家/组编 平文 等/编著，机械工业出版社，版权归原作者所有