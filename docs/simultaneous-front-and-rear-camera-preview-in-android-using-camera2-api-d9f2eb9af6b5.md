# 如何使用 Camera2 API 在 Android 中同时使用前置和后置摄像头预览

> 原文：<https://betterprogramming.pub/simultaneous-front-and-rear-camera-preview-in-android-using-camera2-api-d9f2eb9af6b5>

## 在你的应用程序中同时使用两个摄像头

![](img/bca6250ef0e2f5e3da0682b995cd5b66.png)

迈克尔·斯科克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

不久前，我被分配了一项任务，从一台运行 Android 的设备上同时获取前后摄像头的反馈。像往常一样，我去了 Stack Overflow，然后去了 GitHub，然后去了其他博客，只是意识到这次我可能要孤军奋战了。很难受，对吧？

在能够解决这个问题之后，我花了一些时间去帮助那些可能发现自己陷入同样处境的人。

我为本教程制作了一个示例应用程序。本教程中分享的所有代码片段都来自应用程序本身。如果您不理解任何一个片段，您可以参考整个合并文件。您可以在 GitHub 上找到本教程的示例应用程序:

[](https://github.com/ananthrajsingh/front-rear-simultaneous-camera) [## ananthrajsingh/前后同步摄像机

### Android 应用程序可以同时预览前置和后置摄像头。…

github.com](https://github.com/ananthrajsingh/front-rear-simultaneous-camera) 

如果你对 Android 相机完全陌生，那么 [android/camera-samples](https://github.com/android/camera-samples) 库将是一个很好的起点。

注意:Java 实现已经从 [android/camera-samples](https://github.com/android/camera-samples) 中移除。对于 Java 实现，可以参考这个[分叉库](https://github.com/ananthrajsingh/camera-samples)。

对于本教程，假设您能够自己实现一个摄像机提要。如果不是这样，请访问上面提到的存储库。教程会更有意义。否则，你可能会发现自己迷路了。

# 设置预览视图

我们将需要两个单独的视图来呈现来自两个相机的预览。我们将从创建视图来展示预览开始:

现在，在打开我们的摄像机之前，我们需要确保它们的[texture view](https://developer.android.com/reference/android/view/TextureView)都可以使用 [TextureView 进行渲染。表面纹理拉伸器](https://developer.android.com/reference/android/view/TextureView.SurfaceTextureListener)。

我们将关注点完全分离。两个 TextureViews 都有自己的侦听器。听众将异步打开相关的摄像机，而不依赖于其他摄像机。关注点的分离达到了这样的程度，即使一个摄像机由于某种原因不能显示预览，另一个也可以完美地运行。

当视图可用时，听众将使用`onSurfaceTextureAvailable(SurfaceTexture, Int, Int)`打开各自的摄像机:

# 打开两个摄像头

我们的`openCameraFront(int, int)`和`openCameraRear(int, int)`功能将在实际打开相机之前设置前后相机参数。这些参数对于不同的相机是不同的。因此，它们需要单独设置。我们在谈论什么参数？参数如:

1.  摄像机 ID
2.  传感器方向
3.  相机所需图像的宽度和高度

这是在下面的函数中实现的。附上仅与前置摄像头相关的代码(后置摄像头也类似):

现在我们已经为前后摄像头设置了参数，我们准备打开它们。

打开相机是一个异步过程。使用`CameraDevice.StateCallback`来报告和处理成功或失败，T3 在打开摄像机时作为参数传递，并且不可为空。

确保摄像机的权限已授予该应用程序，并且没有其他更高优先级的应用程序正在使用任何摄像机。否则，相机打开请求将失败。这将通过在 [CameraDevice 中抛出一个](https://developer.android.com/reference/android/hardware/camera2/CameraDevice.StateCallback#onError(android.hardware.camera2.CameraDevice,%20int)) [CameraAccessException](https://developer.android.com/reference/android/hardware/camera2/CameraAccessException) 来报告。各摄像机设备的 StateCallback#onError 。

此时，设备将尝试同时打开两个摄像头。如果我们得到了摄像设备。StateCallback #为两台摄像机打开了回调，我们已经成功打开了它们。我们可以使用日志检查成功或失败，并在遇到任何挑战时进行调试。作为参考，这是我如何在这个项目中为前置摄像头实现`CameraDevice.StateCallback` (后置摄像头也类似):

# 从两台相机获得预览

我们已经完成了大部分繁重的工作。我们只剩下将来自摄像机的反馈引导到各自的视图。为此，我们必须创建一个[表面](https://developer.android.com/reference/kotlin/android/view/Surface)，并把它交给我们的[摄像设备](https://developer.android.com/reference/android/hardware/camera2/CameraDevice)。现在，根据 Surface，CameraDevice 调整其硬件处理管道，以按照要求的大小给出输出。

因为我们现在只需要从相机中得到预览，我们的 TextureViews 将作为我们的表面。我们将分别为前置和后置摄像头创建一个 CameraCaptureSession，传递各自的表面以获得预览:

仅此而已。你可以访问 [CameraFragment.kt](https://github.com/ananthrajsingh/front-rear-simultaneous-camera/blob/master/app/src/main/java/com/ananth/frontrearcamera/fragment/CameraFragment.kt) 来查看所有这些合并的工作代码。如果 Camera2 API 是一个傻瓜相机，那么它就像一个 DSLR。这可能看起来势不可挡，但它的美妙之处在于它提供了对摄像机镜头的精细控制。

![](img/d869d417d2e34332278f335eb714b1ab.png)

# 结论

如果一个人理解了相机 2 的复杂性，他就能想出如何战略性地同时使用两个相机。我鼓励你试着一键同时从两台相机上捕捉图像。

如果您有任何建议、反馈或问题，我很乐意听取您的意见。