# 使用 Etcd 高效组织您的应用配置

> 原文：<https://betterprogramming.pub/organize-your-application-configuration-efficiently-with-etcd-89dad3fa3dcf>

## 洞察最强大的 etcd 功能

![](img/c47a85a6a68697ea75a52cd875ac61bf.png)

由[法尔扎德·纳兹菲](https://unsplash.com/@euwars?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

有时，我们的应用程序需要来自不同来源的配置数据，比如数据库连接属性、用户名和密码。维护和监控关键数据的变化变得非常困难。

[etcd](https://etcd.io/) 为复杂和简单的应用程序提供了强大的解决方案。[根据 IBM](https://www.ibm.com/cloud/learn/etcd) 的说法，它是“一个开源的分布式键值存储，用于保存和管理分布式系统保持运行所需的关键信息。”您可以将您的应用程序配置存储在一个地方，它可以被分布式系统或机器集群访问。

# 为什么选择 etcd？

*   它使用起来很简单:您可以使用标准的 HTTP/JSON 工具，比如 curl，向 etcd 读取或写入数据。
*   它是可靠的:它可以处理硬件和网络故障。如果您有一个群集，每个节点都是复制的，并且具有完全的数据访问权限。
*   它有强大的功能:您可以观察键值对或目录的修改，并对更改做出反应。

# 功能概述

让我们来看看一些最重要的功能。我们将使用 [etcd-java 客户端](https://github.com/IBM/etcd-java)用 Kotlin 编写代码。

## 入门指南

最快的入门方式是使用 Docker 映像。

1.  创建一个`docker-compose.yml`文件，并放入以下配置:

注意 gRPC 网关端点。etcd v3.4 使用`[CLIENT-URL]/v3/*`同时保留`[CLIENT-URL]/v3beta/*`。有关入门的更多信息，请查看[开发指南](https://github.com/etcd-io/etcd/blob/master/Documentation/dev-guide/api_grpc_gateway.md)。

2.通过执行以下命令启动 etcd:

```
docker-compose up -d
```

3.现在，让我们通过检查 etcd 版本来测试连接:

```
curl -L http://127.0.0.1:2379/version
```

您应该会看到这样的响应:

```
{"etcdserver":"3.4.13","etcdcluster":"3.4.0"}
```

## 放和拿钥匙

您可以使用 curl 将键值对移植到 etcd，但是在本教程中，我们将使用 Kotlin 来演示这些特性。

1.  打开 IDE，用 Kotlin 库创建一个新的 Gradle 项目。
2.  将以下内容粘贴到您的`build.gradle`文件中:

请注意，我们使用的是 com.ibm.etcd 客户端库。IBM 的实现是健壮的，特别是在租约和观察抽象方面。

3.创建一个名为`Main.kt`的类。

4.让我们在`main`函数中创建 etcd 连接，并输入一些值:

5.运行应用程序来测试是否插入了键值对。

你的控制台中的结果应该是`value`。

请注意，etcd 使用 Base64 编码。如果使用 curl 输入键值，就需要编写编码字符串。在这个例子中，键值对是`foo-bar`:

```
curl -L http://localhost:2379/v3/kv/put \
  -X POST -d '{"key": "Zm9v", "value": "YmFy"}'
```

## 手表钥匙

如果您想对键值修改做出反应，Watch 特性会很方便。

例如，假设您有一个有状态的服务，并希望在运行时将日志级别从`debug`更新到`info`，而无需重启应用程序。

1.  创建一个观察器来检查`watch`事件:

2.像我们之前做的那样放置一个键-值对:

3.我们将模拟一些工作。为了简单起见，触发一个`Thread.sleep`方法几秒钟。

4.最后，关闭手表。

您可以观察您的所有键或特定键。我们只想观察`loglevel` 键。

5.启动应用程序，当它等待时，执行 curl POST 请求来更新`loglevel` 键的值。在这个例子中，我将把`debug` 更新为`info`。我已经使用 Base64 算法对字符串进行了编码。

```
curl -L http:*//localhost:2379/v3/kv/put \* -X POST -d '{"key": "bG9nbGV2ZWw=", "value": "aW5mbw=="}'
```

您应该会在 IDE 控制台中看到一个更新事件:

```
watch event: WatchUpdate[rev=1129,events=[Event[type=PUT,key=loglevel,modRev=1129,prevKey=]]]
```

# 结论

我们已经探索了一些最强大的 etcd 功能。我希望你能把这些知识应用到你的项目中。

感谢您的阅读和快乐编码！

# 参考

1.  [https://github . com/etcd-io/etcd/blob/master/Documentation/dev-guide/API _ grpc _ gateway . MD](https://github.com/etcd-io/etcd/blob/master/Documentation/dev-guide/api_grpc_gateway.md)
2.  [https://etcd.io/](https://etcd.io/)
3.  [https://github.com/IBM/etcd-java](https://github.com/IBM/etcd-java)