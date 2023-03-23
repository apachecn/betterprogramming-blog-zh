# 如何在 Web 上托管 DocC 文档

> 原文：<https://betterprogramming.pub/how-to-host-your-docc-documentation-on-the-web-3561874ffd0a>

## 确保你的文档在网站和代码库之间总是同步的

![](img/a909f488beee43bc120db91b3c48a92f.png)

所有标识取自官方文件。

在本文的第一部分，我们了解了如何使用 DocC 为我们的 Swift 框架生成文档。

在这篇文章中，我们将学习如何与世界分享我们的文档。我们将看到如何导出我们的 DocC 归档文件以供共享。然后，我们将作为一个网站托管文档，并设置一个 CI，以便每次更新我们的代码库时，文档的最新副本都会部署在我们的网站上。

**我们将要学习的内容:**

*   [导出您的 DocC 档案](#f1a5)
*   [从命令行生成文档存档](#1ead)
*   [在 netlify 上托管文档存档](#269d)
*   [使用 bitrise 自动构建和部署 docarchive】](#916a)

# 导出您的 DocC 归档

与他人共享您的文稿的最简单方法是导出 DocC 归档，并使用“邮件”或“信息”共享它。

要导出您的 DocC 档案，请转到**产品>构建文档。**

![](img/5969208ba7e9ca907e1538b64d8a18c7.png)

作者截图

然后点击 Xcode 文档窗口中 libs 根文章名称左边的上下文菜单按钮。

![](img/c0beb56f7f066649a0c5d252d3ee8110.png)

作者截图

然后选择导出归档文件的位置

![](img/d2df696c120064261be1350d20906be1.png)

作者截图

现在，您可以在邮件中附加此文件，或者在信息中与您的朋友和同事共享，他们可以通过双击文件来查看您的文档，这将在他们的 Xcode 文档窗口中打开。

![](img/609f5c1145c4fa0a89a50ca57482f7d9.png)

作者截图

如果您想将文档共享给相对较少的人，共享存档就足够了，但是您可以做得更好，您可以将您的 docc 文档作为网站托管，每个人都可以访问。

为了做到这一点，我们将在网上托管 how 文档，就像苹果自己的文档网站一样。

我们将使用 [**Github**](https://github.com) 和 [**Netlify**](https://www.netlify.com) 来设置我们的文档网站

如果您在 Github 上已经有了您的 swift 包，那就很好，如果没有，请创建一个 repo，并将您的 swift 包目录推送到 Github，作为一个存储库。

# 从命令行生成 docarchive

在继续之前，我们需要在 swift 包目录内的 docs 文件夹中生成 docarchive，以便 docarchive 可以包含在我们的 git 存储库中，该存储库将在稍后用于托管文档网站。

要从命令行生成 docarchive，请从 swift 软件包的根目录运行以下命令

1.  运行以下 CMD 以在自定义派生数据位置构建 docarchive

```
xcodebuild docbuild -scheme ElegantAPI -derivedDataPath ~/ElegantAPIBuild -destination 'platform=iOS Simulator,name=iPhone 12'
```

2.我们将把生成的`docarchive`复制到 swift 包内的路径`Docs/ElegantAPI.docarchive`，运行:

```
cp -R `find ~/ElegantAPIBuild -type d -name 'ElegantAPI.doccarchive'` Docs/ElegantAPI.doccarchive
```

3.我们将需要添加`/* /index.html 200`，因为`docarchive`构建在 Vue.js 之上，我们需要这个重定向来使路由工作，通过运行下面的命令，我们将在我们的`docarchive`中创建一个名为`_redirects`的文件，然后 Vue.js 路由器将使用它来成功导航到我们的文档页面。

```
echo "/* /index.html 200" >  Docs/ElegantAPI.doccarchive/_redirects
```

现在，您可以将该文件夹添加到 g it 并推送到 GitHub。

# 在 netlify 上托管文档存档

现在我们的 Github repo 包含了我们的`docarchive`，我们将使用 netlify 作为网站托管那个`docarchive`,`docarchive`已经生成了在 web 上展示我们的文档所需的所有 HTML 和 JS，我们只需要在服务器上提供它们，为此我们将使用 Netlify，所以如果你没有 Netlify 的帐户，你可以在:[https://app.netlify.com/signup](https://app.netlify.com/signup)创建一个。

你可以用 GitHub 注册。

![](img/a8e31c137807bbf15c3af610dc99a696.png)

作者截图

你会被问到一些问题，下面是一个你如何填写的例子。

![](img/fbe23e158fc8b5c53bde0fc9170f9101.png)

作者截图

您将看到一个项目设置页面，选择导入项目:

![](img/c881e52717ecb274b4eb520d616f41ab.png)

作者截图

选择您的 GitHub 作为您的源提供商

![](img/0bebba26bc4167e2cf5ddf1d9e5e75be.png)

作者截图

选择您的 swift 包的 GitHub 存储库:

![](img/c4a5842a7c09b42b58c7b5866a0465f0.png)

作者截图

您将看到下面的表单，在 GitHub 存储库中添加与您的`docarchive`位置相同的发布目录路径。

![](img/f6c47dca35e5032fd0b1b9d754d5dc0a.png)

作者截图

完成后，您将看到类似于下面的截图:

![](img/1f156deee92724dc74e725da49ae418b.png)

作者截图

您可以通过点击左上角的链接导航到您的托管文档站点，在我的例子中是`https://nervous-pike-2f8a3f.netlify.app`，但是您将不会看到文档，而是看到如下所示的空白屏幕:

![](img/134ee89a224fe713641ec443f5cf125d.png)

作者截图

要查看您的文档，您需要修改 URL，如下所示:

```
https://nervous-pike-2f8a3f.netlify.app/documentation/ElegantAPI
```

在这里，您可以根据您的基本路径和包名来更改基本 URL 和结束路径。

格式为`<base url>/documentation/<swift package name>`

现在，您可以在更改后的 URL 上看到您的托管文档网站:

![](img/c7c76bd1cc190bc43e64b007c095d270.png)

作者截图

## 更改托管文档的基本 URL

正如你可能已经注意到的，netlify 为我们提供了一个随机生成的 URL，但是你可以编辑这个 URL，使它更易于阅读。

对于“转到域”设置:

![](img/c45d9820dad875dbad5f9962e831e64c.png)

作者截图

然后点击右边的选项选项卡并点击“编辑站点名称”:

![](img/58c653572f6cd23a1c1868bbe4b3e42b.png)

作者截图

然后在字段中添加您的首选子域名:

![](img/0edd2ea90c55bb0f28fc73853665a704.png)

作者截图

瞧啊。您现在可以访问该网站:

```
[https://elegantapi.netlify.app/documentation/ElegantAPI](https://elegantapi.netlify.app/documentation/ElegantAPI)
```

# 使用 bitrise 自动化 docarchive 构建和部署

现在我们已经建立并运行了我们的网站，但是每次我们改变我们的源文档时，它都需要更新，如果每次你在你的包文档中做了一些改变，并将其推送到 git，网站就会重新生成以反映这些改变，这不是很好吗？

这就是我们将在本节中使用 Bitrise 实现的自动化。

如果您没有来自[这里](https://app.bitrise.io/users/sign_up)的 Bitrise 帐户，请创建一个。

## 在 bitrise 中创建应用程序

创建帐户后，您需要将一个应用程序添加到仪表盘，点击“添加新应用程序”按钮将该应用程序添加到仪表盘。

![](img/1eea6e7c9d32cb4cc02dd83c81b51585.png)

作者截图

在下一步中，您将被要求输入应用程序的隐私类型，您可以选择私人或公共对于此演示，我们选择私人:

![](img/60291ff8babf9c5e9c6082972f904192.png)

作者截图

在下一步中，bitrise 将要求您从所提供的存储库提供商之一连接您的存储库，在本例中，我们选择了 GitHub 和我们的 swift 软件包存储库:

![](img/cab67e830c948c5026201081e8173b48.png)

作者截图

在下一步中，将询问您是否要设置一个额外的私有存储库。

我们不需要任何私人回购访问，因此选择“否，自动添加 SSH 密钥”

![](img/e67d83d0402a6d29da9a6c65fd1ece26.png)

作者截图

接下来，您需要指定您需要从中获取代码的分支，在本例中，我们选择默认的分支“主分支”:

![](img/c3784c995d8e91aef8691a32dfb45909.png)

作者截图

Bitrise 将尝试识别您的项目类型。但是，由于我们没有设置 iOS 应用程序，它无法为我们检测配置:

![](img/876ea0058f83634f84cc9bfc3616a189.png)

作者截图

因此，我们将为 CI 渠道进行定制配置:

![](img/398b15c5b7b3c63b57733112a408b2d9.png)

作者截图

从配置列表中选择“其他/手动”，从下拉列表中选择 macOS 堆栈，然后点击“我准备好了”:

![](img/3d0763d08104e51ff53c4eab6a2550fc.png)

作者截图

跳过应用图标和 webhook 部分，点击右上角的“完成”完成配置:

![](img/c04c494d4e6d4c9c4b7da65a4bd9e045.png)

作者截图

完成配置后，您将能够在仪表板中看到新创建的应用程序:

![](img/274084f938ffacef7772902c4ef68d93.png)

作者截图

## 定义 CI 工作流

点击您的应用程序并转到工作流部分，我们现在将在此定义 CI 渠道，您将看到如下屏幕:

![](img/6901d64f5913b746ee7c615c36d46b39.png)

作者截图

正如你所看到的，bitrise 已经为我们建立了相当多的工作流，但我们不会要求所有的工作流，因为我们不会部署到 bitrise。相反，我们将删除该步骤。要点击“部署到 bitrise ”,向下滚动右侧部分，删除步骤:

![](img/b39026b3557f7de8a81ce7fd36a91b5e.png)

作者截图

现在转到“使用脚本执行任何操作”步骤，并粘贴以下脚本:

```
echo "Building Doc"# Builds the docarchive with custom derive data path, change the scheme name to match your package here
xcodebuild docbuild -scheme ElegantAPI -derivedDataPath ~/ElegantAPIBuild -destination 'platform=iOS Simulator,name=iPhone 12'#2 Copy the docarchive to correct place, change the path according to you package name
cp -R `find ~/ElegantAPIBuild -type d -name 'ElegantAPI.doccarchive'` Docs/ElegantAPI.doccarchive#3 Writes necessary redirects
echo "/* /index.html 200" >  Docs/ElegantAPI.doccarchive/_redirects#4 Git config needed to push back to git repo, replace with your git id and email
git config user.name "DominatorVbN"git config user.email "voilentbynaturet@gmail.com"#5 Add the newly generated docarchive to git indexc and commit 
git add .git commit -m "Archive Updated [skip ci]"#6 Push back to the git repo, if your branch is other than master change accordingly
git push git@github.com:DominatorVbN/ElegantAPI.git HEAD:master
```

这个脚本负责生成`docarchive`，并将更新后的归档文件推送到 git repo。

## 为 netlify 构建设置 webhook

这个脚本将只使用新的`docarchive`更新 git 存储库，但它不会被部署到 netlify。为此，我们将设置一个 webhook，让 netlify 知道重新部署我们的站点。

为此，导航至 netlify dashboard，然后转到构建设置:

![](img/ac4995e787f0c74962387891b6230a16.png)

作者截图

然后从右侧菜单导航到“构建和部署”，移动到“构建挂钩”并点击“添加构建挂钩”:

![](img/2176962180e8751a4093461ca057c55b.png)

作者截图

将构建挂钩命名为 bitrise，并选择 master 作为要构建的默认分支:

![](img/58c8675b77b4920a29882b6190a0da94.png)

作者截图

现在复制这个网址，并添加以下内容:

```
curl -X POST
```

生成的命令将如下所示:

```
curl -X POST https://api.netlify.com/build_hooks/61db0cd86a3e78dfb669d325
```

将此添加到 bitrise 中的脚本步骤:

![](img/b3b7d3351171495a6b9719df7e173f30.png)

作者截图

键入 CMD+S 保存工作流。

## 在 Bitrise 中设置 SSH

我们将需要做更多的设置，以便能够推回到我们的 Github 存储库，我们将需要设置一个 ssh 密钥，它将提供对我们的 Github repo 的访问，以便 Bitrise CI 可以推送到我们的存储库，在 Bitrise 中转到您的应用程序的设置选项卡:

![](img/dd233f39746930d1b739341e161ecc9f.png)

作者截图

然后向下滚动到 SSH 设置:

![](img/f1da63e25f854d1a1020c8fbdaa15385.png)

作者截图

单击“更改 SSH 密钥对”，然后选择“生成 SSH 密钥对”:

![](img/828c9676cc632bc3a9cc7696baca7c65.png)

作者截图

将生成的 SSH 公钥复制到剪贴板:

![](img/f6a9478f5563f0d5cdebaf4ca08777ad.png)

作者截图

转到 Github，从右上角的配置文件按钮导航到设置:

![](img/b450fe2d77b5cb83aa73d6ddff4913fa.png)

作者截图

导航到“SSH 和 GPH 密钥”部分，然后单击“新建 SSH 密钥”:

![](img/68c6adc46593e2c7c1d1fb5e5058d7f3.png)

作者截图

粘贴复制的公钥并给它命名，然后单击“添加 SSH 密钥”:

![](img/bc7df2552d04f00e78c82a7a2b14de58.png)

作者截图

## 为 GitHub 设置 webhook

完成后，我们就可以设置我们的 webhook 了，当我们将代码推送到 GitHub 来设置 webhook 时，它将触发构建。转到`APP > Code > Incoming Webhooks`并点击“自动设置”:

![](img/478676d8e875bd83e13b16dacb9d1c65.png)

作者截图

这就是测试管道的所有配置，你可以在你的文档中做一些修改，然后把它推给 Github。

您可以在 bitrise 仪表板上看到您的构建正在运行:

![](img/206932adc3a85e68194884ad0ad31c5a.png)

作者的屏幕录制

本文到此为止。感谢阅读。

```
**Want to Connect?**Reach out to me on [Twitter](https://twitter.com/amitsamant_dev), [Linkedin](https://www.linkedin.com/in/amitsamant-dev/).
```