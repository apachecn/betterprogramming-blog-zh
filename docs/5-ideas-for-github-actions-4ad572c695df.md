# GitHub 行动的 5 个想法

> 原文：<https://betterprogramming.pub/5-ideas-for-github-actions-4ad572c695df>

## 为您的项目带来更多自动化

![](img/be348ed520b4d053aaa9e0c6d1e8b9d1.png)

马文·迈耶在 [Unsplash](https://unsplash.com/s/photos/creative-agency?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

几年前，GitHub 宣布了一个新功能，GitHub Actions。这是 GitLab 成功的答案。因为 GitLab 提供了许多开发特性，比如开箱即用的持续集成。

微软支持的 GitHub 打了个赌，成功了。GitHub Actions 不仅仅是一个持续集成工具。这是一个自动化工具，在[市场](https://github.com/marketplace?type=actions)，每个人都可以分享他们的经验。

你可以在市场上找到很多有趣的 GitHub 动作。它可以是琐碎的代码质量检查或复杂的安全补丁。使用以下想法作为灵感来改进您的开发工作流程。

# 自动化部署

持续集成(CI)和持续交付(CD)是现代软件开发的重要部分。CI 通过运行所有测试来确保新代码被更快地集成。同时，CD 使您的更改更快生效。

连续交付的简化工作流程如下。在每次推送到主分支时，启动到集成或生产环境的部署过程。

实际上，现代应用程序并没有那么简单。如果我们的应用程序在云中，我们必须首先设置凭证。如果应用程序是容器化的，就必须发布 Docker 映像的新版本。我们还可以为每个环境指定配置。

上面的所有步骤都可以用 GitHub 动作自动化。因此，从外部看，您的部署确实很简单。

# 创建发布和变更日志

在上一步中，我提到了发布 Docker 映像的新版本。这一步可以让你认识到发布任何东西都是可以自动化的。

如果您正在构建一个移动应用程序、一个实用工具或一个库，在发布时通常会有一个清单。必须将产品的新版本上传到存储库中。它需要有一个正确的版本标签和变更日志。

有大量的[动作](https://github.com/marketplace?category=publishing&page=1&type=actions)与将工件发布到不同的平台相关。此外，您可以找到[解决方案](https://github.com/marketplace/actions/release-changelog-builder)来自动化您的变更日志生成。

# 自动贴标机

使用 GitHub 动作的另一个想法是自动化[标记](https://github.com/marketplace/actions/labeler)过程。我们什么时候需要这个？

让我们考虑下面的场景。你的项目有一个很好的结构，每个团队将他们的工作封装在一个特定的文件夹中。这使得团队成为该领域的代码所有者。

如果有人创建了影响特定文件夹中的文件的拉请求，那么该拉请求必须被分别标记。我们将概述与每个团队相关的变化。或者我们可以警告工程师代码有多重要，需要采取哪些额外的措施。

# 安全性

安全性仍然是每个应用程序最重要的主题。我们希望确保遵循最佳实践，并保证源代码的安全。GitHub Actions 也可以帮助解决这个问题。

可以为您的项目配置安全检查。例如，我们希望确保源代码中没有任何[密码](https://github.com/marketplace/actions/check-password)。该策略可以强制只在专用文件中拥有凭证。还可以设置漏洞检查。自动化可以保证应用程序和支持依赖项 100%合规。

# 移动设施

几年前，我实现了一个 bash 脚本来检查。每个 pull 请求的 apk 文件大小。如果大小超过阈值，构建将失败，开发人员必须检查安装文件增加的原因。

如果您的项目需要这个特性，您不需要编写自己的脚本。微软有一个[专用的](https://github.com/marketplace/actions/android-app-size-difference) GitHub 动作。

移动开发者为 Android 和 iOS 项目找到了许多方便的操作。像代码混淆、用证书签署二进制文件以及将它们分发给 beta 测试人员这样的常规步骤可以自动化。

无需配置多个工具，GitHub Actions 正在成为自动化的单一入口。

如果你觉得没有适合你需求的 GitHub 动作，不要失望。提供的 API 允许任何人创建 GitHub 动作。如果你觉得有人可以受益，这是一个在市场上与他人分享的好机会。分享你的自动化想法！

```
**Want to Connect?**Do you want to grow professionally as a software engineer? Are you curious about how to achieve the next level in your career? I compiled a comprehensive list of actions in my book “[Unlock the Code](https://www.amazon.com/dp/B098LNMFCJ/).”
```