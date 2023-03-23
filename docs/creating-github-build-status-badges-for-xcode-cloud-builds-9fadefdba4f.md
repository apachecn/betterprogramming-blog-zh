# 为 Xcode 云构建创建 GitHub 构建状态徽章

> 原文：<https://betterprogramming.pub/creating-github-build-status-badges-for-xcode-cloud-builds-9fadefdba4f>

## Xcode Cloud 目前没有状态徽章。我们能造一个吗？

![](img/5225f752322bac757ab2e513768e2f4b.png)

我们都喜欢美好的事物。这是来我们回购的人首先会看到的东西。这很可能是一件让潜在贡献者远离你的项目的事情。

一个好的`README.md`给出了项目的详细描述，如何贡献代码，可能是安装/构建指南，以及如何创建问题。一个*优秀的* `README.md`会突出项目的状态，展示吸引人和令人兴奋的截图，只是一般会让读者对项目感到兴奋。

这就是身份徽章的用武之地。虽然状态徽章本身并不能让你的项目变得伟大，但是它们可以让你的整体感觉更加完美。显示您项目的 CI 构建/测试状态显示了对您工作的信心。如果你写了糟糕的测试用例，并且你的构建失败了，除非你在按下“构建”之前单脚站立并旋转三圈，否则你不会想要向全世界宣传这一点！

# 问题是

有许多 CI/CD 服务为这些建筑徽章提供服务。还有那些不常提供第三方选项的(见: [shields.io](https://shields.io/) )。由于 Xcode Cloud 太新了，嗯，苹果，还没有提供构建状态徽章的服务。在这种情况下，我该如何发挥我的编程能力呢？

# 解决方案

我们正踏入未知的领域，所以这次 StackOverflow 帮不了我们。

我开始寻找一种在构建完成后从构建机器与外界通信的方法。我当然可以使用一个[定制构建脚本](https://developer.apple.com/documentation/xcode/writing-custom-build-scripts)来完成这项工作，该脚本会向服务器发送一条消息，其中包含构建状态和我通过了多少测试的报告。

不过，这可能有点过头了。在进一步挖掘了[文档](https://developer.apple.com/documentation/xcode/xcode-cloud)之后，我发现了 webhooks。基本上，当一个工作流结束运行时，Xcode Cloud 会向我选择的服务器发送一个`POST`请求，其中包含一个关于构建进展的详细报告。

我不会把它贴在这里，但是如果你看一下 [webhooks 文档](https://developer.apple.com/documentation/xcode/configuring-webhooks-in-xcode-cloud)，苹果公司很大方地提供了一个请求体的例子。“太棒了”，我惊叹道，独自一人在我的客厅里。

# 规划

实施将包含一(1)个部分。HTTP 网络服务器。显然，在构建网络服务器时，有很多选择。我选择使用 [Deno](https://deno.land/) 有很多原因，最重要的是 [Deno Deploy](https://deno.com/) 。我是一个破产的大学生，有一个即将出生的孩子，所以现在支付任何类型的云托管都不是一个好的选择。随着 Heroku 的免费等级的消失，我必须找到一个替代方案。Deno 有一个很棒的免费等级，对我来说已经足够了。

除了 Deno Deploy，Deno 还是一个令人惊叹的 Javascript/Typescript 框架，它提供了我需要的所有工具。它与 node.js 非常相似(事实上，它是由构建 node 的[同一个家伙](https://en.wikipedia.org/wiki/Ryan_Dahl)开发的)，只是它更加现代和安全。此外，它还提供一流的类型脚本支持。

够了 Deno 炒作，我要怎么建立这个东西？

服务器需要接收`POST`请求，解析请求体并以某种方式做出响应。之后，它需要有一个端点，我们可以将它传递给我们的`README.md`,它会根据我们从 webhook 收到的构建状态生成一个状态标记。

# 实施

第一部分在 Deno 中非常简单:

我喜欢在 TypeScript 中使用类型，所以我继续使用 TypeScript 接口对响应进行建模。你可以在这里查看。

代码简单地监听`/builder`端点上的请求，并相应地解析数据。我设置了一些全局属性，它们会根据请求数据而改变。在`parseWorkflow`方法内部，我循环遍历`BuildAction`(T5、`BUILD`或`TEST`中的一个)并根据请求中得到的值更新全局变量。

使用 Deno Deploy，这种方法应该可以很好地工作，但是如果您正在像 Heroku 的自由层那样工作的地方进行部署，您将希望将这些值写入某个地方的持久存储中。事实上，最终我自己也可能会这么做。你可以在 [GitHub 回购](https://github.com/TheNightmanCodeth/XcoudCloudStatusBadge)上跟上我的进度。

接下来，我们需要设置徽章端点。为了简单起见，我将使用 [shields.io](https://shields.io) 来生成它们。这是通过向 shields.io 链接传递一些值来实现的，如下所示:

```
https://shields.io/badge/<label>-<message>-<color>
```

显示通过测试数量的绿色测试标记的 URL 可能如下所示:

```
[https://shields.io/badge/Tests-4%20Passing-green](https://shields.io/badge/Tests-4%20Passing-green)
```

我们可以通过向`requestHandler`添加以下代码来创建端点:

就这样，我们可以将我们的服务器部署到一个托管服务，并将`/builder`端点设置为 App Store Connect 上的 webhook。

一旦所有东西都部署好了，webhook 也设置好了，我们就可以像这样将徽章包含在我们的`README.md`中:

```
![Build status badge](https://ourserver.com/badges/build-status)
```

# 结论

就这样，我们做到了，宝贝。

尽管它并不完美。我真的希望将值[存储在 postgres 数据库](https://deno.com/deploy/docs/tutorial-postgres)中，以确保如果我们的 runner 重新启动或中断，状态不会被无意中更改。GitHub 在`README` s 中处理缓存资产的方式似乎也有些疯狂。总的来说，这是为 Xcode Cloud 实现状态徽章的一个很好的方式。希望有一天我可以创建一个服务，允许用户注册并创建自己的徽章。这肯定是我能做的事，只是目前我负担不起主办费用。用户将生成自己的端点，可以从 Xcode Cloud webhooks 接收消息，他们不必部署自己的服务器来完成这一任务。构建状态将保存在数据库中，当 Xcode pings 服务器时，它会在数据库中找到用户，更新他们的构建设置，徽章端点将为每个用户适当地呈现徽章。

不要忘记查看并启动 [GitHub repo](https://github.com/TheNightmanCodeth/XcoudCloudStatusBadge) 以了解完整的实现，并跟上未来的更新！

# 2012 年 9 月 8 日更新

在过去的一天左右，我已经疯了，试图让徽章与 GitHub 一起工作。GitHub 上的这个讨论强调了我在缓存方面面临的问题。您可以看到，为了让我部署的服务器正常工作，我已经提交了几次。我试过在响应头中设置`Cache-Control`、`ETag`和`Expires`，但都没有用。GitHub 的`camo`媒体缓存似乎并不尊重传递给它的头值。他们的[文档](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/about-anonymized-urls)说简单地编辑`Cache-Control`标题就可以解决这个问题。并没有。我已经设置并验证了响应头，尝试了各种组合，但似乎都不起作用。没有任何意义。

然而，GitLab 的 README 渲染器工作得非常完美。每次在服务器上更新徽章时，它也会在 GitLab 上的 README 上更新。显然，我更喜欢这些徽章在 GitHub 上工作。只是看起来没办法让 GitHub 尊重我的`Cache-Control`。