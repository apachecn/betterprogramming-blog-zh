# 我用 Go 自动化了我的 AWS Lambda 工件

> 原文：<https://betterprogramming.pub/i-automated-my-aws-lambda-artifacts-with-go-847cad8877a4>

## 我在 AWS Lambda 部署中遇到的问题，以及我如何使用基本 CLI 解决这些问题

![](img/14c4cc5c3c083947497cfeac1b85d6ea.png)

蕾妮法语， [CC BY 3.0](https://creativecommons.org/licenses/by/3.0)

我最近对 AWS Lambda 有些兴趣。它很便宜，很容易与其他服务或 API 集成，而且启动和运行起来几乎毫不费力。差不多了。正如我之前在另一篇[文章](https://blog.lorisocchipinti.com/forex-bot-with-lambda-telegram/)中提到的，AWS 控制台中没有 Golang 功能的 web 编辑器选项。相反，有必要构建一个二进制文件，创建一个 zip 存档文件，并将其上传到 WebUI(我猜也可以通过 CLI 上传)。

好的，没问题。起初，我只是像任何体面的家伙一样在终端中输入单独的`go build`和`zip`命令。不幸的是，随着我开始对我的小函数越来越感兴趣，我开始一直打包许多不同的程序。很快，我希望有一些更有条理的东西，所以我创建了这个脚本:

```
#!/bin/bash
filename=lambda-function-$(date +%Y_%m_%d).zip
GOOS=linux go build main.go
zip $filename main
```

我满足了一段时间。

# 设计 Gozip CLI

当然，上面解释的解决方案对于零星的原型开发来说是相当不错的。然而，为了保持实用性，这个代码片段必须与应用程序入口点位于同一个目录中。我必须做大量的复制/粘贴工作，并在文件系统中来回移动。我逐渐厌倦了这种工作方式:我想要手中有可塑性的粘土，但我只有一个僵硬的代码片段。

在这一点上，我意识到我需要一个方便的新 CLI 实用程序，尽管我知道我不应该走得太远，花整个周末来设计一些过于花哨的东西。首先，我很快收集了对 CLI 的一些要求:

*   它应该构建一个 Go 程序，然后将其打包成 zip 存档
*   应该是用 Go 写的(对，一直往下走！)
*   它应该允许在文件系统中的任何地方选择目标目录
*   它应该允许定制一些修订标签来区分不同的可执行版本
*   它应该具有合理的缺省值，这样就可以在不指定任何参数的情况下执行

根据以上几点，我需要两个主要功能:访问命令行参数和与 shell 交互。幸运的是，Go 的标准库已经涵盖了这一点。

# 在工作台上

一般来说，在 Go 中通过调用`os.Args`可以轻松完成处理命令行参数的任务。通过这种方式，可以返回表示命令行参数的字符串标记列表(包括正在执行的程序的名称)并使用它们。然而，每一个像样的 CLI 可以修补其选项，并改变默认行为。例如，我想用这样的命令从任何目录运行我的实用程序:

```
$ gozip --target path/to/main.go --revision v1_3_7
```

通过输入`gozip`命令敲击一次应该也是可能的，所以没有选项应该是强制性的。当然，从头开始管理选项和缺省值是可能的，但是 Go 标准库提供了更实用的东西:`flag`包。顾名思义，`flag`允许您轻松地配置命令行标志。代码如下所示:

很简单，不是吗？`flag`的另一个很酷的特性是它自动为 CLI 生成有用的文档:

```
$ gozip -h Usage of gozip: -name string name of the executable aws-lambda handler (default "main") -output string The desired path to store the zip archive. If not explicitly set, gozip tries to create a sub-directory at the target location -revision string a version number for the executable. If not explicitly set, gozip uses the current datetime -target string path to the Go program (default ".")
```

既然我已经有了捕捉和处理输入的方法，打包 Go 程序就变成了正确检查选项参数是否是有效的文件路径的问题(这是`os.Stat()`的工作)，并与命令行交互以构建& zip 程序。如上所述，我不想被拖入重新创建我的 zip 实用程序的兔子洞——尽管它本身可能是一个有趣的练习——我想继续利用 Go 的本机功能。我们的朋友`os/exec`迅速赶来救援:

```
exec.Command("go", "build", "main.go")
```

这样，`gozip`就充当了一个终端包装器，所以我可以直接向 shell 提交命令，而不需要从头开始重新构建任何东西。

# 将这些点连接起来

所有不同的部分都准备好了，所以此时将它们放在一起很简单:

可能有许多有用的扩展可以用来改进`gozip`，比如连接 Docker 注册中心或 S3 桶，增加对其他语言的支持，甚至开发与 AWS Lambda 的端到端集成。就目前而言，我对我所拥有的感到满意，考虑到它只花了很少的时间来开发，并且肯定会在将来避免一些令人头痛的问题。

# 结论

在本文中，我谈到了我在 AWS Lambda 部署中遇到的问题，以及我如何利用 Golang 标准库中的`flag`和`os/exec`包的基本 CLI 来解决这些问题。虽然额外改进和扩展会使我的工具更有价值，但我觉得我在提高效率和开发工作之间取得了很好的折衷。

你有最喜欢的实用工具吗？大家讨论一下吧！

```
**Want to Connect?**

[Join my newsletter for more stories like this](https://blog.lorisocchipinti.com).
```