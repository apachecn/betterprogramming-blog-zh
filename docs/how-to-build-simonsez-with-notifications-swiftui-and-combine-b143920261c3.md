# 如何用通知、SwiftUI 和组合构建 SimonSez 第 1 部分

> 原文：<https://betterprogramming.pub/how-to-build-simonsez-with-notifications-swiftui-and-combine-b143920261c3>

## 如何构建一个简单的网络游戏的第 1 部分

![](img/5ae8e15a119ccee3b0ec1f203700aa31.png)

照片由 [S X R I P T X](https://unsplash.com/@sxriptx?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄。

我最讨厌的事情之一是阅读一篇基于 iOS Y 和 Swift X 的文章，但读到一半时却发现它已经过时了。所以预先警告:这是写在 iOS 13 和 Swift 5 上的。

在我过去一两周写的一系列文章中，我讲述了如何通过苹果接收，更重要的是发布远程通知。我使用了三种主要类型:提醒、徽章和声音。我顺便提到了 iOS 12 中引入的一个新功能(临时通知)。

事实上，临时通知实际上根本不是一种新类型。它们是一种无需实际请求即可请求允许发送通知的方式。这个想法很简单:在你做任何事情之前，不要问你是否可以发送通知，你可以要求他们是可接收的。iOS 批准了它，并默默地交付它们。这个想法是你以后会要求完全的许可。

让我们根据西蒙说的话(有时称为简单的西蒙说的话)构建一个游戏来尝试一下。我将使用临时通知。严格来说，这不是它们的目的，但我在这里很有创意。

创建一个项目，将其命名为 SimonSez，并将这段代码添加到`appDelegate`中。将通知推送到目标的签名&功能下的功能。您还需要添加 CloudKit，因为我们也将使用它——尽管比 WotsApp 中要少得多。

这将配置临时通知，这意味着您可以发送通知，但他们不会被看到。它仍然会向控制台报告一个十六进制注册密钥，您需要注意。

我们的计划是永远不要求发送提醒、徽章或声音作为通知。我们只是希望有一种方式可以无声地互相传递信息。这里有一个警告:不要被这种想法冲昏了头脑。我敢肯定，您可以向某人发送的静默通知的数量是有限的。由于你只能用你的 Apple ID 为所有通知创建两个推送 ID，你应该谨慎使用。

回到计划上来。我想创建一个简单的按钮板。你按顺序按下它们，然后挑战另一个玩家重复这个顺序，比如红色、红色—蓝色—绿色、绿色、绿色—黄色。

要开始游戏，我们需要共享我在启动时创建的通知 ID 令牌(您注意到的那个)。我们通过 CloudKit 实现在另一个项目中这样做了，但是这次我不想使用完整的 CloudKit。对于一个简单的游戏来说，这感觉有点过了，而且你可能已经通过阅读其他系列发现了，这是一个很大的工作量。让我们保持简单，使用键值存储。毕竟，我们只是在分享一个游戏的十六进制令牌。我开始做一些你永远不应该做的事情:在一个我称为`Common.swift`的文件中定义几个全局变量:

```
var coder: String!
var token: String!
var quest: String = ""
```

接下来，让我们快速构建使用键值存储来共享代码的方法。将这些助手方法添加到我们称为`Storage.swift`的新类中:

在这段代码中，我们通过订阅密钥更改来初始化该类。这触发了一个方法来刷新我们界面上的选择器列表(稍后将会显示)以显示新的键。我们还创建了一个方法来做同样的事情，因为订阅有时可能有点棘手。

其他方法有内务处理、保存密钥、检索密钥和删除密钥。接下来，创建第二个类，将其命名为`Crypto.swift`，并将该方法输入其中:

这将生成我们的随机密钥，以便其他玩家可以在我们加入游戏时找到我们。我们使用一周中的某一天来帮助我们维护表。我们的想法是只在表中保存今天的键。我们干脆放弃其他所有的日子。注意:这并不适用于全球。显然，澳大利亚现在是星期一，远早于美国的星期一。作为一种增强，我们应该给我们的键添加一个时区。

接下来，我们需要为远程通知编写代码。理论上，我们可以简单地在表视图中显示十六进制键，但是它们有 64 个字符长，在现实生活中完全不切实际。我们将它们与我们生成的随机代码配对。

我已经在 WotsApp 的文章中介绍了这方面的内容，但是我想我应该在这里重温一下。

若要通过 Apple 使用发布通知，您需要创建一个证书。我们首先登录您的 Apple 帐户，获取一个私钥，您将使用该私钥作为发布通知请求的一部分。打开 web 浏览器并登录您的 Apple Developer 帐户。在“概述”页面上，您将看到我们正在寻找的名为“证书、标识符和配置文件”的选项卡。

![](img/a5f5bab222f589cdef5c1f537ce2d03d.png)![](img/65648df2aa5f780fc5ec06f319100906.png)

单击它，然后单击左侧选项卡中名为“Keys”的项目。在那里，你可以点击蓝色圆圈中的白色加号。您应该会发现自己看到的配置屏幕看起来很像这样。请记住，您只能创建两个 APNS 类型的密钥，所以要明智地使用它们。

![](img/61dc82ddd778e78320ab310b1287c7d8.png)

点击注册，稍等片刻。注册后，请特别注意将出现在页面上的密钥 ID。你以后会需要它的。

![](img/f725b23b27e5800be65bddf875a6f2fd.png)

好，现在下载你的签名。它将包含在扩展名为. p8 的文件中。标准 OS X 上的应用程序都不理解什么是. p8 文件，你将无法正常打开它。前往“下载”,右键单击该文件，然后用“文本编辑”应用程序打开它。不，不推荐使用它来打开. p8 文件，但它可以工作。

其内容应该如下所示:

```
-----BEGIN PRIVATE KEY-----
MIGTAgVAMBMGByqGSM49AgEGCCqGSM49AwEHBHkwdwIBAQQgl8Kij2y6acAgp1FZ
BHqI6T/Bv4bBgndxuVr1IfuYhemgBgYIKoZIzj0DAQehRANCAASweAt5jGR5H1Vf
QmlPyVVa2hn8jPLxdg0wHyP/xpXbJ5kGunlkXomLh8k+d31tWKKQF2QTzPCzckyi
p0aHWAWG
-----END PRIVATE KEY-----
```

这是您将用来加密发送给 Apple 的信息的秘密私钥。回到编码。重新打开 SimonSez 项目，创建另一个名为`RemoteNotifications.swift`的类。将密钥文件的内容添加到其中:

是的，那是三段引文。你需要所有的东西。现在，我认为一些读者可能会发现最可怕的部分:我们需要一个第三方库来做这件事。我们要找的库是 IBM 写的。谷歌“swift jwt 库”点击[链接](https://github.com/IBM-Swift/Swift-JWT)，你会在 GitHub 中看到这个库:

![](img/2362bc4003e446ca3e4a7c81a6875627.png)

复制 URL 并移回 Xcode 项目。点击蓝色项目图标，然后点击 Swift Packages 选项卡。当它下载各种软件包时，您应该会看到几个这样的窗口。

![](img/a723203714663693b174bf048f3a7fb1.png)

下载完成后，您只需点击完成。

![](img/659af66a4428a935989e3962d11d5240.png)

假设一切正常，您的项目现在应该包含了`SwiftJWT`库。回到`RemoteNotifications`类并导入你的新库。编译它只是为了检查一切工作正常。您应该不会收到任何错误。别担心，那是最伤脑筋的部分。现在应该是一帆风顺了…嗯，差不多了。

下一步是创建一个我们可以发布到苹果通知服务器的有效负载。JSON 有效负载。这是给你的项目的。没什么特别的。这是一个普通的 JSON 通知对象。尽管我应该补充一点，它不需要提醒、徽章或声音。它只是发布一些数据——一个 SimonSez 数据包。JSON 中有一个特殊的标签`“content-available”:1`告诉它这一点。

```
let jsonObject: [String: Any] = ["aps":["content-available":1],"SimonSez":"1,2,2,3,3,3,4"]
```

现在为了确保你没有在这里打错任何东西，把这段代码添加到你的`RemoteNotification`类中:

```
func postNotification(postTo: String, jsonObject:[String: Any]) {
let valid = JSONSerialization.isValidJSONObject(jsonObject)print("valid ",valid)
if !valid {return}
```

快速前进，您现在已经准备好使用 IBM 库了。通过将这段代码添加到您的`RemoteNotification.swift` `postNotification`方法中，定义组成 JSON web 令牌的三个部分中的两个:

```
let myHeader = Header(typ: "JWT", kid: "DNU7997F")
let myClaims = ClaimsStandardJWT(iss: "*", sub: nil, aud: nil, exp: nil, nbf: nil, iat: Date() , jti: nil)
let myJWT = JWT(header: myHeader, claims: myClaims)
```

这里有两个字段需要与所示代码不同的值。`kid:`是您的私钥的密钥 ID，我们前面已经描述过了。而`iss:`属性是你的团队 ID，你应该可以在苹果账户会员页面下找到。

![](img/8c8d5fdd9fab51c81c02ac2eae849874.png)

定义了前两部分之后，现在需要使用您的私钥来构建第三部分。这是您的 JSON web 令牌。

```
let privateKeyAsData = privateKey.data(using: .utf8)
let signer = JWTSigner.es256(privateKey: privateKeyAsData!)
let jwtEncoder = JWTEncoder(jwtSigner: signer)
```

这个谜题的最后一步是打电话给苹果公司的 APNS 服务部。一些附加说明:

*   代码中显示的 URL 是针对沙箱的。你需要用一个稍微不同的来制作。
*   这里提到的令牌是我们向 Apple 注册设备时获取的 64 个字符的字符串。它被打印到控制台上。
*   `apns-topic`是项目的包标识符，`jwtString`是我们刚刚构建的 JSON web 令牌(在我们的例子中是`ch.cqd.SimonSez`):

我在这里添加了一个额外的例程来帮助您理解从服务器返回的任何 HTTP 响应。只有一件事要做:添加`RemoteNotifications`类的`URLSessionDelegateto`；

```
class PostController: NSObject,URLSessionDelegate {
```

但是等等，在我们继续讨论 SwiftUI 之前，我们还需要担心一些方法。让我们停下来看看我们想要发送的 JSON 消息。我们的计划是将此作为背景信息发送。我说过这不是警报。是数据。

```
private var jsonObject: [String: Any] = ["aps":["content-available":1],"SimonSez":"1,2,2,3,3,3,4"]
```

为了实现这一点，我们需要为我们的项目添加一个额外的新功能。点击蓝色项目图标，添加背景模式作为一项功能，并勾选远程通知。

![](img/bd561f169db2a739975717202b9f4b9f.png)

现在回到你的`appDelegate.swift`，把这个方法加进去。它将捕获您发送的通知:

SimonSez 数据包是一个简单的数字串，代表您按下的按钮，您将要求您的同事回忆起来。

我们也需要让与你同事的联系变得更容易，所以让我们准备第二个数据包，以便在你们中的一方看到另一方时确认/交换详细信息:

```
let jsonObject: [String: Any] = ["aps":["content-available":1],"token":token!,"code":self.code]
```

这个包包含几个变量，这些变量将包含您的随机代码和要共享的令牌。它也是一个数据包。这一个的计划是当另一方收到时产生一个警报，这样你们两个都知道你们在同一页上。这也是给他们你的地址并决定谁先走的好方法！

我们需要另一种类型的数据包—一种交换位置的数据包—以便在甲方挑战乙方之后，乙方可以挑战甲方:

```
let jsonObject: [String: Any] = ["aps":["content-available":1],"Toogle":true]
```

现在，假设您从未离开过`AppDelegate.swift`并且刚刚发布了`didReceiveNotification`方法，您将处于编辑和添加一些对这些数据包的解释的有利位置。修改后的方法应该是这样的。注意:严格来说，我不需要 guard 声明，但是把它放进去是个好习惯，因为没有它你会通不过 App Store 的审查！

它是做什么的？根据哪个包被发送出去，它触发不同的组合`PassThroughSubjects`，这里有一个稍微复杂一点的方法——`execAfterDelay`，这是一个递归方法，用于发送有延迟的`PassThroughSubjects`。它用于将刚刚按下的按钮序列回复给其他玩家:

# 结论

继续阅读[第二部分](https://medium.com/better-programming/how-to-build-simonsez-with-notifications-swiftui-and-combine-part-2-de717b1c53d5)，看看所有这些方法是如何一起创建我们的简单游戏的。