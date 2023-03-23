# 可选链接的成本

> 原文：<https://betterprogramming.pub/the-costs-of-optional-chaining-eb21f357d6e2>

## 可选链接有一些隐藏的成本

![](img/4436763a62f537f25d83d253eed745fb.png)

[Artem Sapegin](https://unsplash.com/@sapegin?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

可选链接已经到了第三阶段，是时候重新评估了。

一年多前，我们决定开始使用`@babel/plugin-proposal-optional-chaining`。和往常一样，babel 插件的主要原因是开发者体验:“它会让我们的生活变得更容易”。

确实如此。现在依然如此。我看到它在我们的代码库中到处被使用。

在反应中`componentDidUpdate`:

在渲染函数中:

它看起来很好，很容易理解是怎么回事。

然而，这是以牺牲性能和包大小为代价的。而我们，或者至少我，严重低估了这个成本。

# 表演

让我们把性能放在一边，因为这不是我最关心的。

如果可选链接被过度使用，性能代价是存在的。不要保护你所有的财产，只保护未知的东西。如果你正在处理你自己的代码，假设存在是安全的。

也就是说，我们没有每秒 6500 万次迭代我们自己的渲染函数。因此，即使性能损失高达 45%，在生产环境中仍然可以忽略不计。

我们继续吧。

# 捆绑大小

上面贴的`CommentButtons`组件包含了`244`字节的写好的代码，被[转换](https://babeljs.io/repl#?babili=false&browsers=&build=&builtIns=false&spec=false&loose=false&code_lz=GYVwdgxgLglg9mABAYTgWzQUzFAQiKKBAZwAoBvREYzAJ0QF8BKRcgKEUVsyhFqVIdOiADwATGADcAfEOGj8hBIgnEAhgCMANpjEBectToB-AHQQ1YM7phQG0m1BEB6RUTCz5nEW-WrNOvqGNLRmFlamYpg6UJj2UTGYLr4ecsI-BO4qMOraugZGoeaWZtwADloAnvblVcmZCJ7pzhIyQkxsDEA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=true&timeTravel=false&sourceType=module&lineWrap=true&presets=es2015%2Creact%2Cstage-2&prettier=true&targets=&version=7.5.5&externalPlugins=%40babel%2Fplugin-proposal-optional-chaining%407.2.0)成`1.000`字节。大了四倍。

因为这是我们自己的代码，所以我们可以放心地假设，每当`user`道具不是`undefined`时，它也具有`can`属性。如果后端不能强制执行，那么前端、父组件或者我们调用 API 的地方就可以强制执行。

无论如何，我们可以通过重写组件移除`optional chaining`来将传输的字节大小减少到`477`字节。这里我们甚至没有假设`can`的存在，而是默认为一个空对象。

我意识到这是一个极端的例子。但是我在野外看到了与此非常相似的代码。我们开发人员只是喜欢我们的生产力工具。如果有一个巴别塔插件可以让事情变得更简单，为什么不使用它呢？

我并不是说完全不使用可选的链接。我仍然喜欢使用它。我请你记住这是有代价的。例如，尽量不要在一个方法中对同一属性使用两次回退:

我们可以通过只检查一次`user.can`属性来轻松减少[和](https://babeljs.io/repl#?babili=false&browsers=&build=&builtIns=false&spec=false&loose=false&code_lz=G4QwTgBAxiB2CiATAlgFwgXggVwM4FMwB-AOhllPxVQG4AoUScgEXwBt9V9McDiy4pRO074aQA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=true&timeTravel=false&sourceType=module&lineWrap=true&presets=es2015%2Creact%2Cstage-2&prettier=true&targets=&version=7.5.5&externalPlugins=%40babel%2Fplugin-proposal-optional-chaining%407.2.0):

除非您的第一个可选操作符嵌套在某个地方，否则采取最后一步[可能是值得的，并且完全避免可选操作符:](https://babeljs.io/repl#?babili=false&browsers=&build=&builtIns=false&spec=false&loose=false&code_lz=G4QwTgBAxiB2EF4IFcDOBTSAyLKNgDoZ4AfEiAbwF8BuAKFEmIFEATASwBdFo4D0Oneo16wAIugA26Tuh7ECrKTPQ0gA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=true&timeTravel=false&sourceType=module&lineWrap=true&presets=es2015%2Creact%2Cstage-2&prettier=true&targets=&version=7.5.5&externalPlugins=%40babel%2Fplugin-proposal-optional-chaining%407.2.0)

我希望这能说明我的观点。我确实意识到 gzip 可以消除一些开销，因为它非常擅长压缩像`=== void 0`和`=== null`这样的重复模式。但是即使有了 gzip，可选链接的成本也是存在的。请记住这一点，因为我们将在相当长的一段时间内无法使用通天塔。即使现在是第三阶段，它也不会在短期内登陆我们需要支持的所有浏览器。

我将继续使用可选链接…尽管不那么狂热！