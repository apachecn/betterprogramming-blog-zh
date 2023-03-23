# 探索 Android 中的 Jetpack CameraX 库

> 原文：<https://betterprogramming.pub/exploring-the-jetpack-camerax-library-in-android-26e4911f92d2>

## 集成>相机预览>图像捕捉

![](img/14d8d94e33ff2c19f8ba09c8ecef8c56.png)

罗伯特·舒涅夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

CameraX 是一个 Jetpack 支持库，使 Android 开发人员可以轻松实现相机功能。有了 CameraX 库，我们不再需要为修复不同 Android 版本的问题而烦恼，我们也不必担心手动的生命周期管理。

如果你是一名 Android 开发者，你会知道这有多重要。即使对于经验丰富的开发人员来说，处理 Android 版本的摄像头问题也无疑是一场噩梦。

CameraX 带有用例的概念，为开发人员提供了一种专注于他们真正想要的东西的方式，而不是管理整个相机设置。您应该知道三个主要的使用案例:

*   [预览](https://developer.android.com/training/camerax/preview):在显示器上显示图像。
*   [图像拍摄](https://developer.android.com/training/camerax/take-photo):拍摄并保存高质量图像。
*   [图像分析](https://developer.android.com/training/camerax/analyze):无缝访问算法中使用的缓冲区，例如传递到 MLKit。

在本文中，我们将关注第一个和第二个用例。第一个和第二个用例的组合将服务于 90%的 Android 开发任务。所以，是时候编码了。

# 综合

本质上，我们需要集成三个库:camera-camera2、camera-lifecycle 和 camera-view。在应用程序级`build.gradle`文件的依赖关系节点下添加以下几行。看看下面的代码:

```
// CameraX core library using camera2 implementation
implementation "androidx.camera:camera-camera2:1.0.1"// CameraX Lifecycle Library
implementation "androidx.camera:camera-lifecycle:1.0.1"// CameraX View class
implementation "androidx.camera:camera-view:1.0.0-alpha27"
```

*   **摄像头-摄像头 2 —** 访问核心摄像头功能。
*   **摄像机生命周期—** 使摄像机及其操作具有生命周期意识。
*   **摄像机视图—** 将摄像机视图集成到布局文件中。

# 布局中的摄像机视图

首先，我们需要通过`PreviewView`将摄像机视图添加到我们的布局中。`PreviewView`是一个定制视图，显示 CameraX 用例的摄像机提要。看一下代码:

# 处理权限

要在应用程序中显示预览，我们需要在清单文件中添加 CAMERA 权限。为此，请在清单文件中的应用程序标记内添加以下行。看看下面的代码:

```
<uses-permission android:name="android.permission.CAMERA" />
```

现在我们需要在运行时处理权限。每当用户启动应用程序时，我们需要检查摄像机的访问。如果我们有权限，我们将启动摄像头显示；如果没有，我们应该提示用户启用相机权限。看看下面的代码:

# 相机预览

现在我们有了相机访问，我们需要做的就是从 CameraX 实现一个预览用例。首先，我们需要创建实例`ProcessCameraProvider`来将视图与活动/片段生命周期绑定。看一下代码:

```
val cameraProviderFuture = ProcessCameraProvider.getInstance(this)
```

然后，我们需要给`cameraProviderFuture`添加一个监听器。它有两个参数— `Runnable`和`Executor`。看一下代码:

```
cameraProviderFuture.addListener(
    Runnable {}, 
    ContextCompat.getMainExecutor(this)
)
```

在下一部分你会看到`Runnable`的用法，而`[ContextCompat](https://developer.android.com/reference/kotlin/androidx/core/content/ContextCompat).getMainExecutor()`作为第二个参数。这将返回一个在主线程上运行的`[Executor](https://developer.android.com/reference/java/util/concurrent/Executor)`。

现在，我们进入`Runnable`。首先需要加上`ProcessCameraProvider`；它将相机的生命周期与`LifecycleOwner`绑定在一起。然后，我们需要创建一个预览对象，在布局中将表面链接到取景器，并指定要使用的默认相机。

最后，在 try/catch 块中，我们需要从`cameraProvider`中解除所有绑定，然后绑定我们创建的`cameraSelector`和`preview`对象。看一下代码:

仅此而已！我们已经完成了预览用例。现在，如果您运行应用程序并允许摄像头访问，您应该能够看到摄像头视图。

# 图像捕捉

下一步是捕捉图像并将其保存在存储器中。首先，为了捕获图像，我们需要在 Android 组件中声明`ImageCapture`。看看下面的代码:

```
private var imageCapture: ImageCapture? = null
```

然后我们需要在`startCamera`函数内部初始化它，并将其作为第三个参数添加到`cameraProvider`中。代码如下:

```
imageCapture = ImageCapture.Builder()
        .build()cameraProvider.bindToLifecycle(
        this, cameraSelector, preview, imageCapture)
```

为了捕捉图像，接下来我们需要定义一个保存图像的位置。看一下代码:

现在我们使用`imageCapture`实例来获得可修改的图像捕获用例的稳定引用。然后生成一个空文件来保存捕获的图像。最后，我们使用`takePicture`函数捕获图像并保存。看一下代码:

单击捕获按钮时调用此函数。

当我们把它们放在一起时，请看下面的代码:

# 结论

这里有一个 GitHub 示例。请随意使用`feature/image_capture`分支中的代码。

[](https://github.com/SG-K/CameraXSample) [## GitHub - SG-K/CameraXSample

### 在 GitHub 上创建一个帐户，为 SG-K/CameraXSample 的开发做出贡献。

github.com](https://github.com/SG-K/CameraXSample) 

我最近看到了[萨提亚·帕万·坎塔马尼](https://medium.com/u/d255c4fc9d76?source=post_page-----4c9f44d04e68--------------------------------)发表的这篇关于在安卓系统中渲染 PDF 的优秀文章，强烈推荐:

[](https://medium.com/android-dev-hacks/rendering-pdf-documents-in-android-using-pdfrenderer-f6d4f730b18) [## 使用 PdfRenderer 在 Android 中呈现 PDF 文档

### 如何使用 Android PdfRenderer

medium.com](https://medium.com/android-dev-hacks/rendering-pdf-documents-in-android-using-pdfrenderer-f6d4f730b18) 

目前就这些。希望你学到了有用的东西。

感谢阅读。