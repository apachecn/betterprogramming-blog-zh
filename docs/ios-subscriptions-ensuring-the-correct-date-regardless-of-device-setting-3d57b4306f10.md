# iOS 订阅—无论设备设置如何，都确保日期正确

> 原文：<https://betterprogramming.pub/ios-subscriptions-ensuring-the-correct-date-regardless-of-device-setting-3d57b4306f10>

## 确定 iOS 设备上的本地日期是正确的

![](img/31c030f8a5fb1f9cd7df63e342f2bab6.png)

由[大卫·格兰穆金](https://unsplash.com/@davidgrdm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

如今，我们都在花越来越多的时间在线使用网飞、迪士尼、亚马逊和苹果电视等依靠订阅的应用。

您的客户正在启动一项与这些服务非常相似的服务，您需要确保客户设备上的日期设置正确。

现在所有的 iOS 设备都使用一种叫做 NTP 的协议，这种协议与苹果公司的服务器池 time.apple.com 同步。它的功能是记录时间。我怀疑苹果以某种方式使用它来检查当你试图下载应用程序时日期是否同步。日期设置不正确将有效地禁止下载。

不幸的是，这不是一个你可以通过任何 iOS 官方框架进行的检查。更糟糕的是，用户可以关闭整个时间/日期同步配置，改为手动设置日期。

当有人运行你的应用程序时，你需要采取不同的路线来确保日期是好的。你有什么选择？

# 选项 A

你可以通过 GPS 使用卫星信号来获取日期。但是这需要几秒钟的时间，而且读完堆栈溢出的帖子，我怀疑这是不可靠的，所以这可能不是最好的方法。

```
#import CoreLocation
let gps = CLLocation()
print("gps time ", gps.timestamp)
```

# 选项 B

您可以使用`UserDefaults`来记录他们第一次运行应用程序的日期。坚持记录和监控:如果日期早于最后一次记录的日期，那么一定有问题。大部分时间你会掩护自己，尽管如果他们认真的话，他们仍然可以作弊。这里有一些让你入门的基本代码。

```
let defaults = UserDefaults.standard
defaults.set(Date(), forKey: "lastRun")
let lastRun = defaults.object(forKey: "lastRun") as? Date
```

# C1 选项

您可以编写自己的 NTP 守护进程来自己获取时间。我能看到的唯一缺点是所需代码的复杂性。你可以通过使用一个库来抵消这种复杂性。例如，谷歌一下，你会找到 TrueTime 和克罗诺斯。使用第三方库来解决严重的安全威胁确实有点疯狂，但是，除此之外，如果标准 NTP 不使用任何类型的认证/验证，精通技术的用户可能会找到一种方法来绕过它。

# C2 选项

你可以使用一种新的协议， [NTS](https://developers.cloudflare.com/time-services/nts#:~:text=Network%20Time%20Security%20(NTS)%20provides,is%20divided%20into%20two-phases) ，它运行在 NTP 之上，与 Cloudflare 这样的提供商对话以获取时间。但是你知道，代码的复杂性，加上你所依赖的协议还没有完全达成一致，留下了相当多的遗留问题。实现它也将是一个挑战，因为还没有第三方库可以利用。

# D1 选项

您可以使用自定义 API 来维护自己的服务器，以便在出现提示时返回日期。它会工作，尽管一个非常精通技术的用户可能能够逆向工程它；在这个组合中添加一些加密技术，你应该是安全的。但是等等——即使假设你的客户已经有了一个服务器场来为订阅者提供内容，让他们在上面运行你的定制代码也不会有好结果。那是一个困难的销售。

# D2 选项

同样，一个定制的解决方案，但一个继续在苹果上使用的解决方案，是一个使用独立实现的解决方案。这里是代码的基本框架。若要使用它，您的用户需要登录 iCloud。显示的代码更新了私有 iCloud 数据库中的一条记录，然后查看元数据，就像进行更新时一样。记录的更新日期将是 iCloud 服务器上的日期，所以你再次依赖于苹果基础设施，也就是苹果时间。您也可以在这个解决方案中添加加密，使其真正做到防炸弹。

这个初始片段只是检查以确保 iCloud 可用。

```
init() {
  if isiCloudContainerAvailable() {
    fetchRecord(recordString: "2B441E11-CD16-BD15-98AA-1F54439205F3")
  } else {
    cloudBon = "iCloud Unavailable"
  }
}
```

使用上述内容的代码是打开 iCloud 数据库、更新数据库和报告元数据内容所需的基本代码。

唯一可能让你头疼的问题是本地 iCloud 空间是否已满。

继续编码，保持冷静。