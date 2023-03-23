# 如何用 NDK 读取 Android 中 C++的一个图像文件

> 原文：<https://betterprogramming.pub/how-to-read-an-image-file-in-c-in-android-with-ndk-5d837039da3a>

## 在 Android 的原生层中直接读取任何文件的不同方法

![](img/7fc89c4359b77d253f623b447569a54b.png)

由[奥拉夫瓦尔](https://unsplash.com/@olafval)在 [Unsplash](https://unsplash.com/photos/UTk9cXzYWAg) 拍摄的照片。

Android 中的软件开发可以使用 Java SDK 或[原生开发工具包，又名 NDK](https://developer.android.com/ndk) ，由 Android 开源项目(AOSP)提供。NDK 通常用于编写高性能代码，如图像处理算法。

许多应用程序需要从磁盘读取文件。对于读取图像文件，通常的方法是使用 Android SDK 中可用的 Java APIs 读取文件，或者使用像`MediaStore`API 这样的高级抽象。在本文中，我不会介绍在 Java 层中读取不同文件格式的信息。

有时，可能需要在本地层(C++)处理图像文件。在这种情况下，通常的方法是执行以下操作:

*   将图像作为[位图](https://developer.android.com/reference/android/graphics/Bitmap)加载。
*   和 JNI 一起把它送到原生层。
*   在本地层进行读/写操作。

但是，在某些情况下，您可能希望直接在本机层中读取图像。如果你有这样的情况——这篇文章是给你的！

> 参考消息:当我说“本机层”或“本机代码”时，它指的是 C++代码。我可能会在文章中交替使用这些术语。

此外，虽然这篇文章主要是关于在 C++中读取图像文件，但是这些概念可以很容易地推广到在 Android 的原生层中读取任何文件格式。

在开始介绍步骤和代码示例之前，还有一个问题需要解决。

*为什么首先要读取原生层中的图像？*

我将在“如何做”之后介绍它

有人告诉我，并不是每个人都对我经常提到的“为什么”感兴趣。

*被老婆(-_-)！*

请告诉我事实是否如此。

# 如何读取原生层中的图像

如果你正在阅读这篇文章，我希望你熟悉 Android 开发基础、NDK、Java 本地接口(JNI)等概念。

我希望您也熟悉 Android 中的作用域存储概念。

为了改善对外部存储上的应用程序和用户数据的保护，Android 收紧了应用程序访问 Android 上的文件的方式。

**TL；DR** 不要求过多权限，就不能再访问文件。这对用户有好处！使用文件选择器，您仍然可以要求用户授予对特定文件的权限。

所以我们不再用`File`了。在 Android 中处理 [Uri](https://developer.android.com/reference/android/net/Uri) 更具扩展性。

让我们从读取图像文件的 [Uri](https://developer.android.com/reference/android/net/Uri) 开始。

## 获取要读取的图像的 Uri

您可以使用[Mediastore](https://developer.android.com/reference/android/provider/MediaStore)API 或使用文件选择器类的 UI 来获取文件的 [Uri](https://developer.android.com/reference/android/net/Uri) 。

一个简单的图像拾取器可以像这样在`Activity`中实现:

带有图像拾取器的 Android 活动示例。

对于本文的其余部分，我将假设您已经有了 Uri[和](https://developer.android.com/reference/android/net/Uri)。

下一步是从这个 [Uri](https://developer.android.com/reference/android/net/Uri) 中获取文件描述符。

## 从 Uri 获取文件描述符

在 Unix 和类 Unix 操作系统中，文件描述符(FD)是文件或其他 IO 资源(如管道或网络套接字)的唯一标识符。它们通常具有非负整数值。负值用作误差值。

在 Android 中，我们可以使用 [Uri](https://developer.android.com/reference/android/net/Uri) 来获取相应的 [AssetFileDescriptor](https://developer.android.com/reference/android/content/res/AssetFileDescriptor) ，并使用它在 Java 层打开文件，获取其`FD`值。一旦我们获得本机`FD`值，我们就可以通过 JNI 将这个微小的整数值编组到本机层，以便直接读取文件。

**重要提示:**在这种方法中，资源由 Java 层打开，由本地层使用，请确保发生以下情况:

*   Java 层继续拥有文件，即本机层不应该关闭文件流。
*   Java 层保持文件打开，直到本机层读取了该文件或者不再需要打开该文件。

违反这些规则会导致意外的竞态条件。

下面是如何在 Java 层获得原生的`FD`:

在 Android 中从 Uri 获取 fd 值的代码片段。

## 通过 JNI 将 FD 值发送到本地层

对于其余的内容，我希望读者能够熟悉

*   用 Android 设置 JNI。
*   Android 中的 JNI 基础知识。

> *JNI 代表 Java 原生接口—* [*参考 Android*](https://developer.android.com/ndk/samples/sample_hellojni) *的 hello-jni 样例。*

因此，为了读取本地层中的文件，我们需要一个基本的 Java 库和一个相应的 JNI 文件。下面是一个 Java 库的例子:

JNI 图书馆的 Java 部分，用于阅读 JNI 的图像

假设我们有一个相应的名为`image-loader-jni.cc`的 JNI 文件，它被嵌入到通过构建 JNI 构建目标而创建的`libimage-loader-jni.so`二进制文件中。

用于读取原生层图像的 JNI 文件的框架。

# 读取本地层中的文件

并返回一些关于文件的信息

有多种方法可以解决这个问题。我将列举其中的两个:

## 在 NDK 用图像解码器读取图像

NDK 有 [ImageDecoder](https://developer.android.com/ndk/guides/image-decoder) API，可以读取 JPEG、PNG、GIF、WebP 等不同格式的图像。

**优点**

它是 NDK 的一部分，因此您可以执行以下操作:

*   跳过向项目添加另一个第三方本机依赖项的麻烦。
*   通过不添加第三方库获得隐式 APK 大小缩减。
*   因为它是平台的一部分，所以你可以免费获得关键更新(你这边不需要更新)。

还有，

*   支持多种图像格式，允许不透明地解码任意文件。

**缺点**

*   这是在 API 等级 30 中添加的，所以你只能针对这个版本以上的设备！
*   与位图类似，它将图像解码成一种`Bitmap`格式([示例](https://developer.android.com/ndk/reference/group/bitmap#androidbitmapformat))。默认情况下，图像以`ARGB_8888`格式解码(每像素 4 字节)。
*   这是一个不透明的图书馆。您不能为特定的文件格式输入解码器。

下面是如何使用它来读取图像并将一些信息返回给 Java 层。

> 我本可以在 JNI 代码本身中添加做任何事情的例子，但这不是石器时代，我们不是那种人。
> 
> 我们喜欢代码中的一些结构。所以，让我们写一个新的叫做‘Image’的库。

我们图像库的头文件

接下来，让我们实现解码来自`fd`的图像的逻辑。这将在`ImageFactory#FromFd(..)`下的`image.cc`中执行。

ImageFactory 在图像库中的实现

现在，使用 JNI 的这个库，阅读来自`fd`的图像。

这里还有一些提示:

*   你可以使用像`[AImageDecoder_setTargetSize](https://developer.android.com/ndk/reference/group/image-decoder#aimagedecoder_settargetsize)`这样的 API 直接读取重新缩放的图像。
*   同样，可以使用类似`[AImageDecoder_setCrop](https://developer.android.com/ndk/reference/group/image-decoder#group___image_decoder_1gaf281588607767ff1232c704c7f7d57ec)`的 API 在消费前进行裁剪。

尽管在实践中，我发现`[AImageDecoder_setTargetSize](https://developer.android.com/ndk/reference/group/image-decoder#aimagedecoder_settargetsize)`比我预期的下采样操作要慢。如果您担心这个 API 的性能，并且您手头有另一种方法，请尝试分别加载全分辨率图像和下采样`Image`。

通过解释和代码示例，您可以获得原生层中图像解码的工作版本。

以下是进一步阅读的一些理由:

*   您希望在原生层解码图像，但是您有许多使用 Android <= API 30.
*   You want to read something other than an image.
*   You have a custom and better decoder of your own.
*   You are a curious reader, you knowledge hog!
*   We still have the pending Mr. Elephant in the room to address.

## Read image with custom decoders

The following approach can be used to read any file using 【 value, and then you can use your own decoder to decode the image.

For this example, I’ll assume you have some decoder of your own, and it’s implemented underneath 【 implementation. Let's assume an interface. Here’s the code:

Updated interface of image library with a method to decode Image from string image buffer.

## **的客户以 Unix 方式读取文件！**

对于这种方法，在进入本机层细节之前，我们需要来自 Java 层的两条附加信息。

1.  `fd`的`start offset`(很有可能`0`除非你不想从头开始读文件)。您可以使用`[AssetFileDescriptor#getStartOffset()](https://developer.android.com/reference/android/content/res/AssetFileDescriptor#getStartOffset())` API 来实现这一点。
2.  `length`的文件。您可以使用`[AssetFileDescriptor#getLength()](https://developer.android.com/reference/android/content/res/AssetFileDescriptor#getLength())` API 得到这个。

在 Java 层获得这些信息后，通过 JNI 将其发送到本机层。对于下面的例子，我假设你想解码一个图像文件，你的解码器可以处理它。看起来是这样的:

使用 fd 读取图像并使用我们的图像解码器进行解码的代码片段。

如果您的解码器支持缓冲数据，您也可以使用上述方法读取缓冲区中的图像文件。

# 为什么应该(或不应该)在本机层读取文件

![](img/889413e20bd0d9879991dd277be8649d.png)

房间里的大象——作者使用[稳定扩散](https://huggingface.co/spaces/stabilityai/stable-diffusion)生成的图像。使用稳定扩散生成的图像在[creativml Open RAIL-M](https://huggingface.co/spaces/CompVis/stable-diffusion-license)下由[huggingface.co](https://huggingface.co/spaces/stabilityai/stable-diffusion)免费提供。

那么，为什么您可能希望在本机层而不是 Java 层读取文件是您的问题。

然而，我想根据我的经验，使用不同用例的例子来澄清一些误解。

## 在本地层使用图像

如果您关心的是 Java 层中读取或解码图像的高延迟。注意，Android Java SDK 还附带了 [ImageDecoder，它也有一个 Java API](https://developer.android.com/reference/android/graphics/ImageDecoder) 。相同的本机实现可能支持这一点。您可以使用这些 API 将图像读取为 [Drawable](https://developer.android.com/reference/android/graphics/drawable/Drawable) 或 [Bitmap](https://developer.android.com/reference/android/graphics/Bitmap) 。

对于您可能想要在原生层中进行的任何后期处理，您可以轻松地编组对原生层的`Bitmap`引用。NDK 对[位图](https://developer.android.com/ndk/reference/group/bitmap)有很好的支持，允许你用很少的开销在 Java 和 Native 之间处理它们。

> 我计划在另一篇文章中写更多关于它的内容。

与 Java 层相比，在本机层使用`ImageDecoder`可能不会带来延迟方面的好处。如果你有一些解码器实现可以比 NDK 库更快地处理解码，你可能会得到延迟的好处。

仍然在原生层读取文件的一个有效理由可能是在不需要的时候在 Java 层中去掉讨厌的`Bitmaps`。

比如你想读取一个图像，做一些后期处理，编码成 JPEG 格式保存到磁盘，就可以避免在 Java 中持有一个`Bitmap`引用。

我经常发现持有大量的`Bitmaps`会导致明显的性能问题——可能是因为当`Bitmap`不再被引用时，我们不得不依靠 GC 来回收它们持有的内存。GC 可能不总是按预期工作。然而,`[Bitmap#recycle()](https://developer.android.com/reference/android/graphics/Bitmap#recycle())` API 也可以帮助你做到这一点。

## 避免跨 JNI 边界的文件数据的编组

如果下列情况属实，最好使用本机方法:

*   您需要读取可选的文件格式，并且您已经为它定制了解码器实现。通过这种方式，您可以避免首先在 Java 层以`String`的形式读取它，然后通过 JNI 将它编组到本机层。

我不是 100%确定数据编组如何跨越 Java 原生边界，但这是 Android 开发者网站在 JNI 发布的避免编组大量数据的 [#1 提示。](https://developer.android.com/training/articles/perf-jni#general-tips)

相反，将`fd`传递给本机层“可能”更有性能。

## 使用纯 C++库

这和上面的观点类似。如果您有第三方库来解码您的图像或自定义文件格式，而这些格式不是 Java 版本或纯 Java 版本的，那么使用共享方法来读取文件将是一个好主意。

## 比起 Java，你更喜欢 C++

无可奉告；我听到了！

> 做你认为合适的事——因为这个世界是你的画布！

# 参考

*   [文件描述符—维基百科](https://en.wikipedia.org/wiki/File_descriptor)
*   [asset file descriptor—Android 文档](https://developer.android.com/reference/android/content/res/AssetFileDescriptor)
*   [引用 Android 的 hello-jni 示例](https://developer.android.com/ndk/samples/sample_hellojni)
*   [关于图像解码器的 NDK 指南](https://developer.android.com/ndk/guides/image-decoder)
*   [安卓开发者网站 JNI 提示](https://developer.android.com/training/articles/perf-jni#general-tips)

*原载于*[*https://blog . minhazav . dev*](https://blog.minhazav.dev/how-to-read-an-image-file-in-c++-in-android-ndk/)