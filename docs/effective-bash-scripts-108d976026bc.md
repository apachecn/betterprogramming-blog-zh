# 编写 Bash 脚本的有效方法

> 原文：<https://betterprogramming.pub/effective-bash-scripts-108d976026bc>

## 了解如何使用 Bashy 库实现更好的输入解析和脚本管理

![](img/2f912809457ea8cdeefd75d8bf3fac22.png)

由[马库斯·温克勒](https://unsplash.com/@markuswinkler?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

如今，大多数工具和脚本引擎都有一些非常重要的特性，可以节省我们大量的时间。在我的日常工作中，我最欣赏这些脚本:

*   参数解析和帮助支持:能够自我记录正在启动的命令，枚举所有参数
*   脚本共享:与他人共享您创建的脚本的机会。

这两个特征都是通过现代语言实现的。试想一下，在 node，。net 或 PHP，您有一个包的存储库，您可以下载包管理器安装在您的本地的可执行文件。通过这种方式，内容可以用来与使用公共或私有存储库的其他人共享您创建的程序。

所有这些语言都提供了(直接或使用易于集成的库)自由构建关于控制台命令和选项列表的信息的方法。此外，该解决方案还解决了参数解析问题。但是巴什怎么办？

在本文中，我们将发现用 Go 编写的工具 [Bashy](https://github.com/zeppaman/bashy) ，它旨在通过授权 BASH 脚本使用声明性清单和脚本共享来解析参数，从而解决所有这些问题。

让我们看看！

![](img/72b5c0d0a58540209a54becfabf17f9a.png)

Gabriel Heinzer 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# Bash 清单和参数解析

我敢肯定，我们大多数人都喜欢这样一个事实:如果你在一个命令后输入`--help`(例如`mytool --help`)，你会得到所有的选项和参数。那么，你们中有多少人喜欢写实现它的代码呢？在最高级的语言(C#、GO、PHP)中，有这样做的库和帮助器，但是在 bash 中，最常见的选择是编写如下代码:

如您所见，这段代码不容易维护，每次添加参数时都必须更新。我们的生活会随着巴希而改变！

这与使用 Bashy 是一样的(参数被自动解析):

```
echo "FILE EXTENSION  = ${EXTENSION}"
echo "SEARCH PATH     = ${SEARCHPATH}"
echo "DEFAULT         = ${DEFAULT}"
echo "Number files in SEARCH PATH with EXTENSION:" $(ls -1 "${SEARCHPATH}"/*."${EXTENSION}" | wc -l)
```

怎么可能呢？Bashy 为我们的脚本使用了一种描述性格式。因此，我们将能够通过添加 YAML 配置文件的一些行来解释脚本的参数。因此，解释器将知道参数是如何产生的，并且您将得到填充了正确值的变量。更容易，对吗？

在下面的代码片段中，您将找到显示输入数据的简单脚本的 YAML 描述。您可以将 bash 脚本嵌入清单或链接到外部文件。在这个例子中，为了简洁起见，我使用了一个嵌入式的，如下所示:

现在我们已经看到事情是如何变得更容易的，让我们开始创建我们的第一个 bash 脚本。

# 如何创建你的第一个 Bashy 脚本

正如我们在上一节中介绍的，Bashy 脚本只不过是一个带有相关清单的常规 BASH 脚本。Bashy 解释器通过用 YAML 清单中的信息解析参数来包装脚本，将值提供给脚本，并将它们作为常规变量注入。

这创造了奇迹！

对于简短的脚本，您可以将 bash 代码直接嵌入 YAML 文件中。当然，较长的脚本可以作为外部独立文件保存。使用这些，您将添加文件的路径(它也可以是允许从互联网下载的 URL)。

在本文中，我将始终使用嵌入式版本来简化阅读，避免在教程的每个步骤中使用多个代码片段。

第一步是在中创建一个 YAML 文件，您可以填充所有元数据，如下面的代码片段所示:

```
name: Name
description: the command description
argsusage: help text
```

该命令的名称将是用于调用脚本的名称。同时，`description`和`arg`用法将用于显示帮助文本。描述命令的另一个元素是参数列表。我们可以通过配置元素`params`来添加它:

```
params:
- name: "name"
  desc: "enter your name"
- name: "surname"
  desc: "enter your surname"
```

现在，我们可以添加脚本命令。它可以逐行添加，也可以链接到外部脚本。所有的方法都可以共存，并且会按顺序执行。在这种情况下，我们使用的是嵌入式版本:

```
cmd: echo "commmand executing with $name and $surname" 
```

然后，您可以通过使用变量来获取输入值。每个输入都有一个变量，其名称由您在清单中选择。在我们的例子中，它们是`$name`和`$surname`。

您可以通过将它添加到存储库中来测试它:

```
bashy repo add ./samples/bash.yml
```

现在，脚本可用了，您可以通过键入以下命令来检查它:

```
# basky Name
NAME:
   main Name -USAGE:
   main Name [command options] [arguments...]DESCRIPTION:
   the command descriptionOPTIONS:
   --name value     enter your name
   --surname value  enter your surname
   --help, -h       show help (default: false)
```

脚本的执行只需要:

```
# basky Name --name myname --surname mysurnamecommmand executing with myname and mysurname
```

然后，如果您已经将 bashy 的 bin 文件夹包含到路径文件中，您可以直接调用该命令，因为它是一个物理脚本:

```
Name --name myname --surname mysurname # No "bahy" command herecommmand executing with myname and mysurname
```

就是这样。现在你的第一个脚本已经完成，看看如何与他人分享。

# 部署和使用脚本

像最常见的软件包管理器一样，Bashy 可以在您的本地。因为它是对内部用户估计的(bash 脚本主要由技术人员使用)，所以它被认为是尽可能平滑的。

事实上，Bashy 下载并安装任何 YAML 文件——本地或通过 HTTP。这意味着您的团队可以通过将文件发布到共享文件夹或任何 HTTP 存储库(网站或 artefact 存储库)来共享文件。这一部分非常好，因为它不强迫你安装和采用(也许还要付费)任何第三方工具。

将脚本添加到本地存储库的命令是:

```
# bashy repo add [https://raw.githubusercontent.com/zeppaman/bashy/master/samples/bash.yml](https://raw.githubusercontent.com/zeppaman/bashy/master/samples/bash.yml)List of the script added:
/root/.bashy/scripts/Name.yml
/root/.bashy/scripts/SecondCommand.yml
/root/.bashy/scripts/RemoteCommand.yml
```

现在命令可用了，您可以使用脚本了！

# 结论

在本文中，我们看到了如何使用 Bashy 来简化 BASH 脚本的使用。这个工具增强了 Bash 脚本，使其符合最新的标准，并且节省了我们大量的时间。换句话说，它帮助我们以同样的努力提高更高效的脚本的质量和交付。

所以，你只需要测试它😃。

喜欢这篇文章吗？成为 [*中等会员*](https://daniele-fontani.medium.com/membership) *继续无限制学习。如果你使用下面的链接，我会收到你的一部分会员费，不需要你额外付费。*

# 参考

*   GitHub 上的回购[https://github.com/zeppaman/bashy](https://github.com/zeppaman/bashy)