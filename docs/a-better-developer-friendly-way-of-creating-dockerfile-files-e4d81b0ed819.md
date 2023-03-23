# 一种更好、对开发人员更友好的创建 Dockerfile 文件的方式

> 原文：<https://betterprogramming.pub/a-better-developer-friendly-way-of-creating-dockerfile-files-e4d81b0ed819>

## 在这个容器世界中，许多图像都是通过 Dockerfile 文件定义的。有些简单，有些复杂

![](img/b235692fd75e6dbd6d25913f46729a7e.png)

在 [Unsplash](https://unsplash.com/s/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由 [Taskin Ashiq](https://unsplash.com/@taskinhoo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

# 摘要

## 问题

复杂的 docker 文件通常是那些包含大而长的命令的文件。
当你看到这些，你的大脑开始旋转。这样我们就有了第一个问题:长 docker 文件的可理解性。

**示例链接 A.** (您可能想让它保持打开，我会引用它):

[https://raw . githubusercontent . com/nginxinc/docker-nginx/5971 de 30 c 487356 D5 D2 a2 E1 a 79 e 02 b 2612 f 9 a 72 f/mainline/buster/docker file](https://raw.githubusercontent.com/nginxinc/docker-nginx/5971de30c487356d5d2a2e1a79e02b2612f9a72f/mainline/buster/Dockerfile)

另一个问题:为了图像大小，最终图像通常应该有尽可能少的层，而在开发期间，您希望有更多的层，以便在您修复一些脚本时使用 Docker 的缓存功能。相反的方向…

面临的另一个问题是:可重用性。

## 让我们努力做得更好

在这篇文章中，我将探讨一种非常简单的方法来改善上述问题。

您将获得一种开发友好的方式，在各种 docker 文件中使用可重用的代码片段来创建 docker 文件。它可以生成更高质量的 docker 文件，并在开发过程中使用图层缓存时自动生成小的最终图像。

我认为它可以进一步改进，带来额外的好处。

注意:这不是关于容器、Docker 或 Docker 文件的介绍性课程。这方面还有很多其他的好资源。

代码可以在以下位置找到:

*   [https://github.com/cr1st1p/dockerfile-li](https://github.com/cr1st1p/dockerfile-lib)b—库和指令
*   [https://github.com/cr1st1p/docker-simplestreams-server](https://github.com/cr1st1p/docker-simplestreams-server)—一个用例
*   [https://github.com/cr1st1p/docker-shinobi-cctv](https://github.com/cr1st1p/docker-shinobi-cctv)—另一个用例

# 让我想想…

让我们开始我们的旅程。我们在这里:我们必须为我们的超级杀手……项目……呃..集装箱。

## 认知负荷

根据需要，Dockerfile 文件可以是:

*   非常简单，或多或少由几个运行命令、几个复制命令、一个入口点组成，瞧！做完了，该吃冰淇淋或喝咖啡了！
*   庞大而复杂，运行命令要做很多事情——更新包存储库数据、下载 GPG 密钥、引入包、设置配置文件等等。再看链接一个**，上面的**。冰淇淋可以等，把咖啡端进来。

所以，我们要写一个长的。之后，我们希望能够不大惊小怪地捡起来，对吗？我们不想陷入“只写一次，从不阅读”的境地(谢谢，我已经有多年的 Perl 编程经验了。)

当然，我们总是会遇到大型代码库，但是我们总是希望容易理解它们是关于什么的。那些大的运行 Dockerfile 命令并没有什么帮助，对吗？

这里，再开一个例子，**链接** **B** :

[https://github . com/docker-library/Cassandra/blob/master/docker file . template](https://github.com/docker-library/cassandra/blob/master/Dockerfile.template)。

最近不小心碰到了这个。(幸运的是，至少他们有一些评论——谢谢！)

## 优化—层数

让我们引入另一个方面:*优化。*你现在可能知道图像越小越好。您知道每个 Dockerfile 命令(包括 RUN)都会创建一个新层。

当您存储或传输图像时，所有层都被存储/传输——这意味着如果在一个运行命令中您安装了一些最终图像中不需要的辅助工具，然后在另一个运行命令中您删除了它，这实际上并不重要。您的图像中仍然会有这些数据，只是不在最终图层中。

提示:因此，从安全角度来说，您不应该在其中一个层中使用秘密数据，然后删除它，因为该信息实际上仍然存在！

注意:层本身不一定是坏的(有待进一步讨论)，但是当它们也包含数据时，就有问题了。

好了，我们知道了——尽可能少的层数！那你是怎么做到的呢？

你把所有或者几乎所有的运行命令组合在一个层或者更少的层中。你看到了那个大文本，所有的行都以`&&`开始，以`\`结束，对吗？没有吗？你没有打开**链接** **一个**？

下面是一个基于 Debian/Ubuntu 映像起点的更简单的虚构示例:

```
RUN \
    apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install apt-transport-https ca-certificates \
    && apt-key adv --keyserver SERVER --recv-keys KEY_ID \
    && DEBIAN_FRONTEND=noninteractive apt-get install some_packages \
    && sed -e s/something/otherthing/ \
    && rm somefile \
    && mkdir someDir \
    && run_a_script with_some_parameters \
    && repeat_above a_few_times
```

应该比那个容易读。如果没有注释，就不容易看到各个部分的开始和结束，以及每个部分在做什么。

Docker 确实提供了一个替代方案，解决了由于使用多层而可能在最终图像中出现各种垃圾的问题:[多阶段构建](https://docs.docker.com/develop/develop-images/multistage-build/)。如果您密切关注它的作用，那么它似乎主要在构建阶段的结果只包含一到三个目录时有用。也就是说，您可以轻松地列举它们，以便将它们从多层建筑 docker 图像复制到最终图像中。

如果你在那种情况下，对你有好处，你消除了最终图像大小的问题，也可能降低了认知负荷。其他问题仍然存在，所以请继续阅读。

Docker 也有一个`[--squash](https://docs.docker.com/engine/reference/commandline/build/#squash-an-images-layers---squash-experimental)`命令行参数来将所有层压缩成一个层，但这是一个实验性的特性，你必须在服务器上手动启用。嗯。

此外，如果你真的想有几层而不是一层，我想命令行参数不会有用。

和之前一样的评论——这仅仅解决了最终图像尺寸的问题，而不是全部。

## 开发期间的 Dockerfile 文件与最终版本

假设在你的 Dockerfile 开发过程中，你犯了一些错误(我们都犯了，对吧？).您将会多次运行`docker build`流程。Docker 很好地缓存了每一层，并在后续的构建中尽可能重用这些层。重建时拥有的资源越多，重用的机会就越大。

“哦，”你说，“缓存很好！”嗯，是的，但是根据前面的部分，这是我们寻求一个尽可能少的层的更小的图像的相反方向。

简而言之，看起来在开发过程中您可能需要许多 RUN 命令，但是对于最终的变体，越少越好。嗯。

一种方法是，当我们开发的时候，将这个庞大的命令列表分割成多个不太大的 RUN 命令，在编码结束的时候，将它们重新组合成一个列表。后来，当改变/修复某个东西时，重复这个过程。

以上面的虚构例子为例，我们可以这样分割它:

```
RUN \
    apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install apt-transport-https ca-certificates
# split point 1RUN    
    && apt-key adv --keyserver SERVER --recv-keys KEY_ID \
    && DEBIAN_FRONTEND=noninteractive apt-get install some_packages

# split point 2    
RUN \    
    && sed -e s/something/otherthing/ \
    && rm somefile \
    && mkdir someDir \
    && run_a_script with_some_parameters# split point 3    
RUN \
    repeat_above a_few_times
```

所以我们这样做，我们做我们的编辑/修复/增强，最后，我们重组这些块。

乏味。去喝杯咖啡，还有更多。

为什么是那些分裂点？

**分割点 1** :因为你不太可能希望每次都运行一个`apt-get update`。而且这是一个耗时的过程，所以不管接下来会发生什么，你都希望它被缓存。

**Split point 2** :在列出必需的软件包安装步骤的部分中，您不太可能犯(许多)错误，所以您希望缓存这个可能很耗时的部分。所以你把它和后面的分开。接下来呢？配置。这比软件包列表更有可能需要一些更新，所以这个分割点是一个明智的决定。

**分割点 3** :我们在它们之间分离我们拥有的任何高级组件。我们可能已经成功配置了其中一个组件，您可以开始着手下一个组件了。您希望将缓存层与目前为止您对第一个组件所做的操作结合使用。

提示 1:按照章节被改变的可能性降低来排序章节。例如，包配置最有可能改变，所以那些通常在最后结束。或者入口点脚本。

技巧 2:特别是如果您没有缓存代理，当您添加一个新的存储库时，您还需要从中获取信息(通过一个`apt-get update`)。如果您有多个存储库，首先添加存储库，然后运行一个`apt-get update`。当然，这是 Debian/Ubuntu 特有的，但是其他人也可能有类似的行为。

## 复用性

看**链接**B、docker 文件名(`Dockerfile.template`、*、*如果没有打开链接)。这不是 Dockerfile 的最终版本。它实际上似乎是一个简单的模板文件，其中一些变量被替换为`sed`。

这背后的原因是，他们希望能够强制对其进行一些更改。通常，您会更改基本映像名称和版本，以及您的应用程序版本。

嘿，不错，这里有可重用性——但是太单薄了。另一方面，我们已经得到了提示:模板化或生成 Dockerfile！

让我们看看上面虚构的 Dockerfile 部分示例的内容，甚至可能在**链接** **B** 处。

我们有一个`apt-get update`司令部。很好。在某种程度上，我意识到添加`-qq`可能会更好，不那么冗长。(记下:编辑我所有的 docker 文件来改变这一点。)

我们来安装一个包:`apt-get install some_package`。很好。

后来你了解到，无论那个软件包建议你安装什么，最好更精确地说明你想安装什么和不想安装什么。这使得图像更薄，从安全角度来看更好(攻击面更小)。(记下:编辑我所有的 docker 文件以使用`apt-get install — no-install-suggests — no-install-recommends`。)

啊，把`DEBIAN_FRONTEND=noninteractive`和`-yqq`也加进去。在 Dockerfile 文件中，您可能有几个安装包的地方。你应该把它们都换成那条大线。你闻到另一个问题了吗？(如果你感冒了，那就是复制粘贴。)

我需要一个 NGINX 服务器加上一个 Python 守护程序代码加上一个 Python web 服务器。我想我是从 https://hub.docker.com/r/tiangolo/uwsgi-nginx/的[开始的](https://hub.docker.com/r/tiangolo/uwsgi-nginx/)。

我们还要注意到它到 Dockerfile 的链接:
**Link****C**:[https://raw . githubusercontent . com/tiangolo/uws gi-nginx-docker/master/python 3.7/docker file](https://raw.githubusercontent.com/tiangolo/uwsgi-nginx-docker/master/python3.7/Dockerfile)

无论如何，我很快就放弃了那个源码，因为开始的镜像已经很大了，安装了很多不必要的包。但实际上可能是因为它自己的起始层。

所以，我说，我要给自己造一个。

我以 NGINX 的资源库为起点看了一下(比如**链接**A)——顺便借鉴一下好东西！

有趣的是:当通过`apt` : `Dpkg::Options::= — force-unsafe-io`安装包时，我发现了另一个很好的选择。(所以我自己注意:编辑我所有的 docker 文件来改变安装包命令。嗯，我要开始重复我的待办事项吗？)

我看到了安装 NGINX 的另一个有趣的部分——寻找一个好的服务器来导入 GPG 密钥。我知道我会在其他 Docker 图像中需要 NGINX，所以……(你已经知道该怎么做了，对吗？记录:改变我在所有 docker 文件中安装 NGINX 的方式。)

还有…注意如何清理:缓存目录、日志目录清理、包缓存、库列表、自动安装的包和可以自动删除的包，等等。通过查看每一个 Dockerfile 文件，您将会了解到可以清理的新内容，并且您将会收获更多！(我自己注意:更新我的 Dockerfiles，清理部分，用我可能错过的东西，从我给你的这些链接。)

记下所有这些事情后，我要做什么？去编辑我的每一个 docker 文件。每次我发现我能改进的事情，我都会再做一次。我的大脑开始尖叫:可重用性在哪里？好的开发实践在哪里？

# 开始改善情况

目标:

*   目标 A——降低认知负荷。有你能命名的小的独立部分。当程序是由更小的、名字更好的函数构建而成时，考虑函数和理解整个程序的方式。
*   目标 B——使用相同的代码库，允许 Dockerfiles 寻找生产构建运行或开发构建运行。
*   目标 C——允许 docker 文件基于一些参数(如版本、映像起始点等)看起来有一点不同。).
*   目标 D—允许从可重复使用的片段创建 docker 文件。

思想开始流动…

*   不打算直接使用 docker 文件，而是生成一个。模板机制什么的。没什么新鲜的，别人都在做，但我觉得没有做到极致。
*   由于大多数问题来自 shell 命令，模板引擎将是 shell 脚本( [Bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell)) )。也就是说，我们将从 shell 脚本中生成代码。这意味着没有额外的依赖。无论如何，要编写 docker 文件，您必须知道一些 shell 脚本，所以您可以重用这些知识。很可能您已经有了一个 shell 构建脚本(`build.sh`、`update.sh`等等)。
*   因此，脚本将是输出实际 Dockerfile 内容的 Bash 代码。参数/变量可用于定制输出(如来自图像源)。作为 shell 脚本，您应该能够轻松获得目标 C)。
*   更容易理解:使用更小、更好命名的 Bash 函数。目标 A 检查过了！起个好名字是主观的，但仍然比没有名字大了一步。
*   我开始感觉好些了。我们正在接近一种更加以开发者为中心的方式。吃冰淇淋。
*   我们需要跟踪 Dockerfile 是需要在开发者模式还是生产模式下生成。
*   为了实现前面的思想，我们需要能够区分 RUN 命令和其他命令。正在运行的将在生产模式中加入。(这一项和前一项是针对目标 b 的。)
*   可重用性:你将你的公共函数创建到一个单独的 SCM 存储库中，然后以某种方式将它用作 SCM 依赖(例如，作为 Git 子模块)。)
*   Bash 脚本将使用大量“here 文档”来生成内容。不喜欢那个，但还是可以接受的。
*   所有生成的内容(用于运行命令)需要以`;`开始，以`\`结束。强制:在你的代码前需要有`set -e`。比起`&&`，我更喜欢`;`。有些结构用`&&`方式实现会更难，而`;`更自然——它是为分隔语句而创建的。我不喜欢这样的事实，我仍然不得不用一些东西来限定语句。尽管如此，这是一种可以接受的痛苦。暂时如此。

# 密码

共享(可重用)代码片段:

`main.sh`:

`apt.sh`:

`dockerfile-gen.sh` —真实项目的主文件:

## 使用

生成 docker 文件并运行构建:

假设您处于开发模式:

*   运行一次。您将获得一个包含许多运行条目的文件(每个`run_*`函数调用至少有一个)
*   现在修改脚本中的一些内容，比如与配置相关的代码。运行构建脚本，它应该显示 Docker 正在重用一些缓存层。
*   当一切正常时，用不同的参数运行构建脚本，这将进一步删除`— dev`参数。
*   代码应该再次生成 docker 文件，但是这次使用的 RUN 命令要少得多。
*   对这张图片再做一次测试。

搞定了。

除了那些函数调用，从主观上来说，这些函数调用命名得很好，能让你很快理解我们在做什么，可能会出现一个问题:条件调用是怎么回事？

## **把** `**copy_files**` **叫做**

您很可能需要将一些文件(配置文件、代码、启动脚本等)推送到映像中。在开发过程中，它们发生变化的概率非常高，因此，正如我们所讨论的，我们希望在 docker 文件中尽可能晚地发生这种情况。但是我们还需要在那之后运行一些代码来对它们做一些真正需要的动作(`chmod +x`、`mv`等等)。).

这就是在它之后调用函数`run_fix_files`的原因。不幸的是，这将迫使一个新的层，但我们无能为力。将该功能保持在最低限度。在生产模式下，我们不希望在清理功能和开始更新/安装软件的行之间有任何东西。您希望那里只有`run_*`风格的函数调用。

## `run_cleanup`呼叫

您可能不希望它处于开发模式，因为这需要时间，并且您对运行它或最终图像的大小不感兴趣。
另一方面，这使得测试由生产 other 文件创建的图像变得更加必要，因为您还想测试清理的副作用。

想想看:如果你的应用程序依赖于某个库，但是这个函数和它所做的任何事情都认为它是自动安装的，并不是真正需要的，那该怎么办？

对于目标 D，为了从共享代码中获益，你应该把它放在一个专用的存储库中，比如 g it，或者这个[存储库](https://github.com/cr1st1p/dockerfile-lib)。在你的应用程序的安装说明中，你应该提到用户应该如何`git clone`你的库，这样他们也可以获得依赖关系。

如果您为某个产品创建了一个映像，这个映像很有可能稍后被集成到不同的容器中，那么您应该将您的函数(来自那个`dockerfile-gen.sh`脚本)放在单独的文件中，以允许它们被重用。例如，在[这个库](https://github.com/cr1st1p/docker-simplestreams-server)中，我把一些函数放在目录`dockerfile`中。

关于复用性，本来想多说一点，但是只要同时打开上面那三个链接(A、B、C)自己找找就好了。

重要提示:您不能将这种编写 docker 文件的方式强加给其他人，但是在您的组织中，如果您正在创建许多具有目前为止所讨论的问题的图像，您可能会从改变编写它们的方式中受益。

# 进一步的改进和想法

*   这可能对那些拥有大量使用缓存的专用构建节点的人有所帮助:您可以拥有一段时间后自动使缓存层失效的代码。想想几个多小时后就不想用`FROM ubuntu:latest RUN apt-get update`的缓存层了。
*   有一个好的`apt`处理库，或者你正在使用的任何包管理。也许可以定义一种方法来声明运行时需要的包和构建时需要的包。
*   最重要的是:找到一种不使用 Bash“here documents”的方法，而是使用真正的 shell 代码。你会获得更多的优势。别走开，我对此有一些想法。

哇！太久了！

即使你不会使用或应用我上面描述的，我仍然希望你喜欢它，并且你学到了关于写 Docker 图片的新东西。如有任何问题、意见或建议，请随时联系我。