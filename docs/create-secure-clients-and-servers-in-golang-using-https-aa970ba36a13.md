# 使用 HTTPS 在 Golang 中创建安全的客户端和服务器

> 原文：<https://betterprogramming.pub/create-secure-clients-and-servers-in-golang-using-https-aa970ba36a13>

## 在 Go 中编写安全的 web 应用程序并了解细节

![](img/d0d712f9c2a4f205494cd912929035b3.png)

照片由 [Unsplash](https://unsplash.com/@yogesh_7?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的 [Yogesh Pedamkar](https://unsplash.com/@yogesh_7?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

# 概观

这篇文章的目的是展示如何使用 [Go](https://golang.org/) 和 HTTPS 编写安全的 web 服务和客户端。在研究如何做到这一点时，我看到了大量的文章和要点。然而，它们都没有提供我实现健壮的客户机或服务器所需的完整画面。他们中的大多数只提供了简洁的代码示例，以及同样简洁的关于如何创建代码工作所需的证书的示例。其他面向 gRPC 或 TCP 上的普通 TLS。我还想了解我在做什么，而不仅仅是语法。这篇文章的重点是不仅提供*如何*而且提供*如何*背后的*为什么* s。

如果您已经熟悉 HTTPS、TLS 和公钥/私钥，您可以跳过下一节**安全概述**，直接进入**创建证书和密钥**。

# 安全性概述

安全性对于确保客户的隐私和健康至关重要。他们希望确保他们提供的信息(如密码和信用卡号)会被预期的服务提供商获得。同样，服务提供商有时有必要确保他们正在与预期的客户沟通。

安全通信有两个特征:信任和加密。

信任是安全的基础。没有信任，就无法保证对话双方都不是坏人。

需要加密来确保不良行为者无法偷听对话、获取敏感信息或执行有害操作。

为了确保信任和安全，必须满足以下要求:

*   必须有可信的权威机构来保证客户端或服务器的身份。
*   客户端和服务器可以用来加密其通信的加密技术的可信来源也必须存在。
*   代码中必须支持用于实现客户端和服务器应用程序的相关技术。

所有这些东西的保护伞被称为[公钥基础设施(PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) 。PKI 有四个实现上述要求的组件:

## 公钥加密

公钥可以由所有者安全地与另一方共享，并为另一方提供加密和解密会话的能力。公钥的所有者有一个不共享的相应私钥。公钥只能解密用相应的私钥加密的信息。同样，需要私钥来解密用公钥加密的信息。加密的这种非对称特征保证了信息可以在已知的对话方之间安全地共享。

## 证书

证书包含持有者的身份以及证书所有者的公钥。证书还包括颁发证书的可信机构的信息。

## 证书颁发机构(CA)

证书颁发机构是身份信息的可信来源。他们还可以颁发证书或将该权限委托给注册机构。

## 注册机构

注册机构是证书的可信来源。由 RA 颁发的任何证书也将包含授权它们颁发证书的 CA 的证书。这是确保满足信任要求所必需的。换句话说，ra 本身之所以被信任，是因为它们可以证明自己受到 CA 的信任。

# 创建证书和密钥

证明身份和加密信息的基础是证书和相应的证书颁发机构。出于本文的目的，我们两者都需要。有两种方法可以获得这些，艰难的方法和简单的方法。硬方法适用于真实世界的应用程序。它包括注册一个域(例如，youngkin.com)，获得该域的 DNS 服务，以及获得该域的证书。虽然不难，但需要做大量的工作，你可能需要花一些钱来注册域名。

提供真实体验的一种更简单的方法是创建您自己的 CA 并从该 CA 获得证书。本文使用 CA 签名证书和自签名证书来创建更真实的体验。我发现创建一个 CA，请求证书，并让 CA 签署这些证书有助于理解整个过程。

通常，服务器访问安装在机器上的 CA 证书。本文将演示如何以编程方式注册 CA 证书。

至少有几个工具可以帮助我们实现简单的方法， [certstrap](https://github.com/square/certstrap) 和 [Easy-RSA](https://github.com/OpenVPN/easy-rsa) 。

使用证书的权威命令行工具叫做`openssl`。它可以用来创建 ca、RAs 和证书，以及做许多其他有用的事情(更多细节请参见下面的参考资料部分)。它还可以用来创建所谓的*自签名证书*。[创建一个自签名 SSL 证书](https://linuxize.com/post/creating-a-self-signed-ssl-certificate/)是一个很好的资源，可以教你如何做这件事。虽然它面向 Linux，但在 Windows 和 Mac 机器上也有不同版本的`openssl`。我推荐安装`openssl`的自由叉。这背后的原因在[堆栈交换问题/答案](https://security.stackexchange.com/questions/112545/what-are-the-main-advantages-of-using-libressl-in-favor-of-openssl)中有很好的描述。要验证您是否有 Libre `openssl`，请运行以下命令:

`$ openssl version
LibreSSL 2.8.3`

类似上面的输出表明 Libre `openssl`正在被使用。

在证书生成过程中将会创建几个文件，其中一些带有`.crt`和`.key`就足够了。在阅读有关证书的内容时，您可能还会看到后缀`.pem`。值得注意的是，`.pem`文件相当于`.crt`和`.key`文件。PEM 是一种文件格式。`.crt`和`.key`是关于文件包含什么(证书和密钥)的提示，但是这些文件都使用 PEM 格式。请参见[堆栈溢出讨论](https://stackoverflow.com/questions/62823792/how-to-get-crt-and-key-from-cert-pem-and-key-pem)了解更多详情。

# 安装软件以创建 CA 和证书

如上所述，至少有两种方法可以轻松创建 CA 和 CA 的注册证书。本文使用 certstrap 的原因仅仅是因为它是用 Go 编写的。然而，Easy-Rsa 是一个很好的选择。

开始之前，从 GitHub 上的 [certstrap releases](https://github.com/square/certstrap/releases) 页面下载适当的可执行文件。我把我的放在我的路径下的目录中。您还需要使它可执行(`chmod +x <downloadedfilename>`)。

我们将遵循项目自述文件中的[使用说明](https://github.com/square/certstrap)。在本节结束时，我们将创建一个 CA、一个服务器证书和一个客户端证书。我将所有证书保存在一个名为`~/certs`的目录中。以下所有命令都将从该目录运行。当提示输入密码短语时，只需按 enter 键(即没有密码短语)。

# 创建 CA

```
~/certs certstrap init --common-name "ExampleCA"         
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Created out/ExampleCA.key
Created out/ExampleCA.crt
Created out/ExampleCA.crl
```

`init`指示 certstrap 创建新的 CA。`--common-name` (CN)为我们的 CA 指定名称，命名为 *ExampleCA* 。创建了三个文件:

*   `ExampleCA.key`是*示例 CA* 的私钥
*   `ExampleCA.crt`是*例 CA* 的证明
*   `ExampleCA.crl`是该 CA 的证书吊销列表(CRL)。它包含由相关 CA 颁发的已吊销证书的列表。

一个*通用名*或 CN 通常是与证书相关联的主机的完全合格的域名(FQDN)。

有关证书撤销列表的信息，请参见[该来源](https://jamielinux.com/docs/openssl-certificate-authority/certificate-revocation-lists.html)。

# 为客户端和服务器创建证书

```
~/certs certstrap request-cert --domain  "localhost"       
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Created out/localhost.key
Created out/localhost.csr~/certs certstrap request-cert --domain  "client"
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Created out/client.key
Created out/client.csr
```

`localhost`用作服务器的域，因为如上所述，服务器需要主机的有效 FQDN。`localhost`足以达到这个目的。除了证书签名请求(CSR)和证书本身中的 CN 之外，使用`--domain`标志将创建一个*主题替代名称* (SAN)。San 是指定许多服务寻址方法的当前标准，包括通过 FQDN 或域名。

还有一个`--domain`的替代品，那就是`--common-name`。我们使用`--common-name`来创建上面的 CA。`--common-name`只会生成一个 CN。从 Go 1.15 开始，证书必须包含 SAN 条目，否则 https 请求将失败。只有 CN 的证书将不被接受。如果使用 Go 1.15 或更高版本，并且使用`--common-name`来生成 CSR，您可能会从客户端看到以下错误:

```
Get "https://localhost": x509: certificate relies on legacy Common Name field, use SANs or temporarily enable Common Name matching with GODEBUG=x509ignoreCN=0
```

如错误消息中所述，这个问题可以通过在客户端命令前面加上`GODEBUG=x509ignoreCN=0`来解决。

# 签署客户端和服务器的证书

```
~/certs certstrap sign localhost --CA ExampleCA  
Created out/localhost.crt from out/localhost.csr signed by out/ExampleCA.key~/certs certstrap sign client --CA ExampleCA   
Created out/client.crt from out/client.csr signed by out/ExampleCA.key
```

请注意，所有证书和相关密钥都放在了`./out`目录中。

证书必须由可信机构(在本例中为 CA)签名才能生效。签名是 CA 对证书所有者身份的保证。上面的`--CA`标志指示 certstrap 让我们的`ExampleCA`签署证书。

此时，我们有了 CA、客户机和服务器的证书和密钥。

# 编写客户端和服务器

本文中的所有代码都可以在 GitHub 的我的 [gohttps](https://github.com/youngkin/gohttps) 资源库中获得。

我们将编写一个简单的服务器和一个更高级的服务器，前者不验证客户机的证书，后者在验证客户机的证书时有多种选择。最后，我们将开发一个可以与两台服务器对话的客户机。

在继续之前，我们需要简要讨论一下 HTTPS 是如何实现的。HTTPS 流量由 TLS 层加密。TLS 是 SSL 的后继者，工作在 TCP/IP 之上。它做了许多事情，包括:

*   协商 TLS 会话。这包括协商要使用的 TLS 版本和加密套件。
*   验证服务器的身份
*   如果需要，验证客户端的身份
*   处理所有流量加密

Go 的 HTTP 包包括一个 TLS 配置结构，用于实现客户机和服务器的 HTTPS 通信期望。这将是以下小节的重点。

TLS 需要可靠的传输机制。TCP 和 UDP 是两个选择，但是 UDP 不靠谱。参见[为什么 TLS 需要 TCP](https://security.stackexchange.com/questions/170833/why-does-tls-require-tcp) 了解更多关于这个主题的讨论。

# 简单的服务器

简单服务器的完整实现见 [GitHub](https://github.com/youngkin/gohttps/blob/master/simpleserver/server.go) 。

客户端和服务器之间最简单的 HTTPS 交互是客户端验证服务器的凭证，所有流量都被加密。客户端只需要访问签署服务器证书的 CA 的证书。服务器既不知道也不关心客户端的身份。这是一个非常常见的用例。

下面是一个非常简单的 HTTPS 服务器的实现的分解。首先要做的是创建和配置`http.Server`结构:

`Addr`简单地指定服务器的监听地址。`ReadTimeout`和`WriteTimeout`分别设置读取和写入的超时。如注释所示，`ReadTimeout`设置为五分钟，以便有时间输入机器的用户密码。当使用`curl`并且证书有密码保护时，OSx 有时会提示这一点。在本文中，没有一个证书受密码保护。

严格地说，像这样的简单服务器不需要超时字段。也就是说，健壮的服务器将包含它们。更多详细信息，请参见[完整的 Go net/http 超时指南](https://blog.cloudflare.com/the-complete-guide-to-golang-net-http-timeouts/)。

在本文的上下文中，`TLSConfig`是`Server`结构中最有趣的字段。这是配置所有 TLS 选项的地方。在这种情况下，只需要`ServerName`。`ServerName`必须与服务器证书中的主机名匹配。

服务器需要一个处理函数:

这个处理函数没有太多内容；它只提供普通的“你好，世界！”回应。

最后一步是服务器开始监听请求:

HTTPS 服务器使用`ListenAndServeTLS()`，而不是 HTTP 服务器中的`ListenAndServe()`调用。`*serverCert`和`*srvKey`分别是服务器的证书和私钥文件。包含这些文件的文件名在命令行中传递(下面将详细介绍)。您可能还记得，`localhost.crt`和`localhost.key`是我们为服务器创建的证书和密钥文件。

# 更先进的服务器

参见 [GitHub](https://github.com/youngkin/gohttps/blob/master/advserver/server.go) 了解这个更先进的服务器的完整实现。

上面的简单服务器和更安全的服务器之间的主要区别是增加了要求或要求和验证客户端证书的功能。

和以前一样，需要一个`http.Server`结构:

注意，在这个版本中，`TLSConfig`字段是由函数`getTLSConfig()`配置的。这个`TLSConfig`比简单的服务器案例要多一点。下面是该函数的代码:

让我们把它分成几部分。首先，我们来看看函数签名。

论据如下:

1.  `host` —这是服务器的主机名。它必须与主机证书中提供的名称相匹配。在我们的例子中，这是 SAN。
2.  `caCertFile` —这是签署客户端证书的 CA 的证书文件名，在本例中为`ExampleCA.crt`。此服务器中需要 CA 的证书，因为我们创建了一个未知的 CA，即不是通常在操作系统中配置的 CA(例如，OSx 中的 KeyChain)。所以这里需要补充一下。
3.  `certopt` —可以看到，这是`tls.ClientAuthType`类型。有五种授权类型用于授权/验证客户端证书:

*   `tls.NoClientCert` —不要求也不需要客户端证书。这是默认值。
*   `tls.RequestClientCert` —将请求客户端证书，但这不是必需的，也不会被验证。
*   `tls.RequireAnyClientCert` —需要客户端证书，但任何有效的客户端证书都是可以接受的。它不会根据 CA 的证书进行验证。
*   `tls.VerifyClientCertIfGiven` —不会请求客户端证书，但如果存在，将根据 CA 的证书进行验证。
*   `tls.RequireAndVerifyClientCert` —将需要客户端证书，并将根据 CA 的证书进行验证。

现在我们知道了函数的参数是什么，让我们来看看函数体:

*   第 3 行—我们定义了一个`x509.CertPool`。这是一个将在下面使用的证书池。它将包含签署客户端证书的 CA 的证书。
*   第 4 行—我们检查`certOp`的值。任何大于`tls.RequestClientCert`的值都需要客户提供证书。
*   第 5–8 行—为了验证客户端证书，需要将 CA 证书加载到`caCertPool`中。这些行读取 CA 证书文件并处理任何错误。
*   第 9 -10 行—我们创建一个新的`x509.CertPool`并将 CA 的证书添加到池中。
*   第 15 行—`ClientAuth`字段用于指定所需的客户端证书授权和验证的级别。数值和定义已在上文关于`certOpt`的讨论中给出。
*   第 16 行—`ClientCAs`字段用于指定将用于验证客户端证书的 ca。它的值是从第 9 行和第 10 行创建的`caCertPool`中设置的。该字段的 GoDoc 包含以下内容:"`ClientCA` s 定义了根证书授权机构的集合，如果`ClientAuth`中的策略要求验证客户端证书，服务器将使用该集合。"
*   第 17 行— `MinVersion`设置与客户协商版本时可接受的最低 TLS 版本。低于 1.2 的 TLS 版本被认为是不安全的。详见 [RFC 7525](https://www.rfc-editor.org/rfc/rfc7525.txt) 。

# 创建客户端

该客户端的完整实现见 [GitHub](https://github.com/youngkin/gohttps/blob/master/client/client.go) 。

如前所述，该客户端可以成功地与简单服务器或高级服务器进行通信。让我们来看看重要的代码。

首先要做的是配置客户端的证书和密钥，如果有的话。

如果提供了，代码将从客户端的证书和私钥创建一个`x509`密钥对。此密钥对将在协商 TLS 连接时使用，并用于加密和解密客户端和服务器之间的通信。

接下来，与高级服务器一样，我们将创建一个证书池，其中包含签名的 CA 的证书，在本例中是服务器的证书。

然后，我们用客户机的证书/密钥对和包含 CA 的证书池创建一个`http.Transport`。这个`http.Transport`将用于配置`http.Client`。

`http.Transport`包含一个`tls.Config`。与服务器一样，`Certificates`字段由客户端的证书填充。这里有一个新字段，即`RootCAs`字段。GoDoc 对该字段描述如下:"`RootCAs`定义了客户端在验证服务器证书时使用的一组根证书颁发机构。如果`RootCAs`为`nil`，TLS 使用主机的根 CA 集。”

这是准备客户机与 HTTPS 服务器交互所需要做的一切。剩下的代码准备和发送请求，并处理响应。

# 把所有的放在一起

如果您已经克隆或派生了 [gohttps](https://github.com/youngkin/gohttps) 存储库，您会注意到它具有以下目录结构:

```
gohttps+
       |
       +- advserver
       +- client
       +- simpleserver
```

客户机和每个服务器的源文件都在各自的目录中。程序可以从这些目录中构建和运行。

下面命令行中引用的证书和密钥与上面的**创建证书和密钥**部分中生成的名称相匹配。

# 启动服务器

## 简单的服务器命令行(没有客户端验证)

```
./simpleserver -host "localhost" -srvcert "/path/to/localhost.crt" -srvkey "/path/to/localhost.key"
```

## 具有完全客户端证书验证的高级服务器命令行(`-certopt 4`)。

```
./advserver -host "localhost" -srvcert "/path/to/localhost.crt" -cacert "/path/to/ExampleCA.crt" -srvkey "/path/to/localhost.key" -port 443 -certopt 4
```

# 运行客户端

## 使用`curl`

`curl`无客户端证书验证的请求:

```
curl -vi -d "World" --cacert /path/to/ExampleCA.crt [https://localhost](https://localhost)
```

`curl`使用完全客户端证书验证的请求:

```
curl -d "World" -vi --cert /path/to/client.crt  --key /path/to/client.key --cacert ./out/ExampleCA.crt [https://localhost](https://localhost)
```

## 使用客户端程序

服务器进行完全客户端证书验证的命令行:

```
./client -clientcert "/path/to/client.crt" -clientkey "/path/to/client.key" -cacert "/path/to/ExampleCA.crt"
```

服务器不验证客户端证书的命令行:

```
./client -cacert "/path/to/ExampleCA.crt"
```

如上面创建证书一节所述，如果客户端和服务器证书不是用`--domain`标志创建的，您可能会看到以下错误:

```
Get "https://localhost": x509: certificate relies on legacy Common Name field, use SANs or temporarily enable Common Name matching with GODEBUG=x509ignoreCN=0
```

在前面的命令前加上`GODEBUG=x509ignoreCN=0`将会解决这个错误。有关如何避免此问题的更多详细信息，请参见创建证书一节。

# 结论

感谢跟随。我们在本文中讨论了很多内容，包括:

*   如果您没有 PKI 和证书方面的背景，希望您已经学到了足够的知识，可以开始一段旅程来学习更多关于这个主题的知识。
*   对可用于处理证书和密钥的不同软件工具的简要描述。对于本文，我们使用了 certstrap。
*   如何使用 certstrap 创建 CA 以及客户端和服务器证书签名请求、证书和密钥
*   如何在 Go 中编写一个简单而更高级的 HTTPS 服务器，详细介绍细节
*   如何在 Go 中编写 HTTPS 客户端，再次详细讨论细节
*   运行服务器程序，并使用`curl`和客户端程序访问正在运行的进程

您可以随意使用 [gohttps 资源库](https://github.com/youngkin/gohttps)中的代码进行自己的学习、实验，或者作为创建自己的客户机和服务器的基础。

# 参考

*   [TLS 连接选项](https://github.com/jcbsmpsn/golang-https-example) GitHub 项目是快速创建工作的 HTTPS 客户端和服务器的好资源。当我第一次开始创建 HTTPS 客户机和服务器时，它是我关于这个主题的主要信息来源。它还涵盖了一些常见问题的解决方案。
*   [使用 TLS/SSL 保护 gRPC](https://bbengfort.github.io/programmer/2017/03/03/secure-grpc.html)—虽然面向 gRPC，但基本的 TLS 基础与 HTTPS 相同。本文填补了关于如何在 Go 中配置 TLS 的一些空白。
*   从 HTTP 转换到 HTTPS 的完整指南提供了关于 HTTPS 背后的技术的几乎所有方面的详细、实用的讨论，如何请求证书，以及如何配置各种 web 服务器来支持 HTTPS。至少值得快速浏览一下，看看是否有什么有趣的东西。
*   [公钥基础设施(PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) 是提供安全通信基础的技术和可信组织的混合体。
*   [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) 是一种加密协议，用于保护互联网上的通信安全。
*   [HTTPS](https://en.wikipedia.org/wiki/HTTPS) 是 HTTP 协议的安全实现。
*   [OpenSSL Certificate Authority](https://jamielinux.com/docs/openssl-certificate-authority/index.html)是一个关于使用`openssl`创建 ca 和证书的好资源。
*   [创建自签名 SSL 证书](https://linuxize.com/post/creating-a-self-signed-ssl-certificate/)是创建自签名证书的好资源。这些在本文中没有用到，但是知道这样做是可能的，这很好。
*   [Certstrap GitHub 回购](https://github.com/square/certstrap)
*   [Easy-RSA GitHub 回购](https://github.com/OpenVPN/easy-rsa)
*   [21 个 OpenSSL 示例帮助你在现实世界中](https://geekflare.com/openssl-commands-certificates/)是一个很好的参考，包含常用的`openssl`命令。
*   [QualSys SSL 服务器测试](https://www.ssllabs.com/ssltest/index.html)是一个网页，您可以使用它来测试 HTTPS 对 HTTPS 服务器的访问，并获得对其实施最佳实践的评估。
*   Idrix 有一个很好的测试 SSL 证书的服务。例如:

`curl -vi –cert ./ClientCert.crt –key ./ClientCert.key [https://prod.idrix.eu/secure/](https://prod.idrix.eu/secure/)`