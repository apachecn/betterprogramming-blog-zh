# Go 接口的真实例子

> 原文：<https://betterprogramming.pub/a-real-world-example-of-go-interfaces-98e89b2ddb67>

## 如何实现 Go 接口以及为什么它们如此棒

![](img/b3ed7ec280c708b296c7b29acc417639.png)

照片由 [Sean Lim](https://unsplash.com/@seanlimm?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

假设我正在用 Go 构建一个 web 应用。在我的应用程序中，我想向我的用户发送一条消息。我可以通过电子邮件或短信向用户发送消息。这将是一个完美的接口用例。

对于这个假设的 web 应用程序，我创建了下面的`main.go`文件。

`User`结构代表一个用户。

你可以看到我创建了一个`interface`类型的`UserNotifier`，它只有一个功能:`SendMessage()`。

为了实现这个接口，我必须创建一个实现`SendMessage()`功能的`struct`。

如您所见，我创建了一个新的`EmailNotifier`结构。然后我在 struct 上实现了`SendMessage()`方法。注意，在这个例子中，`EmailNotifier`只是打印一条消息。在现实世界中，你可能会调用电子邮件 API，比如 [Mailgun](https://www.mailgun.com/) 。

就这样，接口现在实现了。

接下来要做的是使用`UserNotifier`接口给用户发送一封电子邮件。

运行该程序时，`EmailNotifier`的`SendMessage()`被正确调用。

```
go build -o main main.go
./main
Welcome Dirk
Sending email to Dirk with content Interfaces all the way!
```

让我们也实现一个 SMS 接口。

一个很酷的特性是我们可以将通知存储在用户结构中。这样，每个用户都会有一个个人通知。

然后，您可以向使用`UserNotifier`接口通知用户的`User`结构添加一个方便的方法。

最后，我在`main()`函数中创建了两个用户，并调用了`notify()`方法。

最终结果如预期般奏效。

```
go build -o main main.go
./main
Sending email to Dirk with content Welcome Email user!
Sending SMS to Justin with content Welcome SMS user!
```

# 结论

这是我展示 Go 接口如何工作，在现实世界中如何有用，同时保持简单的机会。

希望这对某些人有用！