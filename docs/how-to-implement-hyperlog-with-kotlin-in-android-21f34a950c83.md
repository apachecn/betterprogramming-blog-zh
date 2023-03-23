# 如何在 Android 中用 Kotlin 实现 HyperLog

> 原文：<https://betterprogramming.pub/how-to-implement-hyperlog-with-kotlin-in-android-21f34a950c83>

## 关于如何使用 Kotlin 实现 HyperTrack/HyperLog 并将数据记录到远程服务器的详细指南

![](img/efc0dc87a958bbe7d6b621220affa64a.png)

由[法扎德·纳兹菲](https://unsplash.com/@euwars?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

当开发人员需要在代码的任何部分调试问题时，记录是必要的。在 Android 中，我们有[日志](https://developer.android.com/reference/android/util/Log)，这在查找代码中的错误时非常有用。如果你想远程查看或保存日志到你的服务器，那么[HyperLog](https://github.com/hypertrack/hyperlog-android)*是一个流行的选项，因为它是建立在 Log 之上的。*

*现在，有许多博客文章可以帮助你理解什么是 HyperLog，以及它是如何使用 Java 工作的(比如[这个](https://android.jlelse.eu/android-remote-logger-library-for-debugging-343443bd38b7))。今天的文章是使用 Kotlin 实现它的详细指南，并探索了它的特性，所以如果您正在努力理解它或者将 Java 代码转换成 Kotlin，那么这应该会把事情搞清楚。*

*一旦将库添加到应用程序的*`build.gradle`文件的依赖项中，您就可以开始记录日志消息，并通过以下代码片段将它们推送到远程服务器:**

**使用 Kotlin 实现 HyperLog 的示例代码片段**

**上面的代码片段来自一个应用程序的启动器活动，HyperLog 已在该应用程序中初始化。日志级别设置为显示将进一步执行哪种类型的日志记录。在使用标签和消息指定日志类型之后，URL 已经被设置为验证日志向远程服务器的推送(在本例中是 [RequestBin](https://requestbin.com/) )。在通过文件或纯文本推送日志之后，回调方法 success 和 error 完成任务。**

**下面是 Kotlin 的 HyperLog 图书馆的详细指南。**

# **初始化**

```
**HyperLog.initialize(context: Context, expiryTimeInSecond: Int, logFormat: LogFormat)**
```

**这将初始化应用程序中的超级日志功能。默认情况下，七天或七天以上的日志会被自动删除。因此，在这段时间内或立即将它们推送到远程服务器。上下文是该方法的强制参数。其他的可以在初始化的时候删除。**

# **记录结构**

```
**HyperLog.setLogFormat(logFormat: LogFormat)**
```

**这定义了自定义日志消息格式。自定义日志消息非常有用。开发人员可以选择他们在日志中需要的细节以及日志的结构。**

```
**HyperLog.setLogLevel(logLevel: Int)**
```

**根据[文档](http://parseplatform.org/Parse-SDK-Android/api/com/parse/PLog.html):**

> **"设置要显示的日志记录级别，其中每个级别包括它下面的所有级别。默认级别是 LOG_LEVEL_NONE。请确保在部署您的应用程序之前将其设置为`Log.ERROR`或`LOG_LEVEL_NONE`，以确保不会记录敏感信息。”**

```
**HyperLog.d(tag: String, message: String)**
```

**像其他日志级别一样，如果使用了 debug，那么`d`就是用来调试的。同样，根据`Log.ASSERT`、`Log.VERBOSE`、`Log.DEBUG`、`Log.INFO`、`Log.WARN`、`Log.ERROR`中指定的日志级别，分别使用`a`、`v`、`d`、`i`、`w`、`e`和异常。异常也使用了 throwable。**

# **设置和获取 URL**

```
**HyperLog.setUrl(url: String)**
```

**此方法设置需要将日志推送到的有效 URL 端点。如果它是空的或 null，它抛出一个非法的参数异常。**

```
**HyperLog.getUrl()**
```

**当通过`HyperLog.setUrl()` **、**设置 URL 时，它可用于整个应用程序。如果应用程序的其他部分需要这个 URL，那么`HyperLog.getUrl()`从库中保存的变量中获取这个 URL。此方法以字符串形式返回。**

# **获取到期时间**

```
**HyperLog.getExpiryTime()**
```

**如果为日志设置了任何过期时间，此方法将返回以秒为单位的时间长度。否则，它返回默认到期时间(即 604800)。**

# **获取设备日志**

```
**HyperLog.getDeviceLogs(deleteLogs: Boolean, batchNumber: Int)**
```

**该方法根据批号获取设备中存储的日志。如果另有规定，默认批号为 1。此外，如果`deleteLogs`设置为`true`，则在获取后删除日志。因为默认设置为`true`，所以在不需要删除日志的情况下，必须设置`false`。**

```
**HyperLog.getDeviceLogsAsStringList(deleteLogs: Boolean, batchNumber: Int)**
```

**该方法以字符串列表的形式获取存储的日志，而不是如上所述的设备日志模型(在`getDeviceLogs`方法中)。此方法的其余功能与前面的方法相同。**

```
**HyperLog.getDeviceLogsInFile(context: Context, fileName: String, deleteLogs: Boolean)**
```

**该方法使用提供的文件名将存储的日志作为文件对象获取，或者根据自己的时间戳创建文件名。删除日志的方式与上面的其他两种方法相同。**

# **日志可用性**

```
**HyperLog.hasPendingDeviceLogs()**
```

**如果设备中有任何可用的日志，该方法将返回`true`,以防有任何挂起的日志可用。否则返回`false`。**

```
**HyperLog.getDeviceLogsCount()**
```

**此方法返回存储的设备日志的数量(如果有)。否则，它返回`0`。**

```
**HyperLog.getDeviceLogBatchCount()**
```

**此方法返回存储的设备日志批次的数量(如果有)。否则返回`0`。每批包含 5，000 个设备日志。**

# **将日志推送到服务器**

```
**HyperLog.pushLogs(context: Context, filename:String, additionalHeaders: HashMap<String, String>, compress: Boolean, hlCallback: HLCallback)** 
```

**如果`compress`为`true`，该方法将日志以文本文件或 gzip 压缩文件的形式从设备推送到服务器。否则也可以推送简单的文字，保持`false`。推送日志后，它们会自动从设备中删除。HyperLog 的一个好处是，如果日志计数大于设备的日志查询限制，那么它会被批量推送。此外，如果没有包含文件，就不需要在方法中包含文件名或额外的头，但是它们的存在使方法变得完美。该方法提供了`onSuccess`和`onError callback`函数，可以根据需要处理成功和错误。**

# **删除日志**

```
**HyperLog.deleteLogs()**
```

**此方法有助于从设备中删除所有日志。**

# **结论**

**我希望这能让你对这个库提供的功能有更多的了解。**

**感谢阅读。伐木快乐！**