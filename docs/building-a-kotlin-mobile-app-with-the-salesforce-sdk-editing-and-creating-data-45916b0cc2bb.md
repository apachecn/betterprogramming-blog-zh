# 使用 Salesforce SDK 构建 Kotlin 移动应用程序:编辑和创建数据

> 原文：<https://betterprogramming.pub/building-a-kotlin-mobile-app-with-the-salesforce-sdk-editing-and-creating-data-45916b0cc2bb>

## 因此，您可以连接您的应用程序和组织

![](img/575d7a36ab8221620f38cd78b8ccfc9c.png)

[阿里](https://unsplash.com/@alio2?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

这是我们三部分系列的第二部分，我们继续使用 Salesforce Mobile SDK 构建 Android 开发的基础。在[检查状态代码](/building-a-kotlin-mobile-app-with-the-salesforce-sdk-340f31e2dbf5#helpHead2)以发现来自 API 的潜在错误，但是为了简洁起见，我们省略了任何类型的响应检查。

在`MainActivity`中，我们需要利用这个适配器。首先，在类定义的顶部，将`listAdapter`改为`BrokerListAdapter`而不是`ArrayAdapter`，就像这样:

```
class MainActivity : SalesforceActivity() { private var client: RestClient? = null
   private var listAdapter: ArrayAdapter<String>? = null
   private lateinit var listAdapter: BrokerListAdapter
    ...
```

`lateinit`修饰符允许你在构造函数之外初始化一个非空类型。接下来，用这些函数替换两个`onResume`函数:

最后，我们需要跟踪经纪人的真实姓名以及他们的记录 Id。为此，我们可以简单地将它存储在`BrokerListAdapter`维护的字典中。在`sendRequest`中，用这个替换那里的`for`回路:

```
for (i in 0..records.length() - 1) {
   listAdapter.add(records.getJSONObject(i).getString("Name"))
   listAdapter.map(records.getJSONObject(i).getString("Name"), records.getJSONObject(i).getString("Id"))
}
```

继续运行应用程序，您的列表项将可编辑。做个编辑，然后准备好，因为事情就要变得疯狂了。回到你的临时组织，点击 Dreamforce 应用程序中的**经纪人**选项卡。您的编辑应该反映在 Salesforce 平台上！

# 添加数据

我们可以编辑记录，但是如果我们需要添加一个新的经纪人呢？这种格式非常类似于我们用来获取和编辑记录的逻辑。让我们快速浏览一下步骤。

打开`main.xml`，将这些线粘贴在`ListView`之前:

这里，我们添加了一个按钮，每当按下这个按钮时，就会调用一个名为`onAddBrokerClick`的函数。在`MainActivity`中，我们将定义该方法:

对，就是这样！我们定义的那些字段与定制对象直接相关。通过将`Id`设置为 null，我们告诉 API 这是一条新记录。如果您添加一个新行，编辑它，然后回到您的临时组织，您将看到新数据在线出现。

# 未完待续…

在我们上次完成的基础上，在这篇文章中，我们能够在我们的移动应用程序中编辑和添加经纪人数据，然后在 Salesforce 中看到它的反映。相当整洁！

然而，这只是单向的数据更改-从应用程序到组织。如何将数据从 org 发送到应用程序？另外，如何处理手机特有的网络问题，比如连接问题，或者多人同时换场？

我们将在最后的帖子中解决这些非常现实的问题！