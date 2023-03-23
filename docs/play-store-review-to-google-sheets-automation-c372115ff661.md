# 如何自动将谷歌 Play 商店评论引入谷歌表单

> 原文：<https://betterprogramming.pub/play-store-review-to-google-sheets-automation-c372115ff661>

## 知识是让你的应用变得更好的力量

![](img/eb6172dabf536b6791c3142f39ce7482.png)

照片由[拉杰什瓦尔·巴楚](https://unsplash.com/@rajeshwerbatchu7?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/google?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

充分利用谷歌 Play 商店对应用程序的评论对它们的成功至关重要。以下是我们如何有力地分析 Play Store 评论的方法。

在发布你的应用程序后，跟踪用户的情绪是你能做的最重要的任务，因为这可以让你计划未来的迭代，并看到你的用户群中任何潜在的差异。

每月一次，我们的设计师 Jotham 会在一个电子表格中整理我们每个客户应用的最新评论。他用这个来分析数据和分类评论，以了解用户对每个应用程序的最新体验和想法。

可想而知，这个过程很耗时，但是超级有价值。因此，我们决定利用最近的一个 R&D 日来自动化这个过程。

这篇博客概述了自动化的第一步——在电子表格中自动收集评论。

# 第一步

为此，您需要一个服务帐户。您可以根据此[指南](http://(https://developers.google.com/android-publisher/getting_started#setting_up_api_access_clients)创建一个。创建服务帐户后，下载 JSON 文件并将其放在驱动器中。

您需要记下文件 ID。一旦选择了*打开*选项，您就可以在 URL 中找到它。这个文件包含了访问 [Google Play 开发者 API](https://developers.google.com/android-publisher) 所需的一切。

接下来，创建一个新的 Google 工作表，根据您想要从评论中提取的信息设置您的列名。

开始编写脚本；选择*工具、*，然后选择*脚本编辑器*。这将打开一个在线 IDE，您可以在其中编写来自 JavaScript 的 Apps 脚本。

在这里，您需要生成一个令牌来访问 Google Play 开发者 API。为此，我们将使用一个名为 *cGOA* 的便利库。点击*资源*，然后点击*库*，添加库。

搜索`MZx5DzNPsYjVyZaR67xXJQai_d-phDA33`并添加。完成后，您应该添加以下代码并运行以生成令牌(这将只需要完成一次):

generateGoogleToken

要检索令牌，请添加以下代码:

使用 cGoa 检索令牌

然后，可以调用`Goth.getToken(‘PlayConsole’)`来获取令牌。您现在可以访问 Android Publisher 范围，我们可以在其中检索评论。

# 使其可用

您可以通过拨打以下电话来检索用户在过去一周留下的评论的分页列表:

获取评论

您现在可以开始用这些数据填充电子表格了。首先，将结果转换成可用的对象并打开电子表格:

打开电子表格

接下来，检测电子表格的底部，并迭代填充相关列的数据。该函数如下所示:

解析数据并填充电子表格

这使用了一个助手函数:

getColByName

正如您所看到的，我们对评论执行一些其他功能，比如颠倒顺序和计算累积平均值。

我们还检查我们还没有添加评论。运行您的代码，您应该看到电子表格开始填充。

Apps 脚本真正酷的地方在于你可以用多种方式运行它。或者像刚才一样在编辑器中，或者您可以向电子表格添加一个按钮，或者设置一个自定义触发器。

要将按钮添加到电子表格中，请运行以下代码:

添加按钮

要添加触发器，前往 https://script.google.com/home 的[，从三点菜单中选择你的脚本和触发器。有几种类型的触发器可以在计划的时间运行或从电子表格事件运行。例如，我们运行自定义按钮菜单代码`onOpen`。](https://script.google.com/home)

有了电子表格中现成的评论，我们现在可以考虑自动对它们进行分类或进行情感分析。我们将在未来几个月对此进行调查。

# 来源

*   [演练:服务账户](http://ramblings.mcpher.com/Home/excelquirks/goa/walkthroughs/serviceaccount)
*   [安卓发行商:评论](https://developers.google.com/android-publisher/api-ref/reviews)