# 如何用干净的架构格式化 Android 中所有 API 调用可能出现的错误

> 原文：<https://betterprogramming.pub/how-to-format-all-possible-errors-from-api-call-in-android-with-clean-architecture-b435fbd00b0a>

## 最大限度减少应用崩溃

![](img/38d5bb655e4105739a184baa43e25811.png)

在 Android 中执行 API 调用不仅仅是检查互联网连接。虽然有几个流行的开源库可用，如 several 和凌空，这有助于简化这些操作和错误处理。但是重要的是，您能够格式化库在这些 API 调用失败时抛出的所有可能的错误。

本文将向您展示如何处理在进行 API 调用时可能抛出的所有错误类型，以防止您的应用程序崩溃。在本次演示中，我将使用以下工具:

1.  [协程流程](https://developer.android.com/kotlin/flow)
2.  [带句柄的依赖注入](https://developer.android.com/training/dependency-injection/hilt-android)
3.  [房间](https://developer.android.com/training/data-storage/room)和
4.  [天鸽](https://github.com/skydoves/sandwich)

**依赖集成:**

将以下依赖项添加到您的`build.gradle`文件(应用程序模块):

在插件中配置你的手柄来使用`kapt`和你的`build.gradle`(项目模块)。你可以使用我上面贴的链接做到这一点。请不要忘记添加改造的依赖项和您可能在项目中使用的其他库。

**创建数据类:**

对于这个示例，我将使用天气响应数据，只是为了显示不同位置的天气状况列表。

**创建一个接口**

复制并粘贴下面的代码，向天气 API 发出请求。

注意:我使用`ApiResponse`作为`weatherRemoteEntity`数据类的包装器，而不是改造中的普通调用，这将使您能够格式化调用该 API 可能产生的所有可能的响应。

**配置改装生成器:**

为了从改造服务中获取数据源，我们将向改造构建器添加一个`CallAdapterFactory`。因此，将下面的代码添加到您的改造建设者。

**创建数据状态:**

创建一个数据状态密封类，用于处理不同的状态和响应发出。(可选)

**创建存储库类:**

这是我们将要做的所有错误格式化和发送数据到`ViewModel`的地方，以便在活动中显示。下面是我们的存储库的样子。

Sandwich 使您能够从改进的网络响应中构建标准化接口，这允许您处理主体数据、错误和异常情况，显然是在多层架构中使用有用的运算符。

`ApiResponse`有三种主要类型；`Success`、`Failure.Error`和`Failure.Exception`。

*   `ApiResponse`。成功—这表示网络请求已经成功。您可以从`**ApiResponse.Success**`中获得响应的主体数据，以及其他信息，如`**StatusCode**`、`**Headers**`等。
*   `ApiResponse.Failure.Error` —这表示网络请求因错误请求或内部服务器错误而失败。您可以从`ApiResponse.Failure.Error`获得一条错误消息和附加信息，如`StatusCode`、`Headers`等。这几乎涵盖了所有可能抛出的服务器响应。这样您就可以检查错误响应并适当地格式化它们。下面是他们的列表。

*   `ApiResponse.Failure.Exception` —表示创建请求或处理客户端响应时出现意外异常，如网络连接失败，网络请求失败。您可以从`**ApiResponse.Failure.Exception**` 中获得一条异常消息，并适当格式化以避免应用程序崩溃并显示合适的消息。

此外，除了我上面演示的格式之外，还可以用其他不同的方式使用 skydoves 库来实现 API 响应的平滑错误格式化和处理。你可以看看下面的链接。

[](https://github.com/skydoves/sandwich) [## GitHub-sky doves/三明治:🥪一个轻量级的密封 API 库，用于建模改造响应和…

### 发明三明治是为了从改造网络响应中构造标准化接口。三明治让你…

github.com](https://github.com/skydoves/sandwich) 

下面也是天气 app 完整实现的附件，可以随意克隆看看。

[](https://github.com/sunday58/TopupMama) [## GitHub - sunday58/TopupMama:一个用 Kotlin 构建的带有最喜欢的位置选择的天气应用程序

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/sunday58/TopupMama) 

感谢阅读，编码快乐。