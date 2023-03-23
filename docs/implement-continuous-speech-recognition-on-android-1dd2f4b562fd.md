# 在 Android 上实现连续语音识别

> 原文：<https://betterprogramming.pub/implement-continuous-speech-recognition-on-android-1dd2f4b562fd>

## 说出单词来触发应用程序中的语音识别

![](img/29a7949f7181b35bee0d803c92a77c87.png)

照片由[粘土堤](https://unsplash.com/@claybanks?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

语音识别在过去几年里获得了很大的发展。当构建一个您认为语音识别会提升您的用户体验的应用程序时，您可以:

*   集成`[SpeechRecognizer](https://developer.android.com/reference/android/speech/SpeechRecognizer)` API。
*   利用谷歌助手。

在您的 Android 应用程序中实现`SpeechRecognizer`非常简单。我将在本文后面提供一个详细的实现。

然而，我们想要连续的语音识别。不幸的是，API 没有提供使用关键字触发语音识别的机制。所有的语音识别系统都是基于这种模式，无论是谷歌助手的“Ok Google”，iOS 的“Hey Siri”，还是亚马逊设备的“Alexa”。

为此，第二种选择应该符合我们的需求。可悲的是，谷歌助手仍然是一个封闭的 API，并没有提供许多可能性。它提供了 [App 动作](https://developers.google.com/assistant/app/overview)，但是你不会用它实现连续语音识别。

当我第一次看到`[VoiceInteractionService](https://developer.android.com/reference/android/service/voice/VoiceInteractionService)`时，我很兴奋。它似乎做了我想要的`[AlwaysOnHotwordDetector](https://developer.android.com/reference/android/service/voice/AlwaysOnHotwordDetector)`。不幸的是，它通过让你集成一个自定义助手而与谷歌助手紧密绑定。

到目前为止，我们可以使用`SpeechRecognizer`,但我们仍然需要从用户交互中触发语音识别。

# 激活热门关键字的语音识别

我们想将这个过程分成几个步骤:

1.  激活语音识别。
2.  倾听热门关键词。
3.  在检测到关键字时，听用户的声音。
4.  抓住单词，产生结果。
5.  停用语音识别。

我们通过将热门关键词检测与实际语音识别分离来利用`SpeechRecozigner`。

## 基本语音识别设置

如果您的目标是 SDK 23 或更高版本，您必须请求在您的应用程序中录制音频的权限。

AndroidManifest.xml 内部:

```
<uses-permission android:name="android.permission.RECORD_AUDIO" />
```

在您将使用语音识别的活动中:

然后，您需要创建一个语音识别器对象，并为其附加一个侦听器。您将能够响应来自这些回调的事件。确保语音识别在您的用户设备上可用:

创建后，您可以启动识别器。您需要提供一个配置它的意图。这里有一个你可以使用的可能性，但是一定要检查`RecognizerIntent`标志。其中一些可能更适合您的用例。

## 大声说时间！

你准备好开口了。因为您已经实现了`[RecognitionListener](https://developer.android.com/reference/android/speech/RecognitionListener)`，所以仔细看看下面的回调:

API 一理解单词就通知你，不管是部分识别还是完全识别。您只需要解析它们，就大功告成了！

完成后，请记住停止侦听并销毁您的识别器。

## 检测关键字

如果我们回顾我们的步骤，我们已经省略了第二步和第三步。有了基本的设置，你已经准备好了一切——我们只需要将聆听过程分成两个部分。

首先，我们需要添加一个标志来区分对关键字的监听和其他监听。姑且称之为`isActivated`。

开始监听，等待在`onResults`回调中捕捉你的关键词。一旦找到，它将返回匹配项:

有了这段代码，只要标志关闭，您就可以监听关键字。您必须重新启动识别，因为系统在找到匹配项时会将其关闭。

当检测到关键字时，打开标志。下次你说话的时候，你会拿到火柴的。

# 有什么条件？

你开始想知道它将如何影响你的申请了吗？会影响性能吗？穿线呢？

简而言之:它消耗大量电池，线程效率不高。

关于电池，除了尽可能高效地听，没什么能做的。此外，确保在执行完任务后停止监听，并记住销毁识别器。

我对线程处理有更高的期望。可悲的是，API 迫使我们从主线程调用它的所有方法。所以，忘记在`Service`中提取这个吧。

> "这个类的方法只能从主应用程序线程中调用."

简而言之，你可以执行连续语音识别，只要你知道它将如何影响你的应用程序和你的用户。我强烈建议您在应用程序中隔离这种机制，以减轻其影响。

如果你想用这个特性来引导你的应用程序，我已经做了一个库，可以帮助你。

[](https://github.com/StephenVinouze/KontinuousSpeechRecognizer) [## Stephen winouze/kontinuus speech recognizer

github.com](https://github.com/StephenVinouze/KontinuousSpeechRecognizer)