# 带有 GitHub 操作的独立 Mac 应用程序 DevOps

> 原文：<https://betterprogramming.pub/indie-mac-app-devops-with-github-actions-b16764a3ebe7>

## 自动化测试、签名、公证和分发

![](img/ba038ccad386d83680070027a0a23fc3.png)

[雷锐](https://unsplash.com/@ray30?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

你在考虑为 [macOS](https://www.apple.com/macos/) 写一个应用吗？你想使用[自制软件](https://brew.sh)在 [Mac 应用商店](https://developer.apple.com/app-store/mac/)之外发布它吗？在这篇文章中，我描述了如何使用 [GitHub](https://github.com) 和 [GitHub 动作](https://github.com/features/actions)为一个用苹果[开发者 ID](https://developer.apple.com/developer-id/) 签名的 Mac 应用程序设置持续集成、交付和部署。最棒的是，如果 app 是开源的，我描述的一切都是完全免费使用的！(如果你的应用程序不是开源的，而是托管在 GitHub 上的一个私人存储库中，你仍然可以免费这样做，但要受到[限制](https://help.github.com/en/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions)。)

# 这有什么难的？

过去，你可以编译你的代码，把它打包在一个文档或安装程序中，放在一个网站上供下载，然后期望人们下载它——现在不是了。由于恶意软件的传播，苹果公司在操作系统中增加了限制，以防止不受信任的代码运行。这些限制使建造和包装变得复杂。此外，使用过自制软件的用户希望能够从命令行安装并保持最新的软件。这种预期也使分配变得复杂。

自 2012 年以来，Mac 操作系统已经包括了[看门人](https://support.apple.com/en-us/HT202491)，这一功能要求应用程序已经用苹果提供的加密证书签名才能运行。最新版本的 macOS 甚至开始要求在 Mac App Store 之外发布的应用程序必须经过苹果公司的[公证](https://developer.apple.com/developer-id/)才能运行，这是一个[过程](https://developer.apple.com/documentation/xcode/notarizing_macos_software_before_distribution)，要求你在发布之前将应用程序上传到苹果公司，然后等待回复。

使用 Mac App Store 进行分发有很多原因——对于大多数应用程序来说，这可能是正确的地方。但如果你想使用 Mac App Store 中不允许的 API，或者你不想处理审查过程，那么选择退出并找到不同的发布渠道是有意义的。我会告诉你怎么做。

# 我们理想的 DevOps 工作流程

我们理想的 DevOps 工作流是在每次提交时自动测试我们的代码，并在每个发布标签上自动构建和发布以供分发。在我们推动变更和用户访问变更之间不应该有手动的步骤。

在这种情况下，当我们说“发布以供分发”时，我们的意思是可以使用自制软件安装新版本的应用程序。为了实现这一点，我们将在 GitHub 上托管我们自己的[自制 tap](https://docs.brew.sh/Taps) ，并在 GitHub 上托管我们构建的应用作为[发布资产](https://developer.github.com/v3/repos/releases/#upload-a-release-asset)。

# 六个手动步骤

在软件部署的环境中，趋向于“手动的、重复的、可自动化的、战术性的、缺乏持久价值的、线性扩展的”工作被称为[辛劳](https://landing.google.com/sre/sre-book/chapters/eliminating-toil/)。在提交更改、标记并将其推送到 GitHub 之后，我们必须执行六个手动步骤来发布我们的应用程序以供分发*,这些步骤都很辛苦:*

*   构建用于发布的应用程序。
*   使用 Apple 开发者 ID 证书对应用程序进行签名。
*   将签名的应用程序打包到磁盘映像中。
*   公证磁盘映像。
*   将经过公证的磁盘映像作为发布资产上传到 GitHub。
*   用新的应用程序版本和 SHA256 值更新 Homebrew tap。

# 告别辛劳

我们用辛劳做什么？我们将它自动化！最终的结果将是，每当我们向 GitHub 推送标签时，应用程序将自动构建、签名、打包、公证，并作为 GitHub 发布资产上传，我们的家酿 tap 将自动更新以指向新版本。整个过程只需几分钟。

自动化是持续集成(CI)和持续部署(CD)的混合，使用 [GitHub Actions 工作流](https://help.github.com/en/actions/configuring-and-managing-workflows)实现。

# 连续累计

我们创建的自动化的第一部分是持续集成:在每次提交后构建和测试我们的应用程序。为此，我们在 macOS 主机上的 GitHub Actions 环境中运行`xcodebuild test`。

***注意*** *:即使你的应用不包含测试，你仍然可以在每次提交时构建你的应用，并从持续集成中获得价值。在这种情况下，使用* `*build*` *动作代替* `*test*` *动作与* `*xcodebuild*` *在工作流程的最后一步。*

通过将以下 GitHub Actions 工作流放入我们在`.github/workflows/master_test.yml`的项目工作区，我们实现了持续集成:

每当我们将新的提交推送到`master`分支时，这个工作流就会运行。它包括在 macOS 主机上运行的单个作业。这项工作包括三个步骤。

首先，触发工作流的提交的引用将是签出的引用。

然后，确定项目的默认方案，并将其设置为环境变量。(在这一点上，这种方法可能看起来有些多余——毕竟，我们可以对项目的默认方案名称进行硬编码——但是我现在引入这种模式是因为我们将在下一个工作流中再次使用它，并且它允许这个工作流在其他项目中无需修改就可以重用。)

最后，使用`xcodebuild test`构建并测试项目，输出通过`[xcpretty](https://github.com/xcpretty/xcpretty)`传输，这是一个方便的开源工具，预装在 GitHub Actions runners 上。我们用值“`—`”(这个值在 Xcode 中被称为“本地运行签名”)覆盖了`CODE_SIGN_IDENTITY`构建设置，以启用特定代码签名。这允许我们在一台机器上构建和测试应用程序，而不需要为这个工作流配置任何代码签名秘密。

对于这个持续集成工作流来说，一些事情必须是真实的。上述工作流文件必须提交到项目工作区的`master`分支，并推送到 GitHub。项目本身必须配置有包含构建和测试阶段的共享方案，并且该方案必须位于方案列表的第一位。最后，测试必须能够在通过`xcodebuild test`调用时运行——在第一次部署工作流之前，通过从命令行本地运行测试来验证这一点。

# 持续部署

我们创建的自动化的第二部分是持续部署:在每个发布标签之后发布我们的应用程序。我们使用的惯例是，任何以字母“`v`”开头的标签都是发布标签，例如`v1.2.0`。

我们在 macOS 主机上的 GitHub Actions 环境中运行`xcodebuild install`来构建和签署项目，然后我们将我们的应用程序打包成一个磁盘映像，公证它，将其作为发布资产上传，最后用最新的版本信息更新我们的家酿 tap。

通过将以下 GitHub Actions 工作流放入我们在`.github/workflows/master_deploy.yml`的项目工作区，我们实现了持续部署:

这个工作流比前一个工作流多了很多，但是两个工作流的开始是相似的。每当我们向存储库推送以字母“`v`”开头的新标签时，这个工作流就会运行。它包括在 macOS 主机上运行的单个作业。这项工作包括十一个步骤。

和以前一样，第一步检查项目。这一次，触发工作流的标签是检出的标签。

然后，项目的默认方案被确定，并被设置为一个环境变量，如前所述。

接下来，使用`xcodebuild -showBuildSettings`从项目中读取额外的构建设置，并设置为环境变量供后续步骤使用。这避免了在工作流中硬编码项目细节的需要，并且使得在其他项目中重用工作流更加容易。

下一步是在 action runner 主机上设置一个 macOS 钥匙串，其中包含我们应用程序的签名凭证，因为 Xcode 希望在默认钥匙串中找到签名身份。该步骤使用`SIGNING_CERTIFICATE_P12_DATA`和`SIGNING_CERTIFICATE_PASSWORD`秘密以及`security`工具来导入签名证书。(有关如何设置密码的信息，请参见下文。)这是这个过程中最复杂、记录最少的一步。这个 StackOverflow 问题的答案提供了一些背景。

现在我们测试应用程序。我们从来不想发布一个没有通过测试的应用程序版本，所以我们在继续之前要确保测试通过。如果您的项目不包含测试，请省略此步骤。

一旦应用程序已经过测试，下一步是重新构建它以便安装，然后将应用程序安装到一个发布根目录(`DSTROOT`)。

成功完成安装步骤后，使用`hdiutil`将应用程序打包成磁盘映像。

工作流程的下一步是使用`[notarize-cli](https://www.npmjs.com/package/notarize-cli)`工具公证磁盘映像。这是我写的一个工具，用来在持续部署的背景下简化 Mac 应用的公证。它包装了 Xcode 提供的两个工具— `altool`和`stapler`。它用那些工具把磁盘镜像上传到苹果，等待成功，然后把公证钉到磁盘镜像上。这一步使用`NOTARIZE_USERNAME`和`NOTARIZE_PASSWORD`秘密向 Apple 认证。(有关如何设置密码的信息，请参见下文。)

该应用程序已准备好进行部署。下一步是将经过公证的磁盘映像作为发布资产交付给 GitHub。我们使用来自 [GitHub 市场](https://github.com/marketplace)的`[softprops/action-gh-release](https://github.com/softprops/action-gh-release)`动作来为我们执行这个动作。这一步使用的是 GitHub Actions 自动为我们设置的`[GITHUB_TOKEN](https://help.github.com/en/actions/configuring-and-managing-workflows/authenticating-with-the-github_token#about-the-github_token-secret)`秘密。这个令牌的作用范围是允许我们只对运行的工作流所在的存储库进行修改。

至此，app 已经交付。在 GitHub 上闲逛的人可以找到发布的应用并下载它，但是谁有时间做这个呢？为了完成部署，我们仍然需要更新自制水龙头。受欢迎的应用程序可以存放在[家酿木桶](https://github.com/Homebrew/homebrew-cask/tree/master/Casks)储存库中，但是如果你的应用程序还没有受众，它就不会被官方 tap 接受——你需要存放自己的应用程序。幸运的是，托管自己的水龙头很容易，家酿支持从第三方水龙头无缝安装。(有关如何设置龙头和桶以用于此工作流程的信息，请参见下文。)

工作流程中的下一步是检查水龙头。这一步使用了`CASK_REPO`和`CASK_REPO_TOKEN`的秘密。(有关如何设置密码的信息，请参见下文。)屏蔽回收令牌保存在已签出的工作区中，并用于在下一步中将更改推回 tap 存储库。

最后一步是用应用程序的新版本号和发布资产的哈希值更新木桶。版本号由触发工作流的发布标签确定，哈希从经过公证的磁盘映像计算得出。提交作者被设置为触发工作流的用户，更改被提交并推回 tap 存储库。

要让这个工作流工作，有几件事必须是真的。上述工作流文件必须提交到项目工作区的`master`分支，并推送到 GitHub。项目本身必须用包含构建和测试阶段的默认共享方案进行配置。项目必须配置代码签名身份的名称，以便在构建发布时使用，并且该名称必须与存储在 GitHub secret 中的签名凭证相匹配。最后，在家酿 tap 存储库中必须已经存在一个木桶，以便进行最后一步的更新。

# 项目配置

配置 Xcode 项目的方式似乎有无限多种。我希望这些工作流程能与您的工作流程相配合。我已经验证了这些工作流可以与新创建的配置了 UI 测试的 macOS 应用程序项目一起工作，除了默认的`testLaunchPerformance`测试在从命令行运行时失败——删除该测试，您就可以开始工作了。

若要为项目配置开发者 ID 签名，请先在 Xcode 的“帐户”偏好设置中添加您的 Apple ID 帐户。添加帐户后，从团队表中选择您的团队，然后单击“管理证书…”按钮。找一个“开发者 ID 应用”证书。如果您没有看到，请通过单击“+”按钮并从菜单中选择“开发者 ID 应用程序”来创建一个。

***注*** *:如果你用 Xcode 创建了证书，那么它也应该自动导入到你的本地钥匙串中。如果出于某种原因，您需要手动下载您的开发者 ID 证书和私钥，并将其导入到您的钥匙串中，您可以从* [*本页*](https://developer.apple.com/account/resources/certificates/list) *执行此操作。双击下载的"。cer”文件来使用“钥匙串访问”应用程序导入它。*

接下来，对 Xcode 项目中应用程序目标的“签名和功能”设置进行以下更改:

*   停用“自动管理签名”
*   将“团队”设定为与您的 Apple ID 相关联的团队。
*   将“预置描述文件”设置为“无”]。
*   将“签名证书”设置为“开发者 ID 应用”

现在应该可以使用 Xcode 用你的开发者 ID 证书给你的应用签名了。通过从“产品”菜单中选择“归档”来构建要发布的应用程序，验证所有配置是否正确。然后从“窗口”菜单中选择“管理器”,并在档案表中找到新创建的档案。点击“分发应用”，然后选择“开发者 ID”并点击“下一步”选择“导出”并点击“下一步”保留“分发证书”和配置文件设置的默认值，然后单击“下一步”如果出现提示，请输入您的凭据，最后单击“导出”保存签名的应用程序。

那是许多乏味的点击和等待——难怪我们决定自动化这一点！

# 秘密配置

我们已经创建了一个 GitHub Actions 工作流来执行持续集成和持续交付，并且我们的项目配置了开发人员 ID 代码签名。接下来要做的是设置我们的工作流成功运行所需的秘密。

要向 GitHub 存储库添加密码，请导航到存储库的“设置”选项卡。然后从侧边栏选择“秘密”。点按“新密码”，然后输入密码的名称和值。最后，点击“添加密码”

`**SIGNING_CERTIFICATE_PASSWORD**`

这个密码应该设置为一个密码，用于加密您在生成下面的`SIGNING_CERTIFICATE_P12_DATA`密码时创建的 p12 文件。

下面是我最喜欢的使用命令行生成随机密码的一行程序:

```
< /dev/urandom LC_CTYPE=C tr -dc a-zA-Z0-9 | head -c 32; echo
```

`**SIGNING_CERTIFICATE_P12_DATA**`

此机密应该设置为包含签名证书和私钥的 p12 文件的 base64 编码内容。

要设置这个秘密，我们首先需要从 keychain 导出签名证书和私钥。为此，请打开“钥匙串访问”应用程序，确定您的默认“登录”钥匙串已选定，然后选择“我的证书”类别。从列表中选择您的开发者 ID 证书，并从“文件”菜单中选择“导出项目…”。将文件格式设置为“个人信息交换(. p12)”，然后单击“保存”当提示输入加密密码时，输入`SIGNING_CERTIFICATE_PASSWORD`密码的值。然后在“钥匙串访问”提示时输入您的本地帐户凭据。

接下来，打开终端窗口并在磁盘上找到 p12 文件。运行以下命令对文件进行 base64 编码，并将其复制到剪贴板:

```
cat Certificates.p12 | base64 | pbcopy
```

粘贴复制的文本作为秘密的值。

`**NOTARIZE_USERNAME**`:这个秘密应该设置到你开发者账户的 Apple ID。

`**NOTARIZE_PASSWORD**`:此密码应设置为您的 Apple ID 帐户的应用程序专用密码。按照这些说明[创建特定于应用程序的密码。](https://support.apple.com/en-us/HT204397)

`**CASK_REPO**`:这个秘密应该设置为 GitHub 存储库的全限定名称，该存储库托管这个应用程序的容器，例如`bacongravy/homebrew-tap`。

`**CASK_REPO_TOKEN**`:这个秘密应该被设置为 GitHub 个人访问令牌(PAT ),其作用域是对由`CASK_REPO`指定的存储库进行写访问。要生成 PAT，请在 GitHub 上导航到您的用户“设置”，从边栏中选择“开发者设置”，然后从边栏中选择“个人访问令牌”，然后单击“生成新令牌”为令牌命名，选择“repo”范围，然后单击“Generate token”复制显示的令牌，并将其作为密码添加到您的存储库中。

# 抽头配置

家酿 tap 只是一个遵循特定惯例的 GitHub 库。阅读[文档](https://docs.brew.sh/How-to-Create-and-Maintain-a-Tap)了解更多信息。

我建议将你的 tap 库命名为`homebrew-tap`。这将允许您使用如下命令安装应用程序:

```
 brew install <username>/tap/<app-name>
```

您的全限定存储库名称应该是`<username>/homebrew-tap`，这是您应该将`CASK_REPO` secret 设置为的值。

我们假设你的项目资源库命名为“chuck-wagon”，你的 Xcode 项目文件命名为“chuck-wagon.xcodeproj”，默认方案命名为“Chuck Wagon”，方案产生一个名为“Chuck Wagon.app”的产品。在这种情况下，工作流将发布一个名为“Chuck_Wagon.dmg”的磁盘映像，而木桶文件将被命名为“chuck-wagon.rb”。

您需要在 tap 存储库中创建屏蔽文件。阅读文档了解更多信息。

下面是“卡车”的酒桶可能的样子:

要使这适用于您的应用程序，您需要将“<username>”、“chuck-wagon”、“Chuck Wagon”、“Chuck Wagon.app”和“Chuck_Wagon.dmg”替换为适合您的 GitHub 用户名和应用程序的值。</username>

在您在路径`/Casks/$PROJECT_NAME`的`CASK_REPO`存储库中创建一个文件之后，`master_deploy`工作流将能够更新版本和 sha256 值。

# 结论

这并不容易，但我们做到了。恭喜你！

我已经建立了一个[演示库](https://github.com/bacongravy/mac-app-devops-demo)，这样你就可以看到一个活生生的例子。你可以在这里查看`[master_deploy](https://github.com/bacongravy/mac-app-devops-demo/blob/master/.github/workflows/master_deploy.yml)`工作流程[的运行日志。可以在](https://github.com/bacongravy/mac-app-devops-demo/runs/728981487?check_suite_focus=true)[这里](https://github.com/bacongravy/homebrew-tap/blob/master/Casks/mac-app-devops-demo.rb)找到由该工作流构建的演示应用的木桶。

我希望这篇文章能启发你将生活中的辛劳自动化。编码快乐！