# 创建和部署您的新的简单且易于编辑的投资组合网站

> 原文：<https://betterprogramming.pub/create-and-deploy-your-new-minimal-and-easy-to-edit-portfolio-website-76e87fadce8e>

## Jekyll 和 GitHub 页面非常棒

![](img/29ccaffe90adcdfc443ad8cabd5ce7d7.png)

由[劳伦·曼克](https://unsplash.com/@laurenmancke?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

前不久在 GitHub 上看到一个很棒的项目，叫做 [Eve](https://github.com/jibolash/Eve) 。这基本上是一个最小的哲基尔主题，你可以很容易地使用它，并将其设置为你的作品集。

我想我会尝试一下，设置一下，但是有很多事情我想改变。所以，我没有编辑它，而是启动了 BootstrapStudio，决定自己创建一个主题。

你可以在我的网站上看到结果[。我不得不说我对结果感到非常惊讶。它真的很简单，尽管设计结构肯定不是我的全部工作，但我喜欢它。我希望你也能。](https://me.tommasodeponti.site/)

今天，我想向您展示我构建新投资组合的主要步骤，以及如何定制它并将其部署到 GitHub 页面——所有这一切都在五分钟之内完成。

# 为什么是杰基尔？

简要说明一下我为什么选择使用 Jekyll:除了许多强大的功能外，它还提供了一种使用 YAML 文件轻松编辑 HTML 页面内容的伟大而简单的方法。如果不熟悉，到文章结尾就都清楚了。

# 黑暗的创造

你可能已经猜到了， [DarkJTK](https://github.com/heytdep/DarkJKT) (黑暗哲基尔主题)是我创作的主题。在这一部分，我将尽我所能引导你完成创建这个主题的步骤。

## 安装 Jekyll

安装 Ruby 后，安装 Jekyll:

```
gem install jekyll bundler
```

## 构建项目

我们需要创建一个 Gemfile ( `bundle init`)，然后编辑它以添加 Jekyll 作为依赖项:

```
echo 'gem "jekyll"' >> Gemfile
```

现在，我们必须安装 Jekyll:只需在`Projects`目录中键入`bundle`。

## 添加 HTML/CSS/JS

我们现在已经创建了 Jekyll 项目。我们需要创建网页。为此，您需要创建一个`index.html`文件并创建您的页面。我不会重复我构建当前页面的步骤，原因很简单:我使用 BoostrapStudio 重新创建了一个非常简单的设计。

创建页面后，将以下内容添加到`index.html`的第一行。这将告诉杰基尔使用[前置事宜](https://jekyllrb.com/docs/step-by-step/03-front-matter/):

```
---
---
```

## 通过 YAML 文件更改内容

现在是时候释放我使用 Jekyll 的主要原因了:使用 YAML 文件修改 HTML 页面的内容。

1.  在`Project`目录下创建一个`_config.yml`文件。
2.  在`_config.yml`文件中添加一个变量(如你的名字:`name: Tommaso`)。
3.  更新您的`index.html`页面。为了在 HTML 上下文中使用该变量，您需要做的就是将`{{ site.variable }}`添加到代码中。例如，在我的例子中，我将在一个`h1`标签中添加`{{ site.name }}`:

```
<h1>My name is {{ site.name }}</h1>
```

我的`_config.yml`长这样:

```
name: Tommaso
```

## 构建和服务于项目

为了应用这些更改，我们想要执行`jekyll build`。一旦这样做了，要在本地主机上执行它，运行`jekyll serve`。这将在 http://127.0.0.1:4000 上运行您的 Jekyll 项目。

这为您提供了一个关于如何创建您自己的自定义 Jekyll 模板的很好且简单的分步解释。现在，让我们看看如何安装和修改 [DarkJTK](https://github.com/heytdep/DarkJKT) 来让你的网站看起来像 [mine](https://me.tommasodeponti.site/) 。

# 使用 DarkJTK

首先，我们需要安装它:

```
git clone https://github.com/heytdep/DarkJKT
```

然后，在 DarkJTK 目录(`cd DarkJTK`)中，运行`bundle install`来安装项目。然后您可以修改`_config.yml`文件的内容，使 DarkJTK 成为您自己的。

您可以修改:

*   名称:`name: Your Name`
*   关于我的内容:`aboutMe: Hey, I'm ...`
*   简历:`bio: Developer ...`
*   博客页面:`blog: Hey check out my blog ...`。如果你没有博客，你可以很容易地改变 HTML 代码，随心所欲地使用那个部分。
*   简历的图像:首先，你需要把你的图像放在`DarkJTK/assets/img`目录下。然后在 configs 文件中，`bioimg: youriamgefilename`。
*   附加部分(在我的例子中，是致谢部分:`acknowledgments: my bug bounty acknowledgments`)。如果您不想要这个部分，或者想让它有另一个名称，继续修改`index.html`代码。这真的很简单，因为它只是一个 66 行的文件。
*   社交把手。我选择了 GitHub、Twitter、Hackerone 和 Medium，但是你可以很容易地从`index.html`文件中改变图标。

完成所有配置后，在本地环境中测试它:`bundle exec jekyll serve`。这将在 [http://127.0.0.1:4000](http://127.0.0.1:4000) 上托管您的新网站。

喜欢吗？到时候公布吧。

1.  创建一个新的 Git repo(例如`MyNewWebsite`)。

2.删除当前源并添加您的存储库:

```
git remote remove origin
git remote add origin https://github.com/you/MyNewWebsite
```

3.添加文件并提交:`git add .`和`git commit -m "built website"`。

4.现在你只需要把一切都推给回购:`git push origin master`。

是时候实现 GitHub 页面来在一个活动域上托管 Jekyll 网站了。

转到[https://github.com/you/MyNewWebsite/settings/pages](https://github.com/you/MyNewWebsite/settings/pages.)。在`source`下拉菜单下，选择`master`，然后点击`Save`。你会得到一条类似于“你的网站已经准备好在 you.github.io/MyNewWebsite/发布了*”的消息。稍等几分钟，你会看到一切都在直播。*

# 结论

现在你已经学会了如何创建你自己的 Jekyll 主题，编辑它，并发布它。此外，你可能会喜欢 DarkJTK，并把它作为你新的最小投资组合网站。

一如既往，感谢阅读。