# 用 Hugo 和 GitHub 页面创建一个可移植的 Makefile 来连续交付

> 原文：<https://betterprogramming.pub/create-a-portable-makefile-for-continuous-delivery-with-hugo-and-github-pages-98aa44fe059b>

## 构建您的 Hugo 站点，优化图像，并运行您的 CI/CD GitHub 操作流程

![](img/95fdfcd2b051ca6a6f461d95292b897d.png)

如果你了解我，你就会知道我喜欢自动化。我一直在使用 AWS Lambda、Netlify 和 Travis CI 的组合来自动构建和发布我的博客。我的任务工作流程包括:

*   与雨果一起构建，按照 Netlify 和 Lambda 的计划推进
*   优化和调整图像大小(Netlify)
*   使用 [HTMLProofer](https://github.com/gjtorikian/html-proofer) 进行测试(Travis CI)
*   部署到我的[单独的公共 GitHub 页面库](https://victoria.dev/blog/two-ways-to-deploy-a-public-github-pages-site-from-a-private-hugo-repository/) (Netlify)

多亏了 GitHub Actions 的引入，我能够只用一个可移植的 Makefile 就完成以上所有工作。

稍后，我将介绍我的动作设置；今天，我将带您了解我的 Makefile 的本质，以便您可以编写自己的 Makefile。

# Makefile 可移植性

[POSIX standard——flavor Make](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/make.html)可以在所有类似 Unix 的系统上运行。 [Make 衍生产品](https://en.wikipedia.org/wiki/Make_(software)#Derivatives)，如 [GNU Make](https://www.gnu.org/software/make/) 和几种风格的 BSD Make，也运行在类 Unix 系统上，尽管它们的特定用途需要安装相应的程序。

为了编写一个真正可移植的 Makefile，我的遵循 POSIX 标准。(关于 POSIX 兼容 makefile 的更全面总结，我发现这篇文章很有帮助:[关于可移植 makefile 的教程](https://nullprogram.com/blog/2017/08/20/)。)

我运行 Ubuntu，所以我用 BSD Make 程序`bmake`、`pmake`和`fmake`测试了可移植性。因为 shell 命令不同，所以与非混合系统的兼容性稍微复杂一些。有了 NMAKE 这样的衍生工具，最好用适当的 Windows 命令编写一个单独的 Makefile。

虽然我的许多特定用例可以通过 shell 脚本实现，但我发现 Make 提供了一些有价值的优势。当涉及到组织我的步骤时，我喜欢使用变量和[宏](https://en.wikipedia.org/wiki/Make_(software)#Macros)以及[规则](https://en.wikipedia.org/wiki/Makefile#Rules)的模块性。

规则的编写主要归结于 shell 命令，这是 Makefiles 如此可移植的主要原因。最好的部分是你可以在终端中做几乎所有的事情——你当然可以处理上面列出的所有工作流程步骤。

# 我的连续部署(CD)生成文件

这是处理我的工作流的可移植 Makefile。是的，我在里面放了表情符号。我是个怪物。

接下来，该工作流:

1.  克隆公共`Pages`存储库
2.  清除(删除)以前的生成文件
3.  下载并安装指定版本的 Hugo(如果 Hugo 尚未安装)
4.  构建网站
5.  优化图像
6.  用 HTMLProofer 测试生成的网站
7.  准备新的提交并将其推送到公共的`Pages`存储库

如果您熟悉命令行，这其中的大部分可能看起来很熟悉。这里有几个可能需要解释一下的地方。

## 检查程序是否已经安装

我认为这一点很整洁:

```
if ! [ -x "$$(command -v hugo)" ]; then\
...
fi
```

我使用一个否定的`if`条件和`command -v`来检查是否存在一个名为`hugo`的可执行文件(`-x`)。

如果没有，脚本会获取指定版本的 Hugo 并安装它。[这个堆栈溢出答案](https://stackoverflow.com/a/677212)很好地总结了为什么`command -v`比`which`更便于移植。

## 图像优化

我的 Makefile 使用`mogrify`将调整大小和压缩后的图像批量放入特定的文件夹中。它会使用文件扩展名自动查找它们，并且只修改任何维度上大于 1000px 目标大小的图像。在这篇文章中，我写了更多关于[批处理一行程序的内容。](https://victoria.dev/blog/how-to-quickly-batch-resize-compress-and-convert-images-with-a-bash-one-liner/)

有几种不同的方法可以完成同样的任务，理论上，其中一种方法是利用 Make 的[后缀规则](https://en.wikipedia.org/wiki/Make_(software)#Suffix_rules)只对图像文件运行命令。我发现 shell 脚本可读性更好。

## 使用 Dockerized HTMLProofer

HTMLProofer 与`gem`一起安装，并使用 Ruby 和 [Nokogiri](https://nokogiri.org/tutorials/ensuring_well_formed_markup.html) ，这为 CI 工作流增加了大量的安装时间。

谢天谢地， [18F](https://github.com/18F) 有一个[dockered 版本](https://github.com/18F/html-proofer-docker)，实现起来要快得多。它的使用需要启动容器，将构建的站点目录[挂载为数据卷](https://docs.docker.com/storage/volumes/#start-a-container-with-a-volume)，这可以通过将它附加到`docker run`命令轻松实现。

```
docker run -v /absolute/path/to/site/:/mounted-site 18fgsa/html-proofer /mounted-site
```

在我的 Makefile 中，我使用[默认环境变量](https://help.github.com/en/articles/virtual-environments-for-github-actions#environment-variables) `GITHUB_WORKSPACE`指定了绝对站点路径。我将在另一篇文章中深入探讨这个和其他 GitHub Actions 特性。

同时，祝你快乐！