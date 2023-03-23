# 让你的 Flutter 应用和本地后端服务器互相交流

> 原文：<https://betterprogramming.pub/make-your-flutter-app-and-local-back-end-server-talk-to-each-other-579cdeba679a>

## 如何修复“出现 SSL 错误，无法建立与服务器的安全连接”

![](img/b1bdc4fe550e33e774c48162c8f7fd23.png)

由 [Unsplash 上的](https://unsplash.com/s/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)制作者 [NESA 拍摄](https://unsplash.com/@nesabymakers?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

大多数移动应用程序由两部分组成:前端应用程序和后端服务器。开发人员通常在快速原型开发期间使用本地后端服务器。出于安全原因，许多工具/框架禁用不安全的 HTTP 连接。

StackOverflow 上时不时会出现类似“为什么我用 X 构建的 app 只有在本地运行时才会失败？”其中 X 可以是 React、React Native、Cordova 等。

随着 X 的到来，我感觉到了类似的问题，所以这里有一些预先的讨论。

# 失败的例子

## 失败通常表现为失败的请求

下面是我在将请求嵌入 Firebase 库时遇到的一个失败的例子:

```
An SSL error has occurred and a secure connection to the server cannot be made.
NSErrorFailingURLKey=https://localhost:5001/...
Error Domain=kCFErrorDomainCFNetwork
Code=-1200 "(null)" 
[<decode: missing data>]
```

“SSL 错误”表明这可能是一个凭证问题。

## iOS 才是不喜欢不安全感的那个

默认情况下，iOS 不允许不安全的 HTTP 连接，这是上面显示的失败的根本原因。

## iOS，我知道你是对的，但请破例一次

您可以通过在`[flutter_app_root]/ios/Runner/info.plist`中添加以下代码片段来将`localhost`添加到您的 HTTP 白名单中:

```
<key>NSAppTransportSecurity</key> 
<dict>
  <key>NSExceptionDomains</key>
  <dict>
    <key>localhost</key>
    <dict>
      <key>NSExceptionAllowsInsecureHTTPLoads</key>
      <true/>
    </dict>
  </dict>
</dict>
```

*感谢，* [*大尼斯*](https://github.com/densa) *，为妙解。*

由于我们只允许本地主机(其他 HTTP 连接仍然被阻止)，我们的应用程序仍然是安全的。

祝您与 Flutter 和您的本地服务器愉快！