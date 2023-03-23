# 构建一个 CLI 来帮助管理 GitHub 帐户、React 项目等等…

> 原文：<https://betterprogramming.pub/building-a-cli-to-help-manage-github-accounts-react-projects-and-more-2755259be493>

## 介绍贾维斯，我的新的改进的小助手

![](img/ce11e7b99c0a778c1a01b634e245b008.png)

介绍贾维斯，我的新的和改进的小助手。很长一段时间以来，我一直想创建一个 CLI 来帮助我处理工作流程和日常任务。这就是我创建 git-cli 的原因，我在上一篇文章中曾提到过。git-cli 对于 GitHub 帐户的简单管理非常有效，但我希望它能做更多。所以我舒服了些，放了些音乐，思考下一步该做什么。

我的结论是，在 git-cli 上扩展并不能解决问题，我将从头开始创建一个新的 cli。我决定这样做有几个原因。首先，我觉得我以一种很难扩展的方式编写和组织了它——比它需要的更难。其次，我知道我是以一种不提倡代码重用的方式编写的。最后，我觉得我可以做得更好。

既然我已经决定从头开始创建一个新的 CLI，我必须考虑我到底想要它做什么以及如何做。我主要开发部署在谷歌应用引擎上的 React 应用程序，使用 Firebase 函数和 Firestore 作为后端，并将静态文件存储在 AWS S3 上。我还经常需要在多个 GitHub 账户、谷歌云平台账户和 AWS 账户之间切换。根据这些信息，我提出了以下要求:

*   能够轻松地创建、存储、更新、删除 GitHub、GCP 和 S3 配置，并在它们之间切换。
*   能够用一个命令将更改推送到 Github。
*   能够克隆 repos，并通过一个简单的命令从 GitHub 中提取更改。
*   能够部署到谷歌应用引擎，推到 GitHub，并上传静态文件到 S3 所有与一个命令。
*   能够初始化一个完整的样板反应模板，初始化一个 git 目录，安装 NPM 软件包，并建立和运行一个开发服务器所有与一个命令。
*   能够用简单的命令创建 React 组件。

我还决定我想要一种从命令行打开和搜索诸如 Google、NPM、GitHub 和 StackOverflow 等网站的方法。我不确定这个命令是否值得，但是它比我想象的要快，我现在一直在使用它。

现在我已经有了一个需求列表和一个如何让一切工作的粗略计划，除了构建 CLI 之外，剩下唯一要做的事情就是为它想一个名字。这是最容易的部分，因为作为漫威的超级粉丝，只有一个真正的选择:贾维斯。

# 入门指南

我决定首先处理 CLI 的配置管理部分，因为大多数其他功能都依赖于此。我希望能够为我使用的所有不同类型的配置创建新配置、更新和删除特定配置，以及切换配置。

## Git 配置

我管理 git 配置的主要目标是能够创建 SSH 密钥，更新 SSH 配置，并以尽可能少的手工工作将公钥上传到 GitHub。我知道我可以使用 ssh-keygen 命令创建 SSH 密钥，传递所有必要的信息，比如文件名。我还知道我可以使用节点的 fs 模块更新 SSH 配置文件。我不确定的是我是否能把公钥上传到 GitHub，所以我做了一些研究。原来 GitHub 有一个方便的 API，允许添加新的密钥，也可以删除当前的密钥。现在我知道我可以让这一切工作起来——唯一需要手动完成的事情(在命令行之外)就是创建一个个人访问密钥。

现在如果你跑:

```
jarvis config-new git
```

Jarvis 将首先停用当前活动的 git 配置。然后它会提示您输入一个配置 id，您的 GitHub 用户名和电子邮件，以及您创建的个人访问令牌。贾维斯会帮你完成剩下的工作。它在本地存储配置以确保 id 是惟一的，创建 SSH 密钥，更新 SSH 配置，将公共 SSH 密钥上传到 GitHub，并激活配置以便立即使用。

说到激活配置，这是下一步。使用 Jarvis 在 git 配置之间切换非常简单:

```
jarvis config-switch git
```

当这个命令运行时，Jarvis 显示一个存储的配置列表以供选择。您所需要做的就是输入您想要切换到的配置的 id，并告诉 Jarvis 您当前正在进行的回购。一旦完成了这些，Jarvis 将设置正确的本地和全局 git 配置，并设置正确的远程 URL。

但是，如果，例如，你改变你的用户名呢？别担心，我已经使更新存储的配置变得很好很容易。您可能已经猜到了这个命令是什么:

```
jarvis config-update git
```

就像切换配置一样，Jarvis 会询问您想要更新哪个配置。一旦您告诉 Jarvis 要更新哪个配置，它将要求所有可以更改的属性的新值。如果您不想更改特定的属性，请按 enter 键，它将使用当前的属性值。

我原以为 update 命令会像这样简单，但是我很快意识到有几个问题。首先，如果您的用户名已经更改，远程 URL、本地和全局 git 配置将会是错误的。这不是一个大问题，因为我可以用更新后的属性激活配置，一切都会更新。第二个问题是，如果您的用户名已经更改，那么 SSH 密钥和配置将与新用户名不匹配，这将导致任何 git 请求失败。我决定通过完全删除本地和 GitHub 中的 SSH 密钥，并创建新的密钥来解决这个问题。

你可能想知道为什么我选择删除一切，而不是仅仅更新密钥和配置。我这样做是因为我认为这可能是一个方便的功能。例如，假设您怀疑您的一个密钥已经泄露，有人正在使用它来访问您的 GitHub 帐户——要阻止此人，您只需运行 update 命令，按几次 enter 键，泄露的密钥将不再存在于本地或 GitHub 上，并且会创建新的密钥。

最后，我需要一种删除不再使用的配置的方法。这与其他 config 命令的工作方式类似:

```
jarvis config-delete git
```

当您运行这个命令时，Jarvis 会询问您想要删除哪个配置，然后删除它。这很简单，只需在本地删除配置，从 GitHub 中删除公共 SSH 密钥，然后在本地删除 SSH 密钥和配置。如果被删除的配置是激活的配置，那么 Jarvis 也会要求您激活一个新的配置。

## GCP 配置

GCP 配置命令的工作方式与 git 命令基本相同。它们之间唯一真正的区别是 Jarvis 创建、更新和删除 gcloud 配置，而不是创建、更新和删除 SSH 密钥。我知道这不会太难实现，因为作为 gcloud CLI 的长期用户，我知道我可以使用`gcloud config configurations create/delete/activate`和`gcloud config set`命令完成以上所有工作。

我必须克服的唯一问题是删除命令——你不能删除一个活动的 gcloud 配置。我通过创建一个名为 jarvis-dummy 的 gcloud 配置来解决这个问题，jarvis 可以在删除配置之前激活这个配置。这样，即使您使用 Jarvis 删除您已经创建的每个 GCP 配置，它总是会完成。

## S3 配置

S3 配置管理是三个中最简单的。这是因为我不必在任何外部平台上与任何东西通信或管理任何东西。S3 配置只需要存储在本地，因为与 S3 的所有交互都将通过 aws-sdk 包完成，所以我需要的只是能够从存储的配置中读取值。

至此，Jarvis 的配置管理部分就完成了……嗯，算是完成了。我还认为有一种快速查看所有存储配置的方法会很有用。这就像读取存储的配置并在运行以下命令时将它们打印出来一样简单:

```
jarvis config-view git|gcp|s3|all
```

# 使用 Git

git 配置管理是我构建的三个组件中的最后一个，所以我决定继续运行 git，并构建 git 的其余功能。根据我的需求，我知道我需要四个命令:

```
jarvis git-push <version>
jarvis git-pull
jarvis git-clone <repoName>
jarvis git-init
```

让我们从`git-init.`开始，我想让这个命令简化 git 目录的初始化。它需要做的第一件事是运行`git init`，还需要运行`git remote add origin`来添加远程原点。我认为让这个命令激活一个 git 配置也是一个好主意，这样运行一个命令就可以让您完全准备好开始使用其他 git 命令。

`git-clone <repoName>`和`git-pull`都是命令，完全按照你的想法去做。我想把它们添加到 Jarvis 中，主要是因为输入完整的 git 命令很痛苦。例如，代替:

```
git pull git@github.com-username:username/repoName.git
```

您应该键入:

```
jarvis git-clone repoName
```

它们通过读取活动存储的 git 配置并使用其中的数据来格式化 git 命令。他们做的唯一额外的事情是在执行 git 命令之前检查活动 git 配置是否正确。

最后一个`git-push <version>`，这个命令是以类似拉和克隆的方式诞生的。当你可以输入一个命令时，为什么要输入三个命令呢？例如:

```
jarvis git-push 1.0.0
```

而不是:

```
git add -A
git commit -m "1.0.0"
git push -u origin master
```

就像拉和克隆一样，推也在执行任何操作之前检查活动 git 配置是否正确。当运行`git add`命令时，Jarvis 将默认添加所有有变化的文件。您也可以传入想要添加的文件，例如:

```
jarvis git-push 1.0.0 file.js
jarvis git-push 1.0.0 file1.js file2.js
```

# 反应

到目前为止，React 命令为我节省了大量时间。我们先来看看:

## 初始化反应

```
jarvis react-init <projectName>
```

使用这个命令，您可以在几分钟内完成一个基本的 react 项目，而不是花半个小时来完成所有设置。Init 从使用 Jarvis 内置的 git init 命令初始化 git 目录开始。git 初始化后，它开始创建以下结构的所有文件和文件夹:

```
build/
src/
    components/
        Container/
            Container.js
            container.less
            Container.test.js
            index.js
        Head/
            Head.js
            index.js
        views/
            Home/
                Home.js
                home.less
                Home.test.js
                index.js
            PageNotFound/
                index.js
                PageNotFound.js
                pagenotfound.less
                PageNotFound.test.js
    utils/
        Environment.js
    enzyme.js
    index.js
    index.less
    variables.less
.babelrc
.eslintrc
.gcloudignore
.gitignore
app.yaml
CHANGELOG.md
index.html
jest.config.js
package.json
README.md
webpack.config.js
webpack.dev.config.js
webpack.prod.config.js
```

我试图创建尽可能基本和标准的结构，因此它对尽可能多的开发人员有用——但是当然，每个人对项目结构都有自己的偏好。我想根据你的意见不断改进这个结构，让它变得更有用。所有的文件都填充了样板文件内容，允许测试运行，webpack 工作，以及一个包含主页和 404 页面的基本网站。

但是它并没有就此停止，一旦所有的文件和文件夹都被创建，Jarvis 将运行 npm install 并等待所有的包完成安装。然后它会问你是否想运行`npm run test`，如果你让它运行，它就会运行它们。我将它添加到命令中，以便您可以确保所有文件都已正确创建——如果它们都已创建，测试将会通过。测试运行完成后，Jarvis 将询问您是否想要构建并运行项目，如果您回答是，那么它将运行`npm run build`，webpack 将构建文件并提供服务。

总之，`react-init`允许你在几分钟内完成一个基本的 react 项目，然后直接开始构建很酷的东西。

## 反应创建

`react-create`命令和`git-push`命令存在的原因是一样的——当你可以键入一个命令时，为什么要键入一大堆命令呢？在这种情况下是:

```
jarvis react-create ComponentName
```

而不是:

```
mdkir ComponentName
cd ComponentName
touch ComponentName.js
touch ComponentName.test.js
touch componentname.less
touch index.js
```

这个命令还有一个额外的好处，就是用样板文件填充文件，让组件开箱即用。

## 反应部署

我在需求中提到，我想要一个命令，将 react 应用程序部署到 Google App Engine，将更改推送到 GitHub，并将静态文件上传到 S3，所有这些都在一个命令中，`react-deploy <version>`就是这样做的命令。

该命令首先运行测试，以确保一切正常。如果测试失败，命令也将失败。如果测试通过，那么它继续前进，使用 webpack 进行生产构建。这创建了几个文件，主要是`index.css.gz`和`index.js.gz`。就像测试一样，如果 webpack 无法构建应用程序，命令也会失败。接下来，我们使用 Jarvis 的`git-push`命令将更改推送到 GitHub。

一旦新版本被推送到 GitHub，Jarvis 就开始将应用程序部署到 Google App Engine。gcloud CLI 通过使用`gcloud app deploy`命令使这一点变得非常简单。目前，这个命令只部署到 GAE，因为 99%的时间我都必须部署到那里。我确实有计划在将来给这个命令添加更多的平台，但是这在优先级列表中是很低的，所以我在这个版本中没有提到它。

最后，Jarvis 使用 aws-sdk 包将 webpack 创建的 css 和 js 文件上传到 S3。一旦这些文件被上传，应用程序现在就已经部署好了，可以在万维网上使用了。在执行每一步之前，Jarvis 检查活动配置是否正确，以确保一切都在需要的地方结束。

# 打开和搜索网站

我添加的最后一个功能是快速打开和搜索网站的能力。如果你像我一样在开发时总是打开命令行，你可能会发现这个特性和我一样有用。这个特性唯一的缺点是它是 macOS 特有的。它通过使用带有浏览器名称和 URL 作为参数的`open`命令来工作。因此，不用离开命令行，打开浏览器，键入 URL，然后按 enter 键，您现在可以:

```
jarvis site-open <site>
```

贾维斯会为你做好一切。站点参数可以是两个不同的东西，它可以是网站的完整 URL，或者您可以键入 Jarvis 预定义的简化站点标识符之一。目前，以下 id 可用于`site-open`:

```
jarvis site-open so (Stack Overflow)
jarvis site-open npm (NPM)
jarvis site-open cocoa (CocoaPods)
jarvis site-open g (Google)
jarvis site-open awe (Awesome)
jarvis site-open gh (GitHub)
```

如果你想直接在网站上搜索某样东西，你可以使用`site-search`命令。当这个命令运行时，Jarvis 会在执行命令之前询问您想要搜索什么。使用这个命令，您不能只传入任何 URL，您必须使用预定义的简写。可用的速记员有:

```
jarvis site-search so (Stack Overflow)
jarvis site-search npm (NPM)
jarvis site-search g (Google)
jarvis site-search gh (GitHub)
```

一旦你输入了你想要搜索的内容，Jarvis 将会打开一个 Chrome 标签，显示搜索结果。这个命令加速了我的工作流程，比我想象的要快，我现在一直在使用它。

# 下一步是什么？

贾维斯是我会继续努力的，我对未来有一些大的计划，其中一些我已经在这篇文章中提到了。我还将贾维斯上传到了 GitHub 和 NPM，这样任何人都可以使用他并贡献他的能力。我和 Jarvis 的目标之一是建立一个强大的社区，每个人都可以分享他们的想法和需求，这样 Jarvis 就可以在日常工作中帮助尽可能多的人。

# 安装 Jarvis

如果您想安装 Jarvis，您可以从 GitHub 克隆它:

```
git clone https://github.com/willptswan/jarvis.git
cd jarvis
npm link
```

或者使用 NPM 安装它:

```
npm install -g @willptswan/jarvis
```

您可以通过运行以下命令来检查 Jarvis 是否已经正确安装

```
jarvis version
```

或者

```
jarvis help
```

# 结论

我希望你喜欢阅读贾维斯为什么以及如何存在，就像我喜欢建造他和写这个故事一样。我期待着贾维斯的未来，并看到一个社区在他周围成长。

GitHub:[https://github.com/willptswan/jarvis](https://github.com/willptswan/jarvis)

https://www.npmjs.com/package/@willptswan/jarvis:[NPM](https://www.npmjs.com/package/@willptswan/jarvis)