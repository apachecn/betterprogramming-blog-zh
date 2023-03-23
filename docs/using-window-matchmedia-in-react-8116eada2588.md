# 在 React 中使用 window.matchMedia

> 原文：<https://betterprogramming.pub/using-window-matchmedia-in-react-8116eada2588>

## 剧透:Safari 让它乱七八糟

![](img/190ba89a48b071c1bbfb34b719cf3acd.png)

[索菲](https://unsplash.com/@sophiesollmann?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/collections/1841908/motel?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 为什么要用 window.matchMedia？

通常，我们可以使用一个`css @media`查询来改变内容如何显示给不同屏幕尺寸的用户。然而，有时仅仅使用 CSS 很难达到你想要的效果。也许:

*   布局是如此的不同，以至于你可能要重写整个组件
*   您希望包含一些其他逻辑，比如不同的日志记录或其他内容
*   你脑子都炸了，搞不清楚 CSS。

无论您的理由是什么，使用 JavaScript 检查屏幕大小都是一个有用的工具。

# 步骤 0:设置

让我们来设定场景:

您有一个 React 功能组件`MyReponsiveComponent`。您希望为桌面用户显示一样东西，而为使用较窄设备的用户显示完全不同的东西。你有一个变量`isNarrowScreen`，你用它在两者之间切换，但是现在，它总是被设置为`false`。

# 第一步:初始计算

使用`matchMedia` 最简单的方法是使用一次性的`true` 或`false` 查询。我们调用`window.matchMedia`并传递我们可能用于 CSS 版本的同类[媒体查询字符串](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries)。

这种情况下，不超过 500px 我就说是窄屏，所以我们的查询字符串是`"(max-width: 500px)"`。注意括号——没有它们就不行！

# 第二步:全面响应

假设窗口的宽度保持不变，上面的方法工作得很好。然而，如果我们想响应用户调整窗口大小或旋转设备，我们需要更新`isNarrowScreen`。

我们可以通过给`window.matchMedia`的结果添加一个事件监听器来做到这一点，这将让我们知道何时需要改变我们的布局方法。

我们将使用`useEffect`和`useState`来完成此操作。

需要注意的事项:

*   确保在`useEffect`中返回一个`cleanup` 函数来移除事件监听器。
*   在文档中，您可能会注意到 window.matchMedia 的[结果有一个`addListener`方法，但是建议我们尽可能使用`addEventListener`。我们将暂时使用`addEventListener`，并在下一步处理*不可能*的情况。](https://developer.mozilla.org/en-US/docs/Web/API/MediaQueryList)

# 步骤 3:在旧版本的 Safari 上修复它

所以，以前的版本似乎在 Chrome 上运行得很好，但是在 iPhone 上打开它，什么都没有。空白的一页，完全碎了。

事实证明，尽管 Safari 支持我们的`addEventListener` 用法，但它支持它的时间并不长(在撰写本文时)。所以，让我们用废弃的`addListener`做一些向后兼容。

# 第四步:提取

最后，我们将它设置为在所有这些边缘情况下都能正常工作。它很长，很难看，我们不想在我们的代码库中重复多次。因此，让我们将其提取为一个包装组件。

我们将传递两个不同的版本作为道具，并根据之前的逻辑返回合适的版本。

# 全部完成！

我们可以进一步概括——可能会传递任意数量的不同显示版本以及相应的显示大小列表——但是，这个版本对于我的目的来说已经足够好了，所以我将把它留在这里。