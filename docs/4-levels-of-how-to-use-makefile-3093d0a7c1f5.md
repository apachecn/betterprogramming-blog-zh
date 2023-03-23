# 如何使用 Makefile 的 4 个级别

> 原文：<https://betterprogramming.pub/4-levels-of-how-to-use-makefile-3093d0a7c1f5>

## Makefile 快速介绍

![](img/2677369bbf110f115eebbcc0a734ed63.png)

Gabriel Heinzer 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我喜欢 Makefile。今天，我使用 Makefile 来完成我正在做的大部分项目。你可能在 GitHub 上的很多开源项目中看到过`Makefile`([比如这个](https://github.com/scikit-learn/scikit-learn/blob/main/Makefile))。像我一样，您可能想知道 Makefiles 是什么，它们做什么。

有大量令人难以置信的 Makefile 教程。我的目标是让你在五分钟之内开始使用 Makefile。最后，您应该知道足够多的信息，可以开始使用 Makefile 并自学它。

*TL；博士:读取 1 级和 2 级*

# 什么是 Make 和 Makefile

简而言之，`Makefile`是一个特殊的格式文件，它的规则告诉 GNU Make 实用工具(即`make`)如何执行运行在*nix 上的命令。通常，Make 用于编译、构建或安装软件。

虽然 Makefile 通常用于编译 C 或 C++，但它并不局限于任何特定的编程语言。你可以用 Make 做各种东西:

*   执行一系列命令来设置您的开发环境
*   自动化构建
*   运行测试套件
*   部署等。

# 为什么使用 Makefile

编译源代码可能很麻烦，尤其是当您必须包含多个源文件时。

在其核心，`Makefile`是一个实用程序，用于编写和执行一系列命令行指令，如编译代码、测试代码、格式化代码、运行代码等。

它有助于将您的开发工作流程自动化为简单的命令(`make build`、`make test`、`make format`、`make run`)。

另外:

*   `make`预装在您能找到的大多数*nix 系统中
*   编程语言/框架是不可知的吗

好吧。说得够多了，让我们开始真正的交易吧。

# Makefile 快速指南

在浏览下面的关卡时，我强烈建议你创建一个`Makefile`(注意:一定要命名为`Makefile`)并亲自尝试。

## 第一级:“告诉我所有我需要知道的”

在这个级别，您将学习 Makefile 的基础知识；或许你需要知道的就是从中受益。

假设您有一个使用 Docker 的项目。要使用 Docker 容器迭代地构建和运行您的应用程序，您通常需要执行以下操作:

1.  运行`docker build`
2.  确保没有正在运行的容器
3.  运行`docker run`
4.  重复

以下是你自己如何做到这一点:

```
docker build -t image-name . --build-arg ENV_VAR=foo
docker stop container-name || true && docker rm container-name || true
docker run -d -e ANOTHER_VAR=bar--name container-name image-name
```

真麻烦！有很多东西需要记忆和打字。最重要的是，有很多机会犯愚蠢的错误。

当然，您可以在每次进行更改时运行这三个命令。那会有用的。然而，它就是如此低效。相反，我们可以像下面这样写一个`Makefile`:

现在，要构建并运行一个新的 Docker 映像，您只需要一个简单的`make all`命令。在上面的例子中，我们可以调用`make`，因为`all`是第一个规则(注意:默认情况下选择第一个规则)。

总而言之，`Makefile`中的规则通常是这样的:

## **一级外卖:**

1.  `<target>` —通常是文件名，可以是任何名称
2.  `<command>` —通常是用于制作`<target>`的`*nix`命令/步骤。这些必须以制表符开始
3.  `<prerequisite>` —可选。这告诉`make`在命令运行之前，每个先决条件必须存在。因此，先决条件按 1 到 N 的顺序运行(在上面的示例中)。
4.  `@`语法是干什么用的？如果命令行以`@`开始，该命令的回显被抑制([参考](https://www.gnu.org/software/make/manual/html_node/Echoing.html))
5.  仅运行`make`时，默认选择第一个`<target>`

就是这样。简单吧？

现在，在工作中制作一个 Makefile 文件吧！

## 第二级:“酷，但我需要更多”

当涉及到编程的各个方面时，变量替换的使用相当普遍。Makefile 没有被豁免。

那么，如何使用环境变量(带默认)？

假设你想用不同的构建参数`docker build`你的应用程序(例如`ENV_VAR`，这里是你的`Makefile`看起来的样子:

## **二级外卖:**

1.  `.PHONY` —默认情况下`make`假定您的`<target>`是一个文件。因此，如果它们不是，只要用`.PHONY`标记它们，以防你有一个与`<target>`相同的文件名([参考](https://www.gnu.org/software/make/manual/html_node/Phony-Targets.html)和[阅读这个](https://makefiletutorial.com/#phony)以获得更多信息)
2.  用`=`或`:=`语法([引用](https://stackoverflow.com/questions/4879592/whats-the-difference-between-and-in-makefile))声明一个 Makefile 变量
3.  `:=` —执行一次语句
4.  `=` —每次执行一条语句。一个用例是，每次调用函数时都需要一个新的`date`值。
5.  `?=` —仅当 *Makefile 变量*未设置或没有值时设置该变量([引用](https://www.gnu.org/software/make/manual/html_node/Setting.html))
6.  最后，您可以使用一个环境变量来检查一个命令是否存在，如上面的`if`语句所示
7.  注意添加如下的行内注释。您最终可能会给变量增加额外的空间:

```
FOO = /my/path/to # <- a white space!
```

## **奖金**

*   `echo $${ENV_VAR-development}`根据您当前的 shell 环境设置`ENV_VAR` *shell 变量*，默认值为`development`。
*   或者，`make`允许您从命令行传递变量和环境变量，例如`ENV_VAR=development make build`

到目前为止，您可能已经具备了为中小型项目创建 Makefile 所需的一切。

现在，前进，用 Makefile 创造奇迹吧！

## 第三级:“现在，给我看看那些新奇的东西”

这可能是我最喜欢的部分。每个人都喜欢好的`help`消息，对吗？[对不对……](https://knowyourmeme.com/memes/for-the-better-right)

让我们创建一个有用的`help`目标，以防我们的用户需要关于如何在我们的项目中使用 Make 的帮助:

*   `.DEFAULT_GOAL` —还记得我说过当你只运行`make`时，第一个规则是默认选择的吗？你可以用这个覆盖它
*   有了这个，你可以简单地运行`make`来显示每次的帮助信息

然而，每次你添加一个新的目标到你的`Makefile`中，你都需要添加一行新的 echo。

现在，自我记录的 Makefile 帮助消息听起来怎么样？

修改你的`help`目标如下(相应修改):

接下来，添加带有`##`标签的注释，将注释作为`help`消息的一部分打印出来:

嘭！这是一条很好的自我记录的`help`消息:

```
$ makeUsage:
  make <target>Targets:
  help        display this help
  all         run build -> stop -> run
  build       build docker image
  stop        stop running container
  run         run docker container
```

鸣谢:我要感谢 [make help 的作者——对此有很好记录的 Makefiles](https://www.thapaliya.com/en/writings/well-documented-makefiles/) 。

# 第四级:“你的文章没有提供我需要的详细程度！”

这里有一些我整理的 Makefile 参考资料，我强烈推荐你去看看；我应该感谢他们所有人:

*   [用最美味的例子学习 Makefile](https://makefiletutorial.com/)——这是一本非常好的 Makefile 通用教程，我更喜欢它，而不是去阅读 180 多页的手册
*   [使用 Python 制作 Makefile 的技巧](https://blog.mathieu-leplatre.info/tips-for-your-makefile-with-python.html)——如果您正在从事 Python 项目
*   需要一个像样的例子？这里有一个 GitHub 上的 1.8k+ Stars 的[Makefile](https://gist.github.com/isaacs/62a2d1825d04437c6f08)
*   如果其他方法都失败了——GNU Make manual(你可能不需要它的大部分)

# 最后的想法

即使您正在编写好的文档，Makefile 也可能更加可靠。Makefile 的美妙之处在于，它只是一种记录您已经在做的事情的严格方式。

为开源项目做贡献？参考`Makefile`确定开发工作流程。

## Makefile 的当前状态

Makefile 首次出现于 1976 年，众所周知，它的使用有其独特之处。

对 Make 最常见的抱怨是它有相当复杂的语法。结果，你会发现那些绝对喜欢或[讨厌](https://news.ycombinator.com/item?id=7463048) Makefile 的人。

我认为这种效用变得非常个人化。每个人都有自己的最爱。就我个人而言，我对因为许多其他原因而不得不下载另一个花哨的程序/工具/框架/库不太感兴趣。

Makefile“就是管用。”

```
**Want to Connect?**This article was originally published at [jerrynsh.com](https://jerrynsh.com/4-levels-of-how-to-use-makefile/)
```