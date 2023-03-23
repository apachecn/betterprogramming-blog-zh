# 在 Jetpack Compose 中请求权限的两种方法

> 原文：<https://betterprogramming.pub/jetpack-compose-request-permissions-in-two-ways-fd81c4a702c>

## 在 Jetpack Compose 中请求权限的最佳方式

![](img/2cde646642a6be64b0dca5b8297a129d.png)

照片由 [Pathum Danthanarayana](https://unsplash.com/@pathum_danthanarayana) 在 [Unsplash](https://unsplash.com/) 上拍摄

我们首先要申请的是拍照许可。要在我们的设备上使用摄像头，我们需要申请许可，因为它属于“危险”设备，对吗？首先，我们需要将该权限添加到清单文件中，如下所示:

```
<uses-permission android:name=”android.permission.CAMERA” />
```

之后，我们可以进入我们的 Composable，在其中我们需要请求这个权限，并为活动结果创建一个启动器，如下所示:

我们为活动结果创建了一个启动器，我们将通过它启动一个 Android 内置的权限请求对话框。Activity 返回一个布尔值，它告诉我们是否授予了权限。如果它被批准，我们打开相机，如果不是，我们可以显示一些信息对话框或任何你想要的。之后，我们将添加一个按钮，其`onClick`回调调用`checkAndRequestCameraPermission`函数。这个函数看起来像这样:

首先，我们检查许可是否已经被授予，如果是，我们打开摄像机，如果不是，我们通过启动器请求它。最后，我们的主要组件看起来像这样:

现在我们将请求两个位置许可。我们可以同时请求它们，而不需要一个接一个地请求它们。权限是`ACCESS_COARSE_LOCATION`和`ACCESS_FINE_LOCATION`T9。众所周知，第一步是将它们添加到我们钟爱的清单文件中:

```
<uses-permission android:name=”android.permission.ACCESS_COARSE_LOCATION” />
<uses-permission android:name=”android.permission.ACCESS_FINE_LOCATION” />
```

然后，我们将在主组件中添加一个活动结果启动器和一个请求这些权限的按钮。像这样:

现在我们有了一组权限和一个请求多个权限的启动器活动。这个活动将向我们展示一个内置的对话框，请求我们需要的每一个权限。它返回一个 String(权限名)作为键和 Boolean(授权与否)作为值的映射。功能`checkAndRequesstLocationPermissions`看起来是这样的:

它将检查每个权限是否被授予。如果它们都被授予，我们可以使用位置，如果没有，我们将请求那些我们需要的。

# 伴奏者权限库

很酷吧，现在我要给你看更好的东西。那就是[伴奏者权限库](https://github.com/google/accompanist/tree/main/permissions)，它们总是让我们的生活更轻松。我们可以使用它们的`permissionState`和`PermissionRequired`组合来更好地操纵请求的结果。首先，我们将在应用程序级的`build.gradle`文件中添加依赖关系:

```
implementation “com.google.accompanist:accompanist-permissions:0.23.1”
```

**注:**在撰写本文时，0.23.1 是最新的稳定版本。检查是否有更新的版本。

我们将请求相同的相机权限，因此不要忘记从您的手机中删除应用程序或清除应用程序存储。首先，我们需要创建`permissionState`，它将跟踪我们权限的状态:

```
val permissionState = rememberPermissionState(permission = Manifest.permission.CAMERA)
```

之后，我们将创建`CameraPermission` composable，并将权限状态作为参数传递给它，这样它就可以处理权限。在这个 composable 内部，我们使用伴奏者的`PermissionRequired` composable，它接受四个参数:
- `permissionState`:我们之前创建的权限的状态
- `permissionNotGrantedContent`:权限被拒绝后应该显示的内容
- `permissionNotAvailableContent`:权限不可用时应该显示的内容
- `content`:权限被授予后将被调用的 composable 回调
`CameraPermission` composable 看起来是这样的:

**注意:**我们需要添加`ExperimentalPermissionsApi`注释，因为`PermissionState`仍然是实验性的。

所以基本上在我们的主 composable 中，它看起来像这样:

在按钮`onClick`回调中，我们只需用我们的`permissionState`发出一个许可请求，非常简单。现在，我们将请求多个位置权限。类似地做了一些修改。同样，我们首先创建权限状态:

**注:`permissionsState`的**类型现为`MultiplePermissionState`，之前为`PermissionState`。

接下来是创建包含`PermissionsRequired`伴奏者的可组合对象的`LocationPermissions`。它与`PermissionRequired`类似，区别仅在于现在接受的第一个参数`MultiplePermissionState`为参数。

仅此而已。在我们的 main composable 中，我们只启动多个权限请求:

酷！正如你所见，伴奏者权限库给了我们更多的灵活性，使我们的生活更加轻松。你可以在[我的 GitHub repo](https://github.com/Giga99/MediumRepos) 中找到所有的源代码。

这就是这篇文章的全部内容，希望你喜欢！

```
Connect with me on:[Github](https://github.com/Giga99)
[Twitter](https://twitter.com/igor_s1999)
[LinkedIn](https://www.linkedin.com/in/igor-stevanovic/)
[Portfolio website](https://giga99.github.io/portfolio-website/)
```