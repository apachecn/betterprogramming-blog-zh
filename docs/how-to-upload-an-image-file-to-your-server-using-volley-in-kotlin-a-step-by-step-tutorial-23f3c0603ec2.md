# 使用 Kotlin 中的凌空将图像或文件上传到您的服务器

> 原文：<https://betterprogramming.pub/how-to-upload-an-image-file-to-your-server-using-volley-in-kotlin-a-step-by-step-tutorial-23f3c0603ec2>

## 循序渐进的教程

![](img/77930dd80526171846b6c4fba8923d0c.png)

由 [Angela Compagnone](https://unsplash.com/@angelacompagnone?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/android?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

作为一种跨平台、静态类型、通用的编程语言，Kotlin 在 Android 开发者中越来越受欢迎。它与 Java 代码具有完全的互操作性，并支持类型推断，以使其语法更加简洁，而不牺牲可读性。重要的是，Kotlin 是由 Kotlin 基金会开发和维护的，该基金会由谷歌赞助，谷歌是一家已经赢得大多数(如果不是所有)程序员信任的科技公司。

如果你选择 Kotlin 来开发你的 Android 应用程序，你可能会发现自己要处理的一个常见任务是必须上传一张图片，比如用户资料缩略图，到你的服务器上。

我将向您展示一种简单的方法来完成这项工作，使用凌空，一个 HTTP 库，可以实现 Android 应用程序的快速网络请求。

# 第一步:设置

要在应用中使用凌空库，您需要将凌空依赖项添加到应用的 build.gradle 文件中:

```
dependencies {
    ...
    implementation 'com.android.volley:volley:1.1.1'
}
```

当然，您的应用程序需要通过在 AndroidManifest.xml 文件中添加权限来获得使用互联网的权限。

```
<uses-permission android:name="android.permission.INTERNET"/>
```

出于本教程的目的，我简单地创建了一个名为`imageButton`的按钮来启动图库，一个名为`imageView`的图像视图来预览照片，另一个名为`sendButton`的按钮将图像上传到服务器。布局文件如下所示:

# 步骤 2:选择并处理图像

使用带有`image/*`类型的`Intent`将允许您打开图片库。使用此`intent`，使用`startActivityForResult`功能开始活动，这将允许访问图库中选择的图像。

MainActivity.kt 文件

一旦选择了图像，就可以通过实现`onActivityResult`的功能来访问它。具体来说，我们必须访问图像的 Uri，通过这个 Uri，我们在`imageView`中显示图像，并为以后的数据上传创建一个`ByteArray`。

# 第三步。创建一个支持多部分上传的凌空请求子类

要将图像数据上传到服务器，我们需要使用多部分表单请求。为了做到这一点，我们首先创建了一个继承了凌空的请求类的子类。下面是代码:

让我强调一些关键功能。首先，您需要将主体内容类型指定为 multipart/form-data 和使用的边界，后者是服务器正确解析数据所必需的。第二，参数和数据都需要写入 ByteArray，它可以作为 POST 请求的主体发送。

# 第四步。上传您的图像

现在您已经有了影像数据和多部分请求类，您可以上传影像了。当然，您的服务器需要有正确的 API 来响应这个 HTTP POST 请求。出于本教程的目的，我将只使用我在网上找到的一个免费的 HTTP POST 测试服务([https://ptsv2.com/](https://ptsv2.com/))。虽然它是免费的，但请尽量节约使用，并且仅用于测试目的。

作为上面 MainActivity.kt 文件的一部分，需要注意的一件重要事情是，您需要覆盖 request 类的 getByteData 函数。具体来说，您需要为请求提供您的图像数据，并且该类将具有额外的函数来将图像数据转换为上传数据所需的 ByteArray 格式。

在测试网站(【https://ptsv2.com/】)的“厕所”里，你应该会看到你的数据转储。您刚刚使用凌空上传了您的图像！

# 结论

凌空图书馆是一个强大的图书馆，使 HTTP 请求更容易实现。您可以子类化 Request 类来满足您的其他需求，包括一些标准需求(例如，json 请求、string 请求)。这里给出的例子可以很容易地用于上传文件而不是图片。

与 Kotlin 一起快乐编码！