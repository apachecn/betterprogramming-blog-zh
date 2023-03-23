# 在 Swift 中验证文本字段的最佳方式

> 原文：<https://betterprogramming.pub/the-best-way-to-validate-textfields-in-swift-18083ff95090>

## 在 Swift 中验证文本字段的最佳和最简单的方法之一(入门级)

![](img/524513204f55aa4716022bd84b4c1948.png)

照片由[努贝尔森·费尔南德斯](https://unsplash.com/@nublson?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/developer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我注意到许多关于在 Swift 中验证`TextField`的问题——最好的方法是什么，以及如何管理。这些问题中的许多都是相关的，尤其是当你有多个`TextField`的时候，代码的大小可能会增加一倍或两倍。

有许多方法可以做到这一点，但我将向您展示我发现的在 Swift 4 和更高版本中对我有效的最佳方法。

# 我们开始吧

首先，创建一个这样的函数:

```
@objc func textFieldDidChange(_ textField: UITextField) {
    //Here we will write some code, bear with me!
}
```

每次在特定的`TextField`中发现一些变化时，都会调用这个函数。您可以将此功能设置为任意多的`TextField`。
我应该如何设置？

很简单。首先，让我们假设你有四个`TextField`:

```
@IBOutlet weak var name1: UITextField!@IBOutlet weak var name2: UITextField!@IBOutlet weak var name3: UITextField!@IBOutlet weak var name4: UITextField!
```

然后创建一个函数:

我们刚刚做的是为每个`TextField`设置我们之前创建的函数`textFieldDidChange(_:)`。参数`for:`表示在什么情况下我们应该调用这个函数。因此，我们将其设置为`UIControl.Event.editingChanged`，这意味着每次在特定的文本字段上发现变化时。

请在`viewDidLoad()`中调用这个函数:

```
override func viewDidLoad() {
    super.viewDidLoad()

    setupTextFields() //You call the function here.
}
```

好了，设置完毕。

# 确认

设置好一切之后，我们只需要创建验证。

验证`TextField`有一个非常简单的方法，只需根据`TextField`为每个验证添加 guard 语句。但是如果你有多个`TextField`并且每个`TextField`有多个验证，那么它可能是一个充满代码的页面。

因此，我们可以通过创建`UITextField`的扩展来最小化代码，并很好地管理它。然后，在其中创建一个用于验证`TextField`的函数。该函数应该返回一个布尔值，如下所示:

这只是一个自定义验证；您可以根据需要创建验证。验证从参数 word 中获取一个字符串，然后检查 in `TextField`中是否包含该单词。如果`TextField`不为空，并且可以在`TextField`中找到该单词，那么该函数将返回 true。否则，它将返回 false。

注意:这个确切的函数可用于查找您的应用程序中接受的特定单词或拒绝特定单词。它可以在不同的庞大系统中找到，这些系统过滤被拒绝的单词。

太好了，我们到终点了。你还记得当我们创造`textFieldDidChange(_:)`的时候，我告诉你要忍耐吗？现在是时候用一些代码填充它了，如下所示:

所以，你创建一个安全声明，我们将使用你需要的所有`TextField`。如果我们不创建这个函数，想象一下我们会有多少重复代码。更不用说我们是否会创建更多的验证。

因此，如果所有这些`TextField`返回 false，将打印以下内容:

```
print(“Text Fields are not validated, disable everything! ❌”)
```

你可以在这里放一些代码来禁用任何一种按钮或者你可能有的任何东西。因此，如果根据您的要求无效，那么按钮将被禁用，用户无法继续流程，也就是说，他们无法进入下一个`View`。否则，它将启用按钮或任何你想要的。

我希望这篇文章对你有帮助。如果你想了解更多关于组织代码的知识，请查看这篇文章。