---
title: 什么是WebAuthn：在Web上使用Touch ID和Windows Hello登录
date: 2020-12-28 14:31:40
img: https://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/webauthn-logging/banner.png
categories:
  - 技术
tags:
  - WebAPI
  - 翻译
---

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/webauthn-logging/banner.png)

**对你的网站启用 TouchID 和 Windows Hello 身份验证。 WebAuthn 简介：它如何工作以及如何实现。**

<!-- more -->

## 什么是 WebAuthn？

Web Authentication API 是一个[认证规范](https://w3c.github.io/webauthn/)，允许网站使用内置的认证器（如 Apple TouchID 和 Windows Hello）或使用安全密钥（如 Yubikey）对用户进行认证。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/webauthn-logging/1.png)

**它利用公钥加密技术代替密码。**用户注册时，将为该帐户生成一个公钥-私钥对。私钥安全地存储在用户的设备中，而公钥则发送到服务器。然后，服务器可以使用私钥来要求用户的设备签署“挑战书”(challenge)以验证用户身份。

### 向 WebAuthn 注册

注册期间，网站通常会要求用户输入用户名和密码。通过 WebAuthn，网站会生成一对公钥和私钥，将公钥发送给服务器，并将私钥安全地存储在用户的设备中。

![WebAuthn注册流程](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/webauthn-logging/2.png)

### 使用 WebAuthn 登录

登录期间，网站通常会检查用户是否提供了正确的用户名和密码。借助 WebAuthn，网站将发送挑战书并检查浏览器是否可以使用存储在用户设备中的私钥对询问进行签名。

![WebAuthn登录流程](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/webauthn-logging/3.png)

## 纯 JavaScript 实现

要了解更多关于 WebAuthn 的信息，这里有一个关于如何用纯 JavaScript 实现 WebAuthn 的深入解释。请看[苹果在 WWDC20 上的指南](https://developer.apple.com/videos/play/wwdc2020/10670/)。

### 注册

**步骤 1：你的站点请求服务器注册 WebAuthn。**

要求用户输入一些标识符（用户名、电子邮件等）。然后，向你的服务器发送请求，注册一个新的 WebAuthn 凭证。

**步骤 2：服务器指定一些选项来创建新的密钥对。**

服务器指定了一个 `PublicKeyCredentialCreationOptions` 对象，该对象包含了创建一个新的 `PublicKeyCredential`（密钥对）所需的和可选的一些字段。

```js
const optionsFromServer = {
  challenge: "random_string", // 需要转换为ArrayBuffer
  rp: {
    // 我的网站信息
    name: "My Website",
    id: "mywebsite.com",
  },
  user: {
    // 用户信息
    name: "anthony@email.com",
    displayName: "Anthony",
    id: "USER_ID_12345678910", // 需要转换为ArrayBuffer
  },
  pubKeyCredParams: [
    {
      type: "public-key",
      alg: -7, // 接受的算法
    },
  ],
  authenticatorSelection: {
    authenticatorAttachment: "platform",
  },
  timeout: 60000, // 以毫秒为单位
};
```

- `rp`：指定依赖方的信息，用户注册/登录的网站。如果用户注册的是你的网站，那么你的网站就是依赖方。
- `id`：主机的域名，不含协议和端口。例如，如果 RP 的来源是https://login.example.com:1337，那么 `id` 就是 `login.example.com` 或 `example.com`，而不是 `m.login.example.com`。
- `pubKeyCredParams` : 服务器可接受哪些公钥类型。
- `alg` : 一个数字，描述服务器接受的算法，并在 COSE 注册表中**COSE 算法**下进行了描述。例如，-7 用于 ES256 算法。
- `authenticatorSelection` : (可选)限制验证器为平台或跨平台。使用 `platform` 允许像 Windows Hello 或 TouchID 这样的身份验证器。使用 `cross-platform` 允许身份验证器，如 Yubikey。

**步骤 3：在前端，使用选项创建新的密钥对。**

使用 `creationOptions` 我们可以告诉浏览器生成一个新的密钥对。

```js
// 请确保你已经将字符串转换为ArrayBuffer
// 如上所述
const credential = await navigator.credentials.create({
  publicKey: optionsFromServer,
});
```

返回的凭据( `credential` )将如下所示：

```js
PublicKeyCredential {
    id: 'ABCDESKa23taowh09w0eJG...',
    rawId: ArrayBuffer(59),
    response: AuthenticatorAttestationResponse {
        clientDataJSON: ArrayBuffer(121),
        attestationObject: ArrayBuffer(306),
    },
    type: 'public-key'
}
```

**步骤 4：将凭证( credential )发送到你的服务器。**

首先，你可能需要将 `ArrayBuffer` 转换为 base64 编码的字符串或仅仅是字符串。你需要在你的服务器中对此进行解码。

按照这些规范来验证服务器中的凭证。然后，你应该存储凭证信息，允许用户使用此 WebAuthn 凭证登录。

### 登录

**步骤 1：向服务器发送请求以登录。**

这允许服务器发送你的前端需要签名的询问。

**步骤 2：服务器发送挑战书和用户可以登录的 WebAuthn 凭据列表。**

服务器指定一个 `PublicKeyCredentialRequestOptions` 对象，该对象包含要签署的挑战书和用户之前注册的 WebAuthn 证书列表。

```js
const optionsFromServer = {
  challenge: "somerandomstring", // Need to convert to ArrayBuffer
  timeout: 60000,
  rpId: "mywebsite.com",
  allowCredentials: [
    {
      type: "public-key",
      id: "AdPc7AjUmsefw37...", // Need to convert to ArrayBuffer
    },
  ],
};
```

**第 3 步：前端签署挑战书。**

```js
// make sure you've converted the strings to ArrayBuffer
// as mentioned above
const assertion = await navigator.credentials.get({
  publicKey: optionsFromServer,
});
```

返回的 `assertion` 如下所示：

```js
PublicKeyCredential {
    id: 'ABCDESKa23taowh09w0eJG...',	// WebAuthn凭证ID
    rawId: ArrayBuffer(59),
    response: AuthenticatorAssertionResponse {
        authenticatorData: ArrayBuffer(191),
        clientDataJSON: ArrayBuffer(118),
        signature: ArrayBuffer(70),     // 我们需要验证的签名
        userHandle: ArrayBuffer(10),
    },
    type: 'public-key'
}
```

**步骤 4：将 assertion 发送到你的服务器并进行验证。**

在将 ArrayBuffers 发送到服务器之前，你可能需要将其转换为字符串。按照[验证 assertion 的规范](https://w3c.github.io/webauthn/#sctn-verifying-assertion)进行。

当 assertion 被验证后，**用户已经成功登录**。现在你可以生成你的会话令牌或设置你的 cookie，然后返回前端。

## 需要考虑的几件事

**如果用户使用笔记本电脑的 TouchID 登录，你如何允许他们从其他人的笔记本电脑登录？**

如果他们只能从自己的笔记本上登录，可能会有不好的用户体验。一个可能的方法是使用 WebAuthn 作为替代，并始终有一个后备登录方法（例如，使用魔术链接或 OTP）。

**为一个账户添加多个 WebAuthn 凭证。**

你可能希望有一个“设置”页面，允许你的用户从其他设备登录 WebAuthn，例如，如果他们想从笔记本电脑和 iPad 同时登录 WebAuthn。浏览器不知道你在服务器中为用户保存了哪些凭证，如果你的用户已经注册了他们笔记本电脑的 WebAuthn 凭证，你需要告诉浏览器，这样它就不会创建一个新的凭证。使用 `PublicKeyCredentialCreationOptions` 中的 `excludeCredentials`。

## WebAuthn 支持情况

目前，并非所有浏览器都支持 WebAuthn，但支持的浏览器越来越多。请访问 FIDO 网站，查看[支持 WebAuthn 的浏览器和平台列表](https://fidoalliance.org/fido2/fido2-web-authentication-webauthn/)。

![https://fidoalliance.org/fido2/fido2-web-authentication-webauthn/](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/webauthn-logging/4.png)

## 结束

这应该涵盖使用 WebAuthn 注册和登录的基础知识，并帮助你在网站上实现它。

如果你想实现 WebAuthn，这些文档可能会有所帮助：

- React 快速入门-使用 WebAuthn 登录：https://docs.cotter.app/quickstart-guides/react-webauthn
- WebAuthn SDK Reference：https://docs.cotter.app/sdk-reference/web/sign-in-with-webauthn

## 参考

我们引用了这些非常有用的文章来撰写这篇文章：

- WebAuthn Guide：https://webauthn.guide/
- WebAuthn Specs from W3C：https://w3c.github.io/webauthn/

---

原文：[https://blog.zhangbing.site/2020/12/28/what-is-webauthn-logging-in-with-touch-id-and-windows-hello-on-the-web/](https://blog.zhangbing.site/2020/12/28/what-is-webauthn-logging-in-with-touch-id-and-windows-hello-on-the-web/)  
原文：[https://codeburst.io/](https://codeburst.io/what-is-webauthn-logging-in-with-touch-id-and-windows-hello-on-the-web-10e22c49e06c)
