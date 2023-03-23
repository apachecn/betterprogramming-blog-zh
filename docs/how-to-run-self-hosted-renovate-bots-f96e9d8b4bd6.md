# 如何运行自托管更新机器人

> 原文：<https://betterprogramming.pub/how-to-run-self-hosted-renovate-bots-f96e9d8b4bd6>

## 使用这 12 个技巧，通过更新实现依赖关系的自动化更新

![](img/051da2a6589e6744bc51afc2bcfeb44b.png)

由 [Arturo Esparza](https://unsplash.com/@arturoeg_foto?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

更新依赖关系很无聊。尽管它很重要，但我们总是找借口避免更新它们，比如“如果它没坏，就不要修复它”或者“还有更重要的功能需要开发”。随着时间的推移，项目的可维护性会降低。团队以一个 3 年的依赖结束，没有人有足够的勇气去打破它。

那么，有什么解决办法呢？让机器人做我们无聊的工作吧！

在本文中，以 [GitLab](https://gitlab.com/) 为例，您将学习运行自托管[renewal](https://github.com/renovatebot/renovate)bot 的技巧。如果你正在寻找如何在 GitHub 上开始使用 read 的指南，我强烈推荐你[阅读这个](https://www.freecodecamp.org/news/update-dependencies-automatically-with-github-actions-and-renovate/#Getting-Started)。

## TL；速度三角形定位法(dead reckoning)

*   为什么使用翻新
*   开始自主更新(以 GitLab 为例)
*   如何在当地经营翻新
*   调试修复作业
*   12 个有用的改造机器人技巧

免责声明:这些建议是我从工作时间中收集的经验教训中得出的个人观点。尽情享受吧！

# 为什么使用翻新

简而言之，renew([正式文档](https://docs.renovatebot.com/))有助于自动更新项目依赖关系(私有或第三方)。

怎么会？Renovate 解析我们项目的依赖关系管理文件，例如`package.json`、`pyproject.toml`、`go.mod`文件，并相应地使用更新的依赖关系提出一个拉/合并请求(PR/MR)。

Renovate 通过一个简单的配置文件(`config.js`)高度可定制。凭借相当直观的配置设置，它还支持[广泛的软件包管理器](https://docs.renovatebot.com/modules/manager/#supported-managers)。

今天，Renovate 支持许多其他 Git 平台，如 Bitbucket、Gitea，甚至 Azure DevOps。最重要的是，它被许多流行的开发社区或公司使用，如优步、GitLab、Netlify、Apollo GraphQL 等。

## 高光

Renovate 最好的部分是它自动合并 PRs/MRs 的能力。

除此之外，`[packageRules](https://docs.renovatebot.com/configuration-options/#packagerules)` [特性](https://docs.renovatebot.com/configuration-options/#packagerules)提供的灵活性非常方便，它用于将规则应用于特定的依赖项(在个人或组中)。

# 在 GitLab 上运行自托管更新

要在自托管 GitLab 上运行 Renovate，您需要一个私有 GitLab 项目([即下面的存储库](https://about.gitlab.com/blog/2016/01/27/comparing-terms-gitlab-github-bitbucket/))。这个 bot 资源库将用于托管为您的其他项目运行 renewal 的 [GitLab runners](https://docs.gitlab.com/runner/) 。

接下来，假设您已经安装并设置了 GitLab runners，您将需要 bot 项目中的以下内容:

1.  配置以下 CI/CD 变量:

*   `RENOVATE_TOKEN` — GitLab [个人访问令牌](https://docs.gitlab.com/ee/user/profile/personal_access_tokens.html#creating-a-personal-access-token) (PAT)，范围:`read_user`、`api`、`write_repository`。
*   `GITHUB_COM_TOKEN` — [GitHub PAT](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) 最小范围。

2.`config.js`(注:这里有一个[更复杂的例子](https://gitlab.com/gitlab-org/frontend/renovate-gitlab-bot/-/blob/main/renovate/config.js))。

3.`.gitlab-ci.yml`。如果你需要一个更复杂的例子，看看 GitLab 团队的这个例子。否则，这里有一个最小的例子；请相应更新:

4.最后，您需要使用 GitLab 项目的 [CI/CD Schedules](https://docs.gitlab.com/ee/ci/pipelines/schedules.html) 特性定期(例如每小时)运行 renew。请注意，这与 renewal 自己的[时间表](https://docs.renovatebot.com/configuration-options/#schedule)不同。

查看[源](https://gitlab.com/renovate-bot/renovate-runner)了解更多详情。不过，上述步骤应该足够了。

# 如何在当地经营翻新

当我在处理一个大型存储库(完整克隆大于 6GB)时，每个更新工作可能需要几个小时才能完成。能够在本地运行 renew 节省了我大量的实验和调试时间。

首先，创建一个[最小可重复范例(MRE)库](https://github.com/renovatebot/renovate/blob/main/docs/development/minimal-reproductions.md#how-to-create-a-good-minimal-reproduction)。然后，更新您的`config.js`来定位或发现 MRE 存储库。要运行本地修复:

1.  只使用 [Docker](https://www.docker.com/) 要容易得多。所以，确保你已经安装了 Docker。
2.  首先，您需要确保在您的 shell 中导出这些环境变量:

```
export RENOVATE_TOKEN="aa11bb22cc" # GitLab Personal Access token (PAT)
export GITHUB_COM_TOKEN="cc33dd44ee" # GitHub PAT
```

3.接下来，相应地更新您的`config.js`。你需要相应地更新你的目标`repositories`。

4.最后，您可以使用以下代码运行 renew。要获取最新的更新版本，请查看 [Docker hub](https://hub.docker.com/r/renovate/renovate/tags) 。请相应地更改以下命令:

```
docker run \
    --rm \
    -e LOG_LEVEL="debug" \
    -e GITHUB_COM_TOKEN="$GITHUB_COM_TOKEN" \
    -v "/path/to/local/config.js:/usr/src/app/config.js" \
    renovate/renovate:"32.6.12" \
    --token "$RENOVATE_TOKEN" \
    --dry-run="true"
```

这样，以快速反馈回路的方式测试更新的配置现在是可能的。如果您需要更全面的日志，请尝试设置`LOG_LEVEL=”trace”`。

要执行实际运行，请更新`--dry-run="false"`。

# 12 个有用的改造机器人技巧

## 一般提示

1.  不确定如何快速获得翻新的最大收益？看看这个[改造 bot 小抄](https://www.augmentedmind.de/2021/07/25/renovate-bot-cheat-sheet/)而不是[官方改造文档](https://docs.renovatebot.com/)的冗长。
2.  如果你不确定 Renovate 是否支持某个功能，总是先查看他们的 [FAQ](https://docs.renovatebot.com/faq/) 页面。很可能它已经在那里了。
3.  要禁用特定包或库的更新，只需在相应的`[packageRule](https://docs.renovatebot.com/configuration-options/#packagerules)`下设置`enabled: false`。示例:

4.升级后需要运行一些自定义任务或脚本(例如，向 Slack 发布消息的脚本)？[试试](https://docs.renovatebot.com/configuration-options/#postupgradetasks) `[postUpgradeTasks](https://docs.renovatebot.com/configuration-options/#postupgradetasks)`。

5.将特定于项目的更新配置保存在 bot 存储库中，而不是将`renovate.json`保存在其他存储库中。为此，将`[onboarding: false](https://docs.renovatebot.com/self-hosted-configuration/#onboarding)`设置在`module.exports`下。这允许将与翻新相关的配置抽象到一个单独的存储库中。

## 调试修复作业的提示

当谈到自托管解决方案时，调试自己的工作是不可避免的。可能会有这样的场景，您需要测试到您的私有注册中心、代理等的连接。

这里有一些有用的提示:

1.  尝试在本地运行 renew，以获得更快的反馈循环，而不是依赖于您的 CI/CD 管道。另外，在本地跑步几乎是免费的！
2.  使用`trace`T5，以防`debug`没有给你足够的信息。
3.  如果你正在处理一些定制的用例或面临奇怪的遭遇/错误，可以尝试使用前缀“*site:github . com/renovate bot/renewal<你的关键字或短语>* ”进行谷歌搜索。([例](https://www.google.com/search?q=site%3Agithub.com%2Frenovatebot%2Frenovate+wrong+update+version+for+go.mod&ei=6xpJYua5CbiNseMPzbOy2A0&ved=0ahUKEwim2_r3__b2AhW4RmwGHc2ZDNsQ4dUDCA4&uact=5&oq=site%3Agithub.com%2Frenovatebot%2Frenovate+wrong+update+version+for+go.mod&gs_lcp=Cgdnd3Mtd2l6EANKBAhBGAFKBAhGGABQ0CdY2Edg70hoBHAAeAGAAdoBiAGQDZIBBjMyLjAuMZgBAKABAcABAQ&sclient=gws-wiz))。在大多数情况下，你会发现已经有其他人提出了类似的讨论或问题。

## 处理大型存储库(例如，GB 大小)

1.  如果您试图在一个大型存储库上运行 renew，您可能会由于超时而遇到一个`SIGTERM`信号(可以在您的 renew 作业日志中看到)。为了解决这个问题，在你的`config.js`中增加`[executionTimeout](https://docs.renovatebot.com/self-hosted-configuration/#executiontimeout)` [设置](https://docs.renovatebot.com/self-hosted-configuration/#executiontimeout)。例如:

2.在 GitLab 项目的 CI/CD 通用管道设置下，您可能希望增加作业超时(例如 10 小时),因为在大型存储库上无缓存运行时，更新可能需要很长时间。

3.你可能想在两次跑步之间设置`persistRepoData: true`为更快的`git fetch`。例如:

4.在某些场合，你可能会碰到`ERROR: Disk space error - skipping`。在这种情况下，您可能希望为 runner 提供更大的磁盘空间。例如，如果您使用 AWS EC2，请尝试增加卷的大小。

# 结论

在过去的几个月里，我一直在广泛使用 renew bot；在 GitHub 上，还有自托管的 GitLab。今天，我在 GitHub 上的大多数活动项目中使用 Renovate 来自动更新依赖项。到目前为止，renew 能够适应我 99%的用例。

让我们的项目依赖保持最新常常被忽视。然而，忽视这样的工作往往要付出巨大的代价。事实上，我们可以利用 Renovate 的能力来完成这种繁重的工作是一件好事。

我希望这篇文章能为您节省足够的时间，而不是让您浏览 GitHub 讨论和问题的页面。感谢阅读！

```
**Want to Connect?**This article was originally published on [jerrynsh.com](https://jerrynsh.com/12-tips-to-self-host-renovate-bot/)
```