# GitHub 操作的 3 种简单入门方法

> 原文：<https://betterprogramming.pub/3-simple-ways-to-get-started-with-github-actions-5c511cfa1744>

## 从 GitHub 中自动化您的开发人员工作流程

![](img/935b8715d93ebe4a71e4319713f04e85.png)

罗曼·辛克维奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

GitHub Actions 是在 2020 年向 GitHub 上的每个用户开放的，所以它们还是相对较新的。你知道你每个月有 2000 分钟的空闲时间用于你的私人存储库吗？更好的是，公共存储库免费获得**无限分钟。**

你甚至不需要自己构建大部分动作，GitHub 有一个充满免费动作的市场供你使用。那么，你可以利用空闲时间做些什么简单的事情呢？

# 代码林挺

林挺允许你自动检查你的代码的编程和风格问题。如果代码以一致的方式编写，会更容易阅读，所以 linters 旨在为您的代码提供一致的风格。

在 PR 合并到主分支之前运行代码林挺是 GitHub 动作的一个极好的用例，而且做起来超级简单！下面是我对 Ruby 存储库的做法:

```
name: Ruby

on:
  pull_request:
  workflow_dispatch:

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Ruby
      uses: ruby/setup-ruby@v1
      with:
        ruby-version: '2.7.1'
        bundler-cache: true
    - name: Run Rubocop Linting
      run: bundle exec rubocop
```

GitHub 动作是用 YAML 定义的。由于这是我们看到的第一个 GitHub 动作，我将一步一步地介绍:

```
name: Ruby
```

从简单开始，这是这个特定 GitHub 动作的名称。

```
on:
  pull_request:
  workflow_dispatch:
```

接下来，我们定义哪些事件应该触发 GitHub 动作。在这种情况下，我们选择在打开、重新打开或提交请求时运行这个操作。这些是默认的，但是您可以在许多与拉请求相关的事件上触发事件，您可以在这里看到。

我们指定的第二个事件是 workflow dispatch，它允许我们从存储库的 Actions 选项卡中手动运行操作。我发现这在测试一个动作时特别有用。

```
jobs:
  lint:
    runs-on: ubuntu-latest
```

既然我们已经定义了操作应该何时运行，我们可以指定操作应该做什么。动作被分解成作业(每个动作可以定义多个)。在本例中，我们有一个名为 lint 的作业，我们必须定义在什么环境中运行 GitHub 操作。

我选择了 Linux，但也有 Windows 和 macOS。这些跑步者都由 GitHub 托管，使得每个人都可以非常容易地访问它们，但是如果你需要更强大的跑步者，或者需要特定的硬件或工具，你可以[自托管跑步者](https://docs.github.com/en/actions/hosting-your-own-runners/about-self-hosted-runners)。

```
- uses: actions/checkout@v2
- name: Set up Ruby
  uses: ruby/setup-ruby@v1
    with:
      ruby-version: '2.7.1'
      bundler-cache: true
- name: Run Rubocop Linting
  run: bundle exec rubocop
```

每个作业可以有多个步骤，定义为一个列表。大多数操作总是从由 GitHub 构建的 checkout 操作开始，它会将您的存储库签出到 runner 中。

然后我们进入这个行动的细节。首先，我们必须安装 Ruby，这也是一个可以使用的预配置动作。我们可以将参数传递给动作，比如 Ruby 版本。bundler-cache 参数将安装依赖项(Ruby 中的 gems)。

最后，我们可以 lint 我们的代码。Rubocop 是 Ruby 的 linter，一旦运行，要么产生一个 0 退出代码(成功)动作通过，要么产生一个 1 退出代码(失败)动作失败。如果有失败，可以在运行器的输出中查看。你可以在这里看到一个跑步者跑[的例子。](https://github.com/apeacock1991/devise/actions/runs/1802066006)

## 使用 GitHub 动作作为状态检查

如果我们只是在后台运行这个动作，也没多大用。工程师在合并他们的 PR 之前查看 Actions 选项卡的机会非常小。

然而，GitHub 允许你定义拉请求的[状态检查](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/collaborating-on-repositories-with-code-quality-features/about-status-checks)。可以将这些状态检查配置为仅在通过所有状态检查后才允许合并。

因此，工程师不必记得检查动作，我们可以确保林挺在 PR 被合并之前通过。这里有一个[公关](https://github.com/apeacock1991/devise/pull/1)的例子，目前由于林挺失败而被封锁。

# 测试应用程序

如今，大多数应用程序和库都有某种形式的自动化测试。有很多工具可以运行您的测试，但是为什么不使用 GitHub 操作呢？

下面是我如何为一个 m C#存储库运行我的测试:

```
name: .NET

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Setup .NET
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: 3.1.x
    - name: Restore
      run: dotnet restore
    - name: Build
      run: dotnet build --no-restore
    - name: Test
      run: dotnet test --no-build --verbosity normal
```

我不会像上次一样重复每一步，因为我们现在知道了动作的基本结构。尽管如此，还是有一些不同之处:

```
on:
  push:
    branches: [ main ]
```

首先，我们在每次推送时都运行测试——所以即使没有发出拉取请求，我们仍然在运行测试。这是非常宝贵的，因为工程师们通常希望在提交 PR 之前看到测试结果。

虽然我建议在每个分支的每次推送中都运行它，只是为了展示 GitHub 操作的灵活性，但是我已经指定了该操作只应该在主分支上运行。您还可以[指定路径](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#onpushpull_requestpull_request_targetpathspaths-ignore)，导致该操作仅在某些文件被更改时运行。

```
- name: Setup .NET
  uses: actions/setup-dotnet@v1
    with:
     dotnet-version: 3.1.x
- name: Restore
  run: dotnet restore
- name: Build
  run: dotnet build --no-restore
- name: Test
  run: dotnet test --no-build --verbosity normal
```

第一步是安装。GitHub 自己也为其提供了一个动作。那个动作使。NET CLI，我们可以在那里构建和运行我们的测试。

与上面的林挺类似，相关的退出代码将根据测试是通过还是失败而返回。[这里有一个例子](https://github.com/apeacock1991/sustainable-api-architecture/actions/runs/1802057704)它运行和传递。与林挺类似，我们可以使用状态检查来确保只有通过绿色测试的分支才能被合并。

# 扫描应用程序的漏洞

最后，我要介绍的最后一个简单用例是扫描您的存储库的漏洞。这次我们将利用 [GitHub 市场](https://github.com/marketplace?type=actions)的力量，因为使用动作的一大卖点是你可以立即使用的免费动作越来越多。

我将使用 [Snyk 的 GitHub 动作](https://github.com/marketplace/actions/snyk)，因为我发现它很容易设置，并且提供对许多语言的支持——包括 Node，。NET，Scala 和 Docker。

你可以在这里注册一个免费的 Snyk 账户[来开始(更大的组织可以使用付费计划)。您需要一个帐户来访问您的 API 令牌。一旦有了 API 令牌，设置动作就很简单了:](https://snyk.io/plans/)

```
name: Synk Security Checkson:
  push:jobs:
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@master
      - name: Run Snyk to check for vulnerabilities
        uses: snyk/actions/node@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
```

我以 Node 为例，但是您可以将动作更改为任何支持的语言。这个动作引入了一个新概念，那就是环境变量。

为了运行，这个动作需要设置`SNYK_TOKEN`环境变量。我们不想在存储库中以纯文本形式存储机密，但是您可以设置机密，方法是转到存储库，单击“设置”，然后在左侧菜单中，选择“机密”和“操作”。

在该页面中，您可以定义由机密名称及其相应值组成的键-值对。一旦保存，您就可以使用`${{ secrets.YOUR_SECRET_NAME }}`访问正在运行动作的存储库的所有机密。

# 最后的想法

希望您现在对 GitHub 操作有了基本的了解。这只是一个开始，随着采用的增加，我希望可用操作的数量会显著增加。

我们在本文中看到的操作非常简单——但是非常有效——但是我知道有些公司在 GitHub Actions 上运行他们的整个部署管道，所以更复杂的用例也适合他们。如果你被说服了，我建议你去看看 GitHub 的完整文档。

```
**Want to Connect?**I run a free newsletter providing fortnightly technical book recommendations, including my key takeaways from the books. Interested? [Sign up here!](https://subscribe.technicalbookclub.com/?utm_source=medium&utm_medium=article&utm_campaign=simplegithubactions)
```