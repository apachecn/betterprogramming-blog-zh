# 使用 GitHub 操作将 iOS 应用程序部署到 TestFlight 或 App Store

> 原文：<https://betterprogramming.pub/deploy-an-ios-app-to-testflight-or-the-app-store-using-github-actions-c4d7082b1430>

## 建立一个持续的部署渠道来连接 App Store

![](img/3216df490b855e13c96a5fe3c9ac2dbc.png)

尼古拉斯·天梭在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

在本教程中，我们将配置一个 [GitHub](https://github.com) 存储库，使用 [GitHub 动作](https://github.com/features/actions)将 [iOS](https://www.apple.com/ios/) 应用发布部署到[应用商店连接](http://appstoreconnect.apple.com)。我们将创建一个 GitHub Actions 工作流，在每次向 GitHub 推送提交时构建我们的 iOS 应用程序项目，并在每次推送发布标签时将我们构建的应用程序上传到 App Store Connect。

关于代码签名和配置文件，我们将尽可能保持简单。对于只有一个主要开发人员的简单应用程序和项目来说，这应该很好。如果您在一个大型团队中工作，或者您的项目包括多个目标，那么您可能会从采用不同的方法中受益。

如果你想看一个配置成使用本教程中描述的工作流的存储库的实例，你可以看看这个项目。

# 工作流程假设

本教程中描述的 GitHub Actions 工作流程假设它正在其中运行的存储库包含单个 Xcode 项目或工作区，并且项目或工作区的第一个方案构建了一个 iOS 应用程序。

该工作流程还假设 iOS 应用程序目标配置如下。

# 代码签名和配置文件

签署证书和配置文件的情况过去更令人困惑和费力。这是一段漫长的旅程，但有了 Xcode 11，事情就变得相当简单了。几乎所有过去需要访问苹果开发者门户网站的任务现在都可以在 Xcode 中处理。可以在 Xcode 的偏好设置中创建签名证书(用于开发和分发)。为目标启用“自动管理签名”时，会自动生成预置描述文件。

自动管理签名对于开发来说非常好，但是在 GitHub Actions 这样的平台环境中，这使得事情变得更加复杂。因为签名证书和配置文件不存储在项目存储库中，并且因为项目每次都在不同的主机上构建，所以自动管理的签名可能会导致每次工作流运行时都创建新的证书和相应的配置文件。这导致了开发人员门户中证书和概要文件的激增。

有多种方法可以处理这种复杂情况。例如， [fastlane 项目](https://fastlane.tools)包括对[代码签名工作流](https://codesigning.guide)的支持，该工作流依赖于加密证书和配置文件，然后将它们保存在 CI 系统可访问的独立 git 存储库中。

我们将在本教程中采用的方法是将签名证书和配置文件存储为 GitHub 存储库机密。我们的工作流会将签名证书导入到一个 keychain 中，并在构建项目之前将配置文件写入文件系统。

# 项目设置

您可以随意为 iOS 应用程序目标的“调试”构建配置启用“自动管理签名”，但必须为您的“发布”构建配置禁用该设置。通过在项目中禁用自动管理签名，我们确保可以使用与工作流中使用的相同证书和配置文件来手动测试我们的签名和分发流程。

立即禁用“自动管理签名”生成设置:

![](img/c4a97e17fce68fbf018489962a0766f8.png)

创建一个[应用商店标识符](https://developer.apple.com/account/resources/identifiers/list)和一个[分发预置描述文件](https://developer.apple.com/account/resources/profiles/list)。根据应用的功能，您可以使用通配符应用标识或特定应用标识来创建预置描述文件。

下载档案。

使用“Release”构建配置中的“Provisioning Profile”菜单将概要文件导入到项目中:

![](img/0439010856fb7f68a3da43492dd0100d.png)

导入配置文件会将“预配配置文件”构建设置设为导入配置文件的名称，将“代码签名身份”构建设置设为“iOS 分发”，将“开发团队”构建设置设为与预配配置文件关联的团队名称。

确定您拥有“iOS 分发版”或“Apple 分发版”签名证书。导航到“Xcode”>“偏好设置”…>“帐户”,选择您的 Apple ID，然后点按“管理证书”…。如果您没有看到合适的签名证书，您应该使用+˅按钮来创建“Apple Distribution”签名证书。

项目配置到此为止。您可能应该尝试使用 Xcode 手动将您的应用程序提交到 App Store Connect 至少一次，以验证您的项目设置是否正确。

# GitHub 秘密

为了让工作流成功运行，我们需要在 GitHub 中存储五个秘密:

*   签名证书数据的加密密码
*   签名证书数据
*   供应简档数据
*   App Store 连接用户名
*   App Store 连接密码

要将每个秘密添加到 GitHub 存储库中，首先需要导航到存储库的“设置”选项卡。然后从侧边栏选择“秘密”。点按“新密码”，然后输入密码的名称和值。最后，点击“添加密码”

## 签名 _ 证书 _ 密码

我们必须首先选择一个加密密码。您可以选择您喜欢的任何密码，但是如果您想要一些灵感，这里有我最喜欢的 shell 一行程序来生成密码，将其复制到剪贴板，并打印出来:

```
< /dev/urandom \
  LANG= \
  tr -dc a-zA-Z0-9 \
  | head -c 32 \
  | pbcopy \
  && pbpaste \
  && echo
```

粘贴密码作为`SIGNING_CERTIFICATE_PASSWORD`密码的值。

## 签名 _ 证书 _ P12 _ 数据

为了生成加密的证书数据，我们接下来将从“钥匙串访问”应用程序中导出证书身份。为此，请打开“钥匙串访问”应用程序，确定您的默认“登录”钥匙串已选定，然后选择“我的证书”类别。从列表中选择您的“Apple 分发版”(或“iOS 分发版”)证书，并从“文件”菜单中选取“导出项目…”。将文件格式设置为“个人信息交换(`.p12`)”，然后单击“保存”当提示输入加密密码时，粘贴`SIGNING_CERTIFICATE_PASSWORD`密码的值。然后在“钥匙串访问”提示时输入您的本地帐户凭据。

接下来，打开一个终端窗口，在磁盘上找到`p12`文件。运行以下命令对文件进行 base64 编码，并将其复制到剪贴板:

```
cat Certificates.p12 | base64 | pbcopy
```

粘贴复制的文本作为`SIGNING_CERTIFICATE_P12_DATA`秘密的值。

## 预配 _ 配置文件 _ 数据

我们需要对我们在项目设置期间创建和下载的配置文件进行 base64 编码，然后将其复制到我们的剪贴板。运行以下命令，用您下载的预置描述文件的名称替换`profile.mobileprovision`文件名:

```
cat profile.mobileprovision | base64 | pbcopy
```

粘贴复制的文本作为`PROVISIONING_PROFILE_DATA` secret 的值。

## 应用商店连接用户名

此密码应设置为您的开发者帐户的 Apple ID。

## 应用商店连接密码

此密码应设定为 Apple ID 帐户的特定于应用程序的密码。按照这些说明[创建一个特定于应用程序的密码。](https://support.apple.com/en-us/HT204397)

# 关于认证的一个注释

当在 GitHub Actions 上下文中运行时，我们需要使用特定于应用程序的密码来验证 App Store Connect，因为当帐户启用了[双因素验证](https://support.apple.com/en-us/HT204915)时，我们将用于向 App Store Connect 提交应用程序的工具不支持使用常规密码。自 2019 年起，具有“账户持有人”角色[的开发者账户需要进行双因素认证。](https://developer.apple.com/news/?id=02202019a)

我们可以创建一个单独的 Apple ID，不启用双因素认证，而不是创建一个特定于应用程序的密码。然后，我们会将其添加到团队中，但不添加“帐户持有人”角色。这可能是一个更安全的解决方案，但它需要更多的工作来设置和维护。对于本教程，我们将使用特定于应用程序的密码。

# 工作流脚本

为了保持工作流文件的可读性，并使步骤更易于调试，我们将把一些工作流步骤逻辑放在单独的脚本文件中。我们将编写三个脚本，并将它们安装到与`.github`目录中的工作流相邻的`scripts`目录中。第一个脚本将读取项目文件并设置工作流作业环境变量。第二个将设置一个默认的钥匙串，并将签名证书导入其中。第三个将把配置文件写入磁盘。

创造`.github/scripts/set-env-from-xcodeproj.sh`:

该脚本确定项目中第一个方案的名称，然后读取`PRODUCT_NAME`构建设置的值。它使用`[set-env](https://help.github.com/en/actions/reference/workflow-commands-for-github-actions#setting-an-environment-variable)` [工作流命令](https://help.github.com/en/actions/reference/workflow-commands-for-github-actions#setting-an-environment-variable)设置一个同名的作业级[环境变量](https://help.github.com/en/actions/configuring-and-managing-workflows/using-environment-variables)。环境变量将在工作流的后续步骤中使用。

创建`.github/scripts/import-certificate.sh`:

此脚本创建一个新的钥匙串，将其设为默认，解锁它，并将其配置为保持解锁状态。然后，它对来自`SIGNING_CERTIFICATE_P12_DATA`环境变量的签名证书数据进行 base64 解码，并使用来自`SIGNING_CERTIFICATE_PASSWORD`环境变量的解密密码将其导入到钥匙串中。最后，[一些钥匙扣魔法](https://stackoverflow.com/a/40039594)撒在上面。

创造`.github/scripts/import-profile.sh`:

这个脚本创建了`~/Library/MobileDevice/Provisioning Profiles`目录，然后对来自`PROVISIONING_PROFILE_DATA`环境变量的配置文件数据进行 base64 解码，并存入文件`profile.mobileprovision`。

运行以下命令将脚本标记为可执行:

```
chmod a+x .github/scripts/*.sh
```

# 工作流程

创建`.github/workflows/deploy.yml`:

该工作流以两种方式触发。每当新的提交被推送到`master`分支时，以及每当新的发布标签被推送到存储库时(按照惯例，发布标签是以字母“`v`”开头的任何标签，如“`v1.2.3`”)，它都会被触发。无论工作流是如何触发的，初始步骤都将运行，但是工作流中的最后两个步骤将仅在工作流被发行标签触发时运行。

工作流从签出与触发工作流运行的项目相同的项目提交开始。

接下来，工作流运行我们的`set-env-from-xcodeproj.sh`脚本来设置`PRODUCT_NAME`环境变量。

然后，工作流运行我们的`import-certificate.sh`脚本，并将`SIGNING_CERTIFICATE_P12_DATA`和`SIGNING_CERTIFICATE_PASSWORD`秘密作为同名的环境变量传递给脚本。

之后，工作流运行我们的`import-profile.sh`脚本，并将`PROVISIONING_PROFILE_DATA` GitHub secret 作为同名的环境变量传递给脚本。

然后，工作流使用来自`[fastlane](https://docs.fastlane.tools/advanced/fastlane/)`工具的`[build_app](https://docs.fastlane.tools/actions/build_app/)`动作构建应用程序。`fastlane`工具预装在 GitHub builder 主机上。我们没有在我们的项目中使用`[Fastfile](https://docs.fastlane.tools/advanced/Fastfile/)`或者[将](https://docs.fastlane.tools/getting-started/ios/setup/#use-a-gemfile) `[fastlane](https://docs.fastlane.tools/getting-started/ios/setup/#use-a-gemfile)` [定义为依赖](https://docs.fastlane.tools/getting-started/ios/setup/#use-a-gemfile)——我们只是利用动作和工具的便利来简化我们的工作流程。如果我们想要更多的控制，我们可以用复杂的调用`xcodebuild archive`和`xcodebuild -exportArchive`来代替这个简单的命令。但是对于我们的目的来说，使用`fastlane`要容易得多。

接下来，工作流上传构建日志作为 [GitHub Actions 工件](https://help.github.com/en/actions/configuring-and-managing-workflows/persisting-workflow-data-using-artifacts)。这些日志对于诊断失败的构建可能很有用。通过将它们保存为工件，它们在作业完成后仍然可供下载。

如果工作流由发布标签触发，则工作流将执行两个最终发布步骤。

首先，工作流上传由构建步骤输出的 iOS 应用商店包(`ipa`)和调试符号(`dSYMs`)作为 [GitHub release](https://help.github.com/en/github/administering-a-repository/about-releases) 资产。`GITHUB_TOKEN`秘密作为环境变量传递给脚本。虽然保存这些资产并不是绝对必要的，但它们可能有一天会派上用场。如果自动上传到 App Store Connect 失败，您需要手动重试，则`ipa`可能会有用。如果您想将用户崩溃报告符号化，那么`dSYMs`可能会很有用。

然后，工作流将`ipa`上传到 App Store Connect。一旦应用程序成功上传，它将可用于 TestFlight 并提交到应用程序商店。`APP_STORE_CONNECT_USERNAME`和`APP_STORE_CONNECT_PASSWORD`秘密作为同名的环境变量传递给脚本。

最后两个发布步骤依赖于我们在工作流的前面从项目的构建设置中设置的`PRODUCT_NAME`环境变量。通过使用这种模式，我们避免了将项目的任何细节硬编码到工作流中，并确保相同的工作流可以跨多个项目存储库使用。

提交脚本和工作流，并将它们推送到 GitHub，然后转到存储库的“Actions”选项卡，查看工作流的运行情况。

# 后续步骤

安装了这个工作流之后，每当您将新的提交推送到项目的 GitHub 存储库时，您应该会看到您的工作流正在运行，并且您可以在标记发布的提交之前验证您的项目是否构建成功。

如果您的项目包含测试，那么您可能需要考虑在工作流中添加一个额外的步骤(在“构建应用程序”步骤之前)，该步骤运行您的项目测试，并在任何测试失败时返回一个错误。添加一个测试步骤会在每次推送之后给你额外的反馈，并且会防止你意外地发布一个没有通过任何测试的构建。例如，您可以添加一个运行`fastlane run run_tests`的步骤。

本教程到此结束。我希望你学到了一些东西。编码快乐！