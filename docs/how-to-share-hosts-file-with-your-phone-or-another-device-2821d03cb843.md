# 如何与您的手机或其他设备共享“主机”文件

> 原文：<https://betterprogramming.pub/how-to-share-hosts-file-with-your-phone-or-another-device-2821d03cb843>

## 在 5 分钟内改善您的开发环境

![](img/d22b330aefc6cc5283c87303037501fa.png)

[Yura Fresh](https://unsplash.com/@mr_fresh?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

开发环境需要在`hosts`文件中定制条目，这是一个常见的场景。

假设您的`/etc/hosts`包含多个指向`localhost`的服务，如下所示:

```
127.0.0.1 api.local
127.0.0.1 cms.local
127.0.0.1 web.local
```

多个服务可以在同一个 HTTP 服务器的同一个端口上运行。在这种情况下，服务器将主机名映射到特定的服务(虚拟主机)。您不能仅使用 IP 连接到服务。

假设移动应用程序使用 API `api.local`。您想要在真实的电话设备上测试/开发应用程序，但是在那里无法解析 API 主机。

解决方案之一是使用代理服务器。

# 代理服务系统

(转发)代理服务器充当来自客户端的请求的中介。换句话说，设备(客户端)的网络流量将通过您的计算机(服务器)。

因此，主机映射将可用于设备，因为使用了本地 DNS 解析器。

## 设置

代理服务器必须在您的计算机上运行。

有一个关于 [Squid](http://www.squid-cache.org/) 服务器的例子。然而，它可能是您选择的任何服务器，如 [HAProxy](http://www.haproxy.org/) 、 [Tinyproxy](https://tinyproxy.github.io/) 或 [Traffic Server](https://trafficserver.apache.org/) 。

sSuid 服务器可以用 Docker 快速启动。例如，有一个现成的 [Squid4 图像](https://hub.docker.com/r/babim/squid/):

```
$ docker run --name squid -d \
  --restart=always \
  --publish 3128:3128 babim/squid
```

就是这样。默认情况下，它充当转发代理。

注意:默认情况下，服务器接受来自本地网络的连接。如果要应用附加限制，请调整配置。

## 配置客户端

该设备应与服务器连接到同一个网络。请检查您本地网络的计算机 IP。

然后只需配置网络连接，使用手机上的代理。

**Android 的代理设置**

*   连接到 WI-FI 网络
*   前往设置→ WI-FI
*   长按已连接的网络名称
*   修改网络配置→显示高级选项
*   设置代理设置
*   主机名→您计算机的(代理服务器)IP
*   端口→ 3128
*   不要对→ 127.0.0.1 使用代理服务器
*   救援

**iOS 的代理设置**

参见苹果支持页面[在 iOS 设置助手](https://support.apple.com/en-us/HT202693)中设置高级网络设置

现在，网络流量通过代理服务器。

注意:当代理服务器关闭时，你必须把网络设置改回来。