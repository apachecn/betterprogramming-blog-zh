# 如何让你的应用依赖保持最新？

> 原文：<https://betterprogramming.pub/how-to-keep-your-app-dependencies-up-to-date-833fc45dae4>

## 有哪些挑战，如何正确地自动化维护？

![](img/dfef445e5b285af4d6ade2ea1de7819e.png)

由[法鲁拉斯米](https://unsplash.com/@fahrulazmi)在 [Unsplash](https://unsplash.com/photos/BnWDqUCWQDU) 上拍摄的照片

软件开发的一个方面经常被忽视:维护依赖关系。我不会说谎，在很长一段时间里，我和我的团队都是如此。

# 为什么要花时间在维护上？

保持过时的依赖关系不仅会造成不兼容，或者看起来不酷。它提出了两个大问题:安全性和性能。

还记得 [Equifax 事件](https://www.google.com/amp/s/www.wired.com/story/equifax-breach-no-excuse/amp)吗？好吧，这是一笔本可以避免的 4.25 亿美元的和解金，但如何避免呢？Apache Struts 的副总裁 René Gielen 说:

> 我们意识到的大多数违规行为都是由于未能更新软件组件造成的，而这些组件在数月甚至数年内都是易受攻击的

是的，维护依赖关系对性能也有很大的影响。带有[反应本地导航](https://medium.com/opendoor-labs/how-we-improved-app-performance-and-code-quality-by-upgrading-react-navigation-25ccd8363432)的用例就是一个很好的例子。它展示了最新版本如何带来更高的性能，同时也有助于提高代码质量。

[](https://medium.com/opendoor-labs/how-we-improved-app-performance-and-code-quality-by-upgrading-react-navigation-25ccd8363432) [## 我们如何通过升级 React Navigation 来提高应用性能和代码质量

### 深入探讨 Opendoor 如何升级我们最重要的第三方库&在不重写的情况下提高性能…

medium.com](https://medium.com/opendoor-labs/how-we-improved-app-performance-and-code-quality-by-upgrading-react-navigation-25ccd8363432) 

# 挑战

当升级依赖关系时，有一个主要问题:**时间**。

您必须了解每一个依赖项更新，并应用必要的更改。对于大型应用程序来说，在同一天进行多次更新并不罕见。

*应用必要的变更*意味着:

*   更新您的配置(package.json with NodeJS，Maven config with Java，或者 pip for Python，..)
*   通常将升级后的配置发送到 GIT 存储库
*   如果您希望您的应用程序能够工作，那么在将它们添加到您的主工作之前，您需要测试您的新配置(有时还需要进行更改)。

有些工具允许您获取要更新的依赖项列表。例如 NPM 有一个[内置命令](https://docs.npmjs.com/cli/v8/commands/npm-outdated)。其他社区工具也有助于达到同样的效果，但功能更多，比如 [npm-check-updates](https://www.npmjs.com/package/npm-check-updates) 。

在这里你面临第二个挑战:这个更新**会打破什么**？

 [## 语义版本 2.0.0

### 给定主要版本号。补丁，增加:主要版本当你做不兼容的 API 改变，次要…

semver.org](https://semver.org/) 

包经理要求开发人员遵循一个版本控制系统( [NPM](https://docs.npmjs.com/about-semantic-versioning) 、[玛文](https://docs.oracle.com/middleware/1212/core/MAVEN/maven_version.htm)、[皮普](https://www.python.org/dev/peps/pep-0440/))。这通常至少包括一个次要版本和一个主要版本。次要版本指定一个新功能，而主要版本表示一个 [*突破性变化*](https://en.wiktionary.org/wiki/breaking_change) 。

这就是为什么经常使用版本范围:您指定您的依赖版本，但默认情况下接受较新的、较小的更新。所以我们可以放心地使用新的小更新，对不对？嗯，没有。

> 您可以像创建库的开发人员一样，确信小的更新不会破坏任何东西。一句忠告:不要。

这意味着，对于每次更新，您需要验证没有任何损坏。通过手动测试，任何项目都有可能不需要建立专门的架构和过程。但这是正确的解决方案吗？

对于一个中等规模的应用程序来说，每天列出和更新过时的依赖项已经是一件非常困难的事情了。添加手动测试是不现实的，你既没有时间去做，也不能避免每次都打破常规。

# 解决办法

一个词:自动化。这是使这个过程不仅可以忍受，而且现实的唯一方法。

## 获取过期的依赖关系

首先也是最重要的，我们需要定期获取需要更新的依赖项。已经有几个很好的解决方案，可以集成到 GIT 提供者中。

配置完成后，它们会为新的依赖项创建一个带有更新配置的拉请求。反过来，您可以选择将它们合并到您的主分支中。我在搜索过程中考虑了两个工具: [renovatebot](https://docs.renovatebot.com/) 和[dependent bot](https://docs.github.com/en/code-security/supply-chain-security/keeping-your-dependencies-updated-automatically/enabling-and-disabling-dependabot-version-updates)。

两者都是很好的工具，可以与多种语言一起工作。Dependabot 的优势是可以直接集成到 Github 中。另一方面，renew 可以与多个提供商合作。

我的旅程是从 GitLab 开始的，所以我选择了 renew，并继续这样做，即使我现在正在 Github 上使用它。如果您正在使用 Github，请放心使用 Dependabot！

## 固定依赖关系

在讨论依赖项更新的相关问题之前，我们先来讨论一下固定依赖项。Renovate 发出的第一个拉请求是**锁定**您的依赖项:它是什么，为什么？

renewal 有一个很棒的部分是关于[版本范围和固定依赖](https://docs.renovatebot.com/dependency-pinning/)。固定依赖关系意味着使用依赖关系的精确版本，而不是范围。还记得我关于不要盲目信任正在创建库的开发人员的建议吗？

*我敢打赌，99.9%的读者在他们的旅途中都会遇到以下问题。*

你在一个项目上工作了一段时间，但是生活发生了，你把它搁置了，一切都工作得很好，并且保存在你最喜欢的 GIT 提供者上。几个月后，您克隆了您的存储库，并再次开始工作，但是请注意:您无法构建您的应用程序，某些东西已经坏了。

怎么会？这就是版本范围的危险。在某个时候，一个库创建者发布了一个错误的版本。也许这使它与另一个库不兼容，或者它不能与你的代码一起工作。

版本范围是个好主意，理论上是*。请使用精确的版本，或者换句话说，固定您的依赖项。*

## *避免破坏性的改变*

*在这一点上，我们应该理解并接受任何依赖关系更新*可能*破坏某些东西。*

*实际上，更新一个依赖项与添加一个新特性没有什么不同。软件工程已经有了确保一切正常运行的技术，我个人使用的是:*

*   *静态分析*
*   *单元/集成测试*
*   *e2e 测试*
*   *有时候，手工测试*

*因为依赖关系更新工具处理拉请求，所以运行检查/测试非常容易。这允许完整和快速的反馈:这个更新破坏了什么吗？此外，在将任何东西合并到我们的主要工作之前，我们确保一切正常。*

*实际上，这要归功于 CI。在 renew 案例中，用名称`renovate/<dependency>`创建分支。CI 可以被配置为在那些特定的分支上自动运行语法检查、单元/集成或 e2e 测试。*

*有时，我们想要更多的信心，例如关于一个重要库的主要版本。可以部署应用程序，允许在需要时进行简单的手动测试。*

*大多数更新不需要手动测试，这意味着一切都可以自动化，甚至合并依赖更新！*

*[](https://docs.renovatebot.com/configuration-options/#automerge) [## 配置选项

### 本文档描述了您可以在更新配置文件中配置的所有配置选项。任何配置…

docs.renovatebot.com](https://docs.renovatebot.com/configuration-options/#automerge) 

没错，如果测试成功，大多数依赖项更新工具可以被配置为自动合并拉请求。当自动化测试不够时，对于最重要的库，或者对于开发工具，可以给出例外。

如何保证[哈士奇](https://github.com/typicode/husky)的一个主要版本不坏你的配置？那么[指挥官](https://github.com/conventional-changelog/commitlint)呢？可以接受自动合并带来的风险，或者添加例外。

## 建议

将依赖项更新到最新版本并不一定会提高安全性和性能。

偶尔，它会引入一个漏洞。如果你运气不好，构建系统的变化可能会给你的包增加很大一部分，从而降低性能。

这不是避免更新的理由:新版本可能会引起问题，旧版本会更糟。这意味着应该使用性能测试和安全工具。

例如， [lighthouse](https://developers.google.com/web/tools/lighthouse) 的多次运行之间的巨大差异可能表明存在性能问题。

另一方面，定期运行 [snyk](https://snyk.io/) 是一个很好的安全实践。

# 实践中的相关性更新

在写这几行的时候，我有几个托管的应用程序。他们是用 React 做的(用 [CRA](https://create-react-app.dev/) 、[盖茨比](https://www.gatsbyjs.com/)，或者[下一个](https://nextjs.org/))、 [React Native](https://reactnative.dev/) 、[Electron](https://www.electronjs.org/)([React Forge+React](https://www.electronforge.io/guides/framework-integration/react))。

都配置了 renew，托管在 Github 上。我和 [CircleCI](https://circleci.com/) 合作，因为它性能好，而且不太贵。CircleCI 被配置为在所有的`renovate/<dependency>`分支上运行测试，并将一切部署到测试环境中。

启用自动合并，运行以下测试，并在一切成功的情况下合并依赖项更新。对于我想在升级前手动验证的特定库，添加了一些例外。

## 单元测试和语法

无论使用哪种技术，第一次测试总是相同的:

*   所有这些应用程序都是用打字稿制作的。我的第一个语法检查使用了带有`--noEmit`标志的 [tsc](https://www.typescriptlang.org/docs/handbook/compiler-options.html) ，以及 ESLint 和 Prettier。
*   然后，我的单元/集成测试正在运行。这些都是感谢[测试库](https://testing-library.com/)写的。

从一般的角度来看，过程的第二部分是相同的，但是技术堆栈不同。

## 端到端

对于用 CRA、盖茨比或 Next 编写的应用程序， **e2e 测试**多亏了 [Cypress](https://www.cypress.io/) 才能运行。

柏树不能用于反应原生生物，并且不再支持电子。

我选择了 React Native 的 [Detox](https://github.com/wix/Detox) ，因为根据我的经验，这是我最喜欢的。对于电子来说， [Spectron](https://github.com/electron-userland/spectron) 现在被弃用，但是[弃用公告](https://github.com/electron-userland/spectron/issues/1045)建议[编剧](https://playwright.dev/)。

所有这些都可以很容易地配置为在 CircleCI 上运行。React Native 存在一个问题，它需要 Android 和 IOS 环境才能运行。

幸运的是，CircleCI 提供了 [Android](https://circleci.com/docs/2.0/android-machine-image/) 和 [IOS](https://circleci.com/docs/2.0/testing-ios/) 执行器。

这样，我能够在正确的环境中运行我的所有测试，让我有足够的信心允许自动合并。

## 部署

我需要在两种情况下访问我的应用程序的生产版本:

*   对于我的主分支，要有最终的结果。
*   对于为更新特定依赖项而创建的分支(禁用自动合并)。

根据我使用的技术，我使用不同的服务。

[Netlify](https://www.netlify.com/) 是用 CRA 或盖茨比等制作的静态应用程序的一个很好的解决方案。它可以配置为部署您的主分支，以及针对您的主分支发出的所有拉请求。

理论上，Netlify 应该能够部署下一个应用程序，但实际上，它几乎不稳定。令人欣慰的是， [Vercel](https://vercel.com/dashboard) 提供了相同的功能，集中在 Next。

对于 React Native，Android 和 IOS 的应用程序构建在管道上，并作为工件上传。这允许我在需要时下载它们，并在我的设备上测试它们。

最后，带有电子的应用程序也作为我们可以下载的神器上传。

# 结论

现在，您已经了解了中型和大型应用程序如何保持它们的依赖关系是最新的。实用的解决方案应该可以帮助你设置它。

请随意分享您的想法以及您个人是如何处理这些问题的！*