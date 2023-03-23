# Appwrite 后端服务器版本 0.4 已发布

> 原文：<https://betterprogramming.pub/appwrite-backend-server-version-0-4-is-out-learn-whats-new-e1516bf14ee>

## 了解最新动态

![](img/7ba8111f1628eeb4f7be026b2d055e46.png)

蒂姆·莫斯霍尔德在 [Unsplash](https://unsplash.com/s/photos/launch?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

我们很高兴地宣布 [Appwrite](https://appwrite.io/) 后端服务器版本 0.4 的发布。新的 [Appwrite](https://appwrite.io/) 版本包括新的功能和错误修复，使 Appwrite 更接近我们的第一个稳定版本(1.0)。与每个 Appwrite 版本一样，如果没有 [Appwrite 开发者社区](https://github.com/appwrite/appwrite)的大力帮助，这是不可能的。

如果这是你第一次听说 Appwrite，你应该知道它是一个面向 web 和移动开发者的完整开源后端服务器。Appwrite 为开发人员提供了开始构建 web 或本机应用程序所需的所有通用 API。目前，Appwrite 仅作为一个自托管服务提供，您可以使用 Docker 轻松安装它。你可以在 [appwrite.io](https://appwrite.io) 或者我们的 [GitHub 资源库](https://github.com/appwrite/appwrite)了解更多信息。

这是 Appwrite 后端服务器的第四个版本，我们很高兴我们继续努力实现每月发布一个版本的目标。

这个版本包括十个新功能、八个错误修复和三个安全补丁。下面是这个版本的完整的更改日志。

# 0.4 版更改日志

## **功能**

*   为语言环境服务和电子邮件模板添加了五个新的语言环境(is、ml、th、fo、ph、pn)。
*   两阶段船坞建造。核心 Docker 图像大小减少到 127MB。
*   将 HTTP 来源检查仅限于浏览器集成。
*   在[欧盟国家](https://appwrite.io/docs/locale#getCountriesEU)服务中将新英国退出欧盟日期更新为 2020 年 1 月 31 日。
*   为登录和注册页面添加了版本号，以便更容易地调试问题。
*   我们现在使用本机 Docker 卷来提供更好的跨平台支持和更轻松的读/写权限管理。
*   增加了对自定义 SSL 证书的支持，无需设置代理服务器。
*   添加了进行 API 调用时的项目 UID 验证检查。这应该有助于开发人员更好地理解我们的认证错误。
*   ClamAV docker 映像已更新至 1.0.7 版。
*   MariaDB docker 映像已更新至 1.0.1 版。

## **安全**

*   [PHP-FPM 安全补丁修复](https://bugs.php.net/patch-display.php?bug_id=78599&patch=0001-Fix-bug-78599-env_path_info-underflow-can-lead-to-RC.patch&revision=latest) —将 PHP 版本升级至 7.3.12【主要】。
*   移除头像文件的可执行权限[Minor]。
*   由于安全问题，SDK 生成器更新了 Twig 依赖关系:【https://www.exploit-db.com/exploits/44102】T4【未成年】。

## **Bug 修复**

*   创建新项目时添加了新的加载消息。
*   修复了创建新项目时损坏的重定向 URL。
*   修复了在尝试从 Appwrite 控制台创建用户时，用户密码太短时出现的错误。
*   修复了拒绝在端口不是 80 或 443 的本地主机上创建会话 cookies 的问题。
*   修正了阻止实际文件大小计算的错误。
*   修复了 MariaDB SQL 滥用表时间列字段类型。
*   修正了注册失败时控制台不显示错误信息的问题。
*   修复了从 IP 主机名访问控制台时未正确设置 cookie 会话的问题。

## **突破性变化**

*   OAuth 路径现在是/auth/login/oauth，而不是/auth/oauth，并且/auth/oauth/callback 现在是/auth/login/oauth/callback，这是为了更好地与我们将来引入的新登录方法保持一致。
*   将文件属性名称从“sizeCompressed”更改为“sizeActual ”,以更好地反映服务器逻辑。

# 开始

如果您还没有开始使用 Appwrite 后端服务器，您可以通过用 Docker 安装您的本地实例[来快速完成。一旦你安装了 Appwrite 后端服务器的本地副本，最好的开始方式是阅读我们的](https://github.com/appwrite/appwrite#installation)[入门教程](https://appwrite.io/docs/getting-started-for-web)和我们的 [API 文档](https://appwrite.io/docs)。

# 信用

再次，非常感谢我们所有的社区成员，他们贡献了他们的时间，发送了惊人的请求，报告了错误，并建议了新的功能。

特别感谢成员们，他们贡献了自己的时间，给了我们许多关于他们 0.3 版本体验的反馈和见解。再次非常感谢我们的社区成员 Mostafa Hussein，他花了几天时间来改进我们的 Docker 图像构建阶段。

我们已经有了来自世界各地的惊人的 99 名贡献者。这个新版本的发布是我们在 [Github](https://github.com/appwrite/appwrite) 和 [Discord](https://discord.gg/GSeTUeA) 上收到的社区成员的反馈和帮助的产物。

# 下一步是什么？

加入我们的 [Github 知识库](https://github.com/appwrite/appwrite)和 [Discord 服务器](https://discord.gg/GSeTUeA)来分享你的想法和创意，并使用 Appwrite 寻求支持。

我们将很快发布 0.5 版本的目标。我们希望您能参与构建 Appwrite 的路线图。