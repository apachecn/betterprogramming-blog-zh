# 我如何构建健壮、可伸缩的 Go 应用程序

> 原文：<https://betterprogramming.pub/how-i-build-robust-scalable-go-applications-192a0df32d1>

## 我如何使用接口来保持我的 Go 应用程序模块化

![](img/1b34754ea5a6384e570ae13f873b1e4a.png)

构建经得起时间考验的代码——照片由 [Cederic X](https://unsplash.com/@cedericx?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

假设客户要求您在 [Go](https://golang.org/) 中创建一个后端应用程序。在这个场景中，客户端只有一个请求:他希望能够存储和查询用户。

在本文中，我将展示如何建立这样一个项目。

# 仓库

在 Go 中，我通常为资源创建存储库。存储库负责保存和检索资源。在这种情况下，资源将是一个用户对象。

我创建了一个`userrepo`包和一个文件`api.go`。API 文件将保存所有导出的函数。

这样，就有了一个定义其他包如何与`userrepo`包交互的中心位置。

我定义了`UserRepository`接口。目前，我们只有两个简单的方法，一个用于存储用户，一个用于查找用户。

# 履行

我已经定义了一个接口，现在是时候实现这个接口了。让我们从一个`mockUserRepo`实现开始。

顾名思义，这个除了*模仿*的所有功能之外什么也不会做。

我在`userrepo`包中创建了一个文件`mock.go`。

`mockUserRepo`实现了`UserRepository`接口，对于每个函数，它只打印一条消息。

让我们回到`api.go`文件，添加一个`New()`函数。这将返回一个`UserRepository`接口的实现。

# 为什么要使用接口？

因为我们已经定义了一个`UserRepository`接口，所以我们可以有多个实现。

很久以前，当我刚开始软件开发时，我总是在看界面时挠头，心想:“那又怎样，我只需要一个实现。”

对于小型的个人项目来说，这可能是真的，但是在更大的真实世界的应用程序中，您通常有多个实现。

例如，您可以添加一个连接到 Google 云数据库的`cloudUserRepository`。

在生产环境中使用它是有意义的。但是，在您的本地环境中，添加 SQLite 数据库可能会更容易。

通过创建一个实现`UserRepository`接口的`sqliteUserRepository`,您可以轻松地完成这个任务，而不必重写很大一部分代码。

# 一个例子

好的，我将展示一个例子来让你相信界面真的很棒。

让我们在`userrepo`包中创建一个`sqlite.go`文件。这个文件将包含`UserRepository`包的 SQLite 实现。

类似地，让我们在`userrepo`包中创建一个`cloud.go`文件，它将包含 Google Cloud 实现。

注意，现在我只是打印一条调试消息，实现实际的接口超出了本文的范围。

接下来，我更新了`api.go`文件的`New()`函数。

我们将环境传递给`New()`函数，它将基于这个环境返回`UserRepository`的正确实现——太棒了！

# 将这一切结合在一起

让我们创建主程序。

在新的包`main`中，我创建了一个`run.go`文件。

这里，我使用`userRepo.New()`函数来获得基于环境的`UserRepository`的实现。

如果我需要在一个环境中交换用户库，我只需要改变`userrepo.New()`函数，这太棒了。

让我们通过运行以下命令来测试它:

```
go run main/run.go
```

它输出以下内容:

```
cloudUserRepo: mocking the StoreUser func
```

因此，创建了`UserRepository`接口的正确实现。

这是一个我经常使用的设计模式，并且被证明非常有用。希望你和我一样发现这个模式很棒！

你可以在 [GitHub 上找到这个库。](https://github.com/Dirk94/go-interface-example)