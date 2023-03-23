# 构建一个 Slack 应用程序，向频道自动发送生日消息

> 原文：<https://betterprogramming.pub/build-a-slack-app-to-send-automated-birthday-messages-to-a-channel-9ec20c8438e1>

## 队友们，生日快乐

![](img/f58b8e86d920c0a88150c1b69ee08fac.png)

队友的认可可以提高生产力，促进协作。认可队友的一个简单但经常被忽视的方法是在他们生日时庆祝，让他们知道他们是宝贵的团队成员。

在本教程中，我们将学习构建一个 Slack 应用程序，这样你的整个团队就不会错过庆祝队友生日的机会。

再也没有超负荷的日历了——所有的东西都存储在 Google Sheets 中，生日消息总是准时到达。

# 它的作用

Slack 应用程序将在太平洋标准时间每天早上 8 点通过向指定频道发布消息来启动生日庆祝活动，确认在当天和当月出生的队友。

Slack-app 模板预编程有两个方便的命令。工作区成员可以通过以下方式快速查询 Google 电子表格中已登记的生日列表:

`/cmd list` —在调用用户可见的私人消息中返回所有存储的生日

![](img/cb19a6e7d302db5d956ca1e9dcc189be.png)

成员还可以使用以下方式直接从 Slack 将生日添加到电子表格中:

`/cmd add <First and Last Name> <Birth Month> <Birth Day>` —将全名、出生月份和生日添加到电子表格中

`/cmd add Andrea Sanchez 12 07`

*   确保同时添加名字和姓氏。

![](img/b4621a10556c9046c9598d470b06f798.png)

## 你事先需要什么

*   [1x 松弛团队](http://slack.com/)
*   [1x 标准库账户](http://stdlib.com/)
*   [此谷歌电子表格的 1x 副本](https://docs.google.com/spreadsheets/d/1SKw97duf9RG7S6_3XcnQ2jKg7d-vuZfrq8_2rpDIoMQ/edit?usp=sharing)

## TK 删除？目录:

*   Google 电子表格设置
*   安装 Slack 应用
*   测试你的 Slack 应用
*   如何更改松弛通道和松弛消息
*   如何改变时间间隔
*   支持
*   感谢

# Google 电子表格设置

复制这张[谷歌电子表格](https://docs.google.com/spreadsheets/d/1SKw97duf9RG7S6_3XcnQ2jKg7d-vuZfrq8_2rpDIoMQ/edit?usp=sharing)并将其命名为**公司生日**或类似的名称。确保设置了三个字段`Name`、`BirthdayMonth`和`BirthdayDay`。像我做的那样，用测试数据填充几行。确保至少为其中一行输入当前月份和当前日期——我们需要在部署应用程序之前测试运行它。

![](img/47c1e7345f6baf357fca713618a2f0ff.png)

太好了！您已准备好安装 Slack 应用程序。

# 使用自动编码安装 Slack 应用程序

使用此链接直接复制我的 Slack 应用程序模板:

[https://autocode . stdlib . com/github/Janet HL/BirthdaySlackApp/？filename=README.md](https://autocode.stdlib.com/github/JanethL/BirthdaySlackApp/?filename=README.md)

系统会提示您登录或创建一个免费帐户。如果你有一个标准图书馆帐户，点击**已经注册**并使用你的标准图书馆凭证登录。

给你的项目起一个唯一的名字，然后选择**从 Github 启动 API 项目:**

![](img/89f4e1ca860dacaba28609eb974a32f5.png)

2

Autocode 会自动建立一个项目框架，将您的项目保存为 API 端点。

![](img/38bd7a4501e87f0b364ce69c335f0f0b.png)

要将 API 部署到云中，请浏览左侧的`functions/events/scheduler`文件夹并选择`daily.js`文件。

![](img/af41b00d4e5a5bc80438ee0df58be78b.png)

选择红色的 **Account Required** 按钮，这将提示您链接 Slack、Giphy 和 Google Sheets 帐户。

![](img/55033656cf4e3f7a0f74dbe7e82d23dd.png)

让我们从链接一个时差资源开始。

选择**链接新资源**链接新的 Slack app。

![](img/3e9e9198a7214f363f7821b135542910.png)

选择**安装标准库 App** 。

![](img/4829194fa6204019d69dcc433cf43cfc.png)

您应该会看到一个 **OAuth** 弹出窗口。选择您想要安装 Slack 应用程序的工作区，然后单击**允许。**

![](img/ea32722430a0854f361a80e38e448280.png)

如果你愿意的话，给你的 Slack 应用一个名字和图片。

![](img/95f37c932a68748a4a0833f15b124df4.png)

选择**完成**。绿色勾号表示您已经链接了您的 Slack 帐户。接下来选择**链接资源**，通过类似的认证流程连接到您的谷歌账户。

![](img/79f52e52d5e22be11eb3a413cf9c89b9.png)

选择创建电子表格的谷歌账户后，选择**允许。**找到并选择**公司生日**电子表格。继续**完成链接。**

![](img/be8a71af65b570773cb07242e4d3c1bd.png)

最后，按照说明完成将您的 Giphy 帐户链接到您的标准库令牌。

![](img/e205b639ec0ed081ad54ca5f0445827a.png)

一旦链接完 Slack、Google Sheets 和 Giphy，选择**完成链接。**

![](img/2137b37ec29dfadfb58004578177a401.png)

现在，您已经准备好测试运行代码，并验证您已经将应用程序链接到正确的帐户。

# 测试你的 Slack 应用

选择绿色的**运行代码**按钮来测试您的时差项目。

![](img/280d851eba15bbfd73e3757694da0481.png)

您应该会在您的 **#** 通用频道中看到以下消息。

![](img/e070ed80794a021617934066bb27e79d.png)

## 🚀部署你的 Slack 应用

要部署您的 Slack 应用程序，请选择文件管理器左下角的**部署**。

![](img/ada48e3f8b734a0c8e9dedb3cc183697.png)![](img/3744a20c5adbe092f99491d5b8eed3d0.png)

您的 Slack 应用程序已上线！设定为每天太平洋标准时间上午 8:00 在 **#** 通用频道发布消息。您的 Slack 应用程序现在可以在您授权的 Slack 工作区中使用。

你的 Slack 应用应该响应:

`/cmd list`，返回所有姓名及其出生月份和生日的列表。请注意，该消息仅对您可见，以便您的请求不会打扰您的工作空间。

![](img/cb19a6e7d302db5d956ca1e9dcc189be.png)

如果您想在电子表格中添加生日，请使用`/cmd`,后跟名字、姓氏、出生月份和生日:

`/cmd add Andrea Sanchez 12 07`

一旦你按下回车键，你应该会收到一条确认信息。确认消息仅对您可见:

![](img/b4621a10556c9046c9598d470b06f798.png)

要验证生日记录，您可以检查您的电子表格或再次运行`/cmd list`:

![](img/301af49bea6df96c01707c22878bda48.png)

恭喜你。现在你的应用已经上线了，你可以返回 Autocode，更改 Slack 应用发布消息的频道，更改消息，更改时间等等。

# 如何更改松弛通道和松弛消息

浏览`functions/events/scheduler`文件夹并打开`daily.js`文件。

![](img/1e8dc1d1e0464d86bab674759de3921e.png)

找到**第 62 行到**将`channel`的值更改为工作区中任何通道的名称。在名称前包含一个`#`。

找到**第 63 行**删除并在`text`键内添加您自己的自定义消息。变量`${names}`会传入电子表格中符合给定条件的名字。变量`${result.giphy.gif.url}`正在传入从第 54 行的函数中获取的 URL，它对`lib.giphy.random[@0.0.9]`进行 API 调用

![](img/58e15a989717620ff4a42bab98710502.png)

您可以通过选择**运行代码**按钮随时测试您的更改。

![](img/f2ebcecbcd27b2b63673ea43eac4c14a.png)

完成所需的更改后，确保**保存端点**并**按下**部署**以使您的更改生效。**

# 如何改变时间间隔

要更改时间间隔，浏览`functions/events/scheduler`文件夹并打开`daily.js`文件。

您会注意到代码被编程为在美国时间上午 8:00 运行——洛杉矶时间。要更改 Slack 应用程序发布生日消息的时间，右键单击`daily.js`文件并选择`Clone API Endpoint`。

![](img/0dbe729fd3c30d143899849716c794a6.png)

使用 API 向导选择**调度器**作为您的事件源，然后选择您希望 Slack 应用程序在频道内发布的频率。选择时间和时区，并确保点击****保存终点**按钮。**

**![](img/ac04fb66bec425254f2f6f33e64373b6.png)**

*   **删除第 10 行多余的`const result = {};`语句。**
*   **删除第 70 行多余的`return result;`语句。**
*   **选择**保存端点****

**Autocode 会自动将新的端点文件保存为`/scheduler`文件夹中的`daily0.js`。如果你不想让你的应用程序在两个时间间隔都运行，删除你的第一个文件，并通过选择`Rename API Endpoint.`将新文件重命名为`daily.js`**

**![](img/67800bfa82b0f55374fbf49facbe6dcc.png)**

**确保通过选择文件管理器左下角的**部署**再次部署您的应用以使更改生效。**

**![](img/2f342d4a8f77c17c3f1783e0e47825a6.png)**

# **支持**

*   **经由松弛:`[libdev.slack.com](https://libdev.slack.com/)`**
*   **您可以点击`[https://stdlib.com](https://stdlib.com/)`顶部栏中的`Community > Slack`请求邀请。**
*   **通过推特:[@标准图书馆](https://twitter.com/StandardLibrary)**
*   **通过电子邮件:[support@stdlib.com](mailto:support@stdlib.com)**

# **感谢**

**感谢标准图书馆团队和社区的所有支持，感谢 [Akanksha Kevalramani](http://itsakanksha.com/about.html) 领导[一个启发了本指南的研讨会](https://www.meetup.com/Maker-dev-SD-Chapter/events/267430141/)。**

**黑客快乐！**