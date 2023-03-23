# 在 iOS 和 Android 上使用 Firebase ML 套件识别文本

> 原文：<https://betterprogramming.pub/recognizing-text-with-firebase-ml-kit-on-ios-android-8b9c538715df>

## 用 Firebase 的 ML 工具包实现文本识别的实用指南

在我的上一篇文章中，我谈到了什么是 [Firebase ML Kit](https://firebase.google.com/docs/ml-kit/) ，我们对它的所有特性做了一个简单的介绍。

如果你错过了这篇文章，下面是它的链接:

[](https://medium.com/kulizau/ok-ml-kit-how-smart-are-you-83c9e58b7330) [## “好吧，ML Kit，你有多聪明？”

### ML 工具包的介绍以及为什么您应该了解更多关于它的信息。

medium.com](https://medium.com/kulizau/ok-ml-kit-how-smart-are-you-83c9e58b7330) 

在本文中，我将介绍如何在 ML 工具包中为您的 iOS 和 Android 应用程序实现*文本识别*功能。

开始之前，请确保:

*   你已经在你的项目中包含了 Firebase。你可以在 [iOS 这里](https://firebase.google.com/docs/ios/setup)和 [Android 这里](https://firebase.google.com/docs/android/setup)找到如何做到这一点。
*   如果您打算使用基于云的 API，那么您已经启用了它们。

如前一篇文章所述，你必须升级到 [Blaze plan](https://firebase.google.com/pricing/) 才能使用基于云的 API。

升级后，您会在项目的 Firebase 仪表板的 ML Kit 页面上找到启用基于云的 API 的选项。

![](img/7f25c52a38ce406954eca389692d8b48.png)

用于文本识别和条形码扫描的设备上 APIs 用于图像标注的基于云的 API

# ios

## 第一步:包括豆荚

对于 iOS，您需要包含两个 pod 中的一个，`MLVision`或`MLVisionTextModel`。

如果您计划使用云 API，请使用`MLVision`，如果您只想使用设备上的 API，请使用`MLVisionTextModel`。

把它们包含在你的`Podfile`里，就像这样:

```
pod 'Firebase/Core'# On-device API
pod 'Firebase/MLVisionTextModel'# Cloud-based API
pod 'Firebase/MLVision'
```

一旦你在`Podfile`中包含了这些 pod，运行`pod install`命令来安装这些 pod。

## 步骤 2:导入 Firebase

在您的应用程序中，在需要使用 Firebase 的地方导入它，如下所示:

```
import Firebase
```

## 步骤 3:获取`Vision`的一个实例

我们在这里谈论的是 Firebase 的`Vision`:

```
let vision = Vision.vision()
```

## 步骤 4:获取文本识别器的实例

一旦获得了`Vision`的实例，就需要获得文本识别器，如何获得取决于您使用的 API。

```
// On-device API
let recognizer = vision.onDeviceTextRecognizer()// Cloud-based API
let recognizer = vision.cloudTextRecognizer()
```

这是负责处理图像和识别其中文本的组件。

## 步骤 5(可选):配置您的文本识别器以检测某些语言

您还可以将文本识别器配置为仅识别特定语言或特定语言集的文本。

```
let options = VisionCloudDocumentTextRecognizerOptions()// Setting languages to English, French & Hindi
options.languageHints = ["en", "fr", "hi"]// Create your text recognizer with the above options
let recognizer = vision.cloudDocumentTextRecognizer(options: options)
```

## 第六步:让你的形象成为一个`VisionImage`

像这样将`UIImage`作为参数传递给`VisionImage`:

```
let visionImage = VisionImage(image: UIImage)
```

在 Firebase 文档中有其他方法可以得到`VisionImage` [。](https://firebase.google.com/docs/ml-kit/ios/recognize-text)

## 步骤 7:用你的文本识别器处理你的`VisionImage`

既然已经有了文本识别器和图像，就可以通过将图像传递给`process(_:completion:)`方法来处理图像并获得结果。

这个方法返回一个`VisionText`对象。

这是一个关于如何从图像中检测文本的教程。ML 工具包中有一个选项，您可以从文档图片的图像中检测文本。

在 Firebase 文档中了解更多信息[。](https://firebase.google.com/docs/ml-kit/ios/recognize-text)

# 机器人

## 步骤 1:添加 Firebase ML Vision 作为依赖项

对于 Android，您需要在应用程序级的`build.gradle`文件的`dependencies`块中包含 ML 视觉依赖，如下所示:

```
implementation 'com.google.firebase:firebase-ml-vision:19.0.3'
```

## 步骤 2:自动下载文本识别 ML 模型

只有在使用设备上的 API 时，才需要遵循这个步骤。

将以下代码块添加到您的`AndroidManifest.xml`文件中，可以确保从 Play Store 下载您的应用程序时，文本识别 ML 模型会自动下载:

```
<application ...>
  ...
  <meta-data
      android:name="com.google.firebase.ml.vision.DEPENDENCIES"
      android:value="ocr" />
</application>
```

## 步骤 3:获取文本识别器的实例

由于您的文本识别器是负责处理图像和识别图像中的文本的组件，因此您需要在执行任何其他操作之前获取该组件的实例:

```
// On-device API
val recognizer = FirebaseVision.getInstance().onDeviceTextRecognizer// Cloud-based API
val recognizer = FirebaseVision.getInstance().cloudTextRecognizer
```

## 步骤 4(可选):配置您的文本识别器以检测某些语言

您可以将文本识别器配置为仅识别特定语言或特定语言集的文本。

```
// Setting languages to English, French & Hindi
val options = FirebaseVisionCloudTextRecognizerOptions.Builder()
        .setLanguageHints(Arrays.asList("en", "fr", "hi"))
        .build()// Create your text recognizer with the above options
val recognizer = FirebaseVision.getInstance().getCloudTextRecognizer(options)
```

## 第五步:让你的形象成为一个`FirebaseVisionImage`

以下是你如何从一个`Bitmap`得到你的`FirebaseVisionImage`:

```
val image = FirebaseVisionImage.fromBitmap(bitmap)
```

## 步骤 6:用文本识别器处理 FirebaseVisionImage

在获得了`FirebaseVisionImage`和文本识别器之后，可以通过调用文本识别器上的`processImage()`方法来处理图像，如下所示:

同样，这是一个关于如何从图像中检测文本的教程。ML 工具包中有一个选项，您可以从文档图片的图像中检测文本。

你可以在 Firebase 文档中了解更多信息[。](https://firebase.google.com/docs/ml-kit/ios/recognize-text)

# 结论

这就是 Firebase ML 套件的文本识别功能！

以下是我的 ML 工具包系列中的所有文章:

[](https://medium.com/thecreateschool/ok-ml-kit-how-smart-are-you-83c9e58b7330) [## “好吧，ML Kit，你有多聪明？”

### ML 工具包的介绍，以及为什么你应该了解更多。

medium.com](https://medium.com/thecreateschool/ok-ml-kit-how-smart-are-you-83c9e58b7330) [](https://medium.com/thecreateschool/recognizing-text-with-firebase-ml-kit-on-ios-android-8b9c538715df) [## 在 iOS 和 Android 上使用 Firebase ML 套件识别文本

### 用 Firebase ML 工具包实现文本识别功能的实用指南。

medium.com](https://medium.com/thecreateschool/recognizing-text-with-firebase-ml-kit-on-ios-android-8b9c538715df)