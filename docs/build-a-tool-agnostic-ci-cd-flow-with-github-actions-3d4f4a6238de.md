# 用 GitHub 动作构建一个与工具无关的 CI/CD 流

> 原文：<https://betterprogramming.pub/build-a-tool-agnostic-ci-cd-flow-with-github-actions-3d4f4a6238de>

## 如何利用一个简单的 GitHub 动作工作流而不牺牲不可知的工具

![](img/9212dab547a96dec21b3b25734a0a006.png)

图片由 [GitHub](https://github.com/) 提供

不可知工具是一个聪明的概念，即您应该能够在各种环境中运行您的代码。有了许多可用的持续集成和持续开发(CI/CD)应用程序，不可知工具给了开发人员一个很大的优势:可移植性。

当然，让你的 CI/CD 在任何地方都能工作*是一项艰巨的任务。仅针对 GitHub 库的流行 CI 应用程序就使用了多种配置语言，包括 [Groovy](https://groovy-lang.org/syntax.html) 、 [YAML](https://yaml.org/) 、 [TOML](https://github.com/toml-lang/toml) 、 [JSON](https://json.org/) 等等……当然，所有语言都有不同的语法。将工作流从一个工具移植到另一个工具不仅仅是一杯咖啡的过程。*

*[GitHub Actions](https://github.com/features/actions) 的引入有可能为组合添加另一个工具——或者，对于正确的设置，极大地简化 CI/CD 工作流。*

*我之前用几个捆绑在一起的应用程序完成了我的 CD 流。我使用 AWS Lambda 来按计划触发站点构建。我让 Netlify 构建推送触发器，运行图像优化，然后将我的站点推送到公共页面存储库。我在公共存储库中使用 Travis CI 来测试 HTML。所有这些都与 GitHub Pages 协同工作，GitHub Pages 实际上是网站的宿主。*

*我现在使用 GitHub Actions 来完成所有相同的任务，使用一个[可移植的 Makefile](https://medium.com/better-programming/create-a-portable-makefile-for-continuous-delivery-with-hugo-and-github-pages-98aa44fe059b) 构建指令，没有任何其他 CI/CD 应用程序。*

# *欣赏贝壳*

*大多数 CI/CD 工具有什么共同点？它们在 shell 环境中运行您的工作流指令！这太棒了，因为这意味着大多数 CI/CD 工具可以做你在终端中可以做的任何事情……而且你几乎可以在终端中做任何事情。*

*特别是对于一个包含用例来说，比如用一个像 Hugo 这样的生成器来构建我的静态站点，在 shell 中运行它是显而易见的。要告诉魔盒做什么，我们只需要写指令。*

*虽然 shell 脚本肯定是最容易移植的选择，但是我使用仍然非常容易移植的 Make 来编写我的过程指令。这为我提供了一些优于简单 shell 脚本的优势，比如变量和[宏](https://en.wikipedia.org/wiki/Make_(software)#Macros)的使用，以及[规则](https://en.wikipedia.org/wiki/Makefile#Rules)的模块化。*

*在我的上一篇文章中，我已经了解了 Makefile 的本质。让我们看看如何让 GitHub Actions 运行它。*

# *使用带有 GitHub 操作的 Makefile*

*就可移植性而言，我的神奇 Makefile 就存储在存储库根目录中。因为 Makefile 包含在代码中，所以我可以在任何可以克隆存储库的系统上本地运行它，只要我设置了环境变量。使用 GitHub Actions 作为我的 CI/CD 工具就像让 Make go 工作起来一样简单。*

*我发现 [GitHub Actions 工作流语法指南](https://help.github.com/en/articles/workflow-syntax-for-github-actions)非常简单，尽管在选项上也很冗长。下面是运行 Makefile 的必要设置。*

*位于`.github/workflow.yml`的工作流文件包含以下内容:*

*我将解释使这个工作的组件。*

# *触发工作流*

*动作支持一个工作流的多个[触发器。使用`on`语法，我为自己定义了两个触发器:一个](https://help.github.com/en/articles/events-that-trigger-workflows)[仅将事件](https://help.github.com/en/articles/workflow-syntax-for-github-actions#onpushpull_requestbranchestags)推送到`master`分支，一个[调度的](https://help.github.com/en/articles/events-that-trigger-workflows#scheduled-events-schedule) `cron`作业。*

*一旦`workflow.yml`文件在您的存储库中，您的任何一个触发器都将导致运行 Makefile 的操作。要了解最后一次跑步的情况，你也可以[在自述文件中添加一个有趣的徽章](https://help.github.com/en/articles/configuring-a-workflow#adding-a-workflow-status-badge-to-your-repository)。*

# *一件讨厌的事*

*因为 Makefile 在每次推送到`master`时都会运行，所以当站点构建没有变化时，我有时会出错。当 Git 通过 [my Makefile](https://victoria.dev/blog/a-portable-makefile-for-continuous-delivery-with-hugo-and-github-pages/) 尝试提交到 Pages 存储库时，没有检测到任何更改，并且提交会令人恼火地失败:*

```
*nothing to commit, working tree clean
On branch master
Your branch is up to date with 'origin/master'.
nothing to commit, working tree clean
Makefile:62: recipe for target 'deploy' failed
make: *** [deploy] Error 1
##[error]Process completed with exit code 2.*
```

*我遇到了一些建议使用`diff`来检查是否应该提交的解决方案，但是由于[原因](https://github.com/benmatselby/hugo-deploy-gh-pages/issues/4)这可能不起作用。作为一种变通方法，我简单地将[当前 UTC 时间](https://gohugo.io/functions/format/#use-local-and-utc)添加到我的索引页面，这样每个构建都将包含一个要提交的变更。*

# *环境和变量*

*您可以使用`runs-on`语法为您的工作流定义运行的[虚拟环境](https://help.github.com/en/articles/virtual-environments-for-github-actions#supported-virtual-environments-and-hardware-resources)。显而易见的最佳选择是 Ubuntu。使用`ubuntu-latest`可以让我得到最新的版本，不管当你读这篇文章的时候发生了什么。*

*GitHub 为工作流设置了一些默认的环境变量。带有`fetch-depth: 1`的`[actions/checkout](https://github.com/actions/checkout)` [动作](https://github.com/actions/checkout)在`GITHUB_WORKSPACE`变量中创建了您的库的最近提交的副本。这允许工作流在`GITHUB_WORKSPACE/Makefile`访问 Makefile。如果不使用 checkout 操作，将找不到 Makefile，并且我会得到如下所示的错误:*

```
*make: *** No rule to make target 'all'. Stop.
Running Makefile
##[error]Process completed with exit code 2.*
```

*而有一个[默认](https://help.github.com/en/articles/virtual-environments-for-github-actions#github_token-secret) `[GITHUB_TOKEN](https://help.github.com/en/articles/virtual-environments-for-github-actions#github_token-secret)` [秘密](https://help.github.com/en/articles/virtual-environments-for-github-actions#github_token-secret)，这个不是我用过的那个。默认情况下，仅在本地范围内使用当前存储库。为了能够推送到我单独的 GitHub Pages 存储库，我创建了一个作用于`public_repo`的[个人访问令牌](https://github.com/settings/tokens)，并将其作为`secrets.TOKEN`加密变量传入。有关详细步骤，请参见[创建和使用机密](https://help.github.com/en/articles/virtual-environments-for-github-actions#creating-and-using-secrets-encrypted-variables)。*

# *便携式工具*

*使用一个简单的 Makefile 来定义我的 CI/CD 过程的好处是它是完全可移植的。我可以在任何我可以访问环境的地方运行 Makefile，包括大多数 CI/CD 应用程序、虚拟实例，当然还有我的本地机器。*

*我喜欢 GitHub 动作的原因之一是让我的 Makefile 运行起来非常简单。我认为语法做得很好，容易阅读，并且在找到你正在寻找的选项时很直观。对于已经在使用 GitHub 页面的人来说，Actions 提供了相当无缝的 CD 体验。如果这种情况有所改变，我可以在其他地方运行我的 Makefile。*

*感谢阅读！*