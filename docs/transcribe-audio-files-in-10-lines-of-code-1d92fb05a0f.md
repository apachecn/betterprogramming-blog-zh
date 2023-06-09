# 用 10 行 Python 代码转录音频文件

> 原文：<https://betterprogramming.pub/transcribe-audio-files-in-10-lines-of-code-1d92fb05a0f>

## 最先进的语音转文本技术

![](img/e7aca417da9885d59bb2d0db001a7e2b.png)

丹·克里斯蒂安·pădureț在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

人类总是拥有在不同背景和情况下理解口语的牵强附会的能力，例如从对话中区分背景噪音，识别音频中的多个说话者等。看到最近在自然语言处理(NLP)，特别是语音识别方面的进步，日复一日地慢慢接近类似人类的理解能力，这是势不可挡的。因此，支持语音的应用程序在我们的日常生活中变得越来越普遍。

然而，在自然语言处理中，语音识别是一个非常棘手的问题。客观地说，我见过的最大挑战是没有足够的音频文件。即使一个人有这些，他们也可能是原始的，混乱的，吵闹的，或者有时甚至是腐败的。此外，音量可能会有问题，有时高，有时低，或者单个音频中存在多个扬声器，等等。因此，从这些数据中生成文本并不容易。

如果您计划在项目中利用语音到文本技术，从头开始构建端到端语音识别模型可能不是最佳的方法，这可能是由于缺乏相关的专业知识或者时间和成本的限制。然而，现在有太多的选项可供您选择，只需通过一个 API 调用就可以提供如此复杂的解决方案。

在接下来的几节中，我将演示如何使用 AssemblyAI API 和 Python 转录音频文件。

我们开始吧。

# 转录音频文件

在本节中，让我带您了解如何使用 AssemblyAI 的 API 来转录音频文件。作为一个介绍性的帖子，我们将坚持异步音频转录，即有一个预先录制的文件可供转录。

## **第一步:安装要求**

就库需求而言，您需要的只是 Python 中的`[requests](https://pypi.org/project/requests/)`包，您可以这样做:

```
pip install requests
```

## **步骤 2:获取您的 API 令牌**

下一步是在`[AssemblyAI](https://app.assemblyai.com/signup)`(免费)网站上创建一个账户。这将为您提供您的私有 API 密钥，我们将使用它来访问语音到文本模型。

## **第三步:转录**

一旦有了 API 密匙，就可以继续利用 AssemblyAI 的转录服务和音频智能特性。你将需要一个音频文件，可以通过一个网址访问，如 AWS S3 桶或上传到音频托管服务，如 SoundCloud。出于我们的目的，我们将使用以下音频文件:

使用 AssemblyAI API 从音频文件生成输出(或转录)分三步完成:

*   **认证**

身份验证是使用授权头实现的，它应该包含在所有 API 请求中。这是按如下方式完成的:

注意:'`YOUR-API-TOKEN`'指的是您帐户的仪表板中提到的唯一的私有 API 密钥。

*   **提交文件进行转录**

完成身份验证后，下一步是调用 HTTP Post 请求，如下所示:

传递给 **POST** 请求的参数有:

1.  *端点*指定要调用的转录服务。
2.  一个 *JSON* ，带有你的音频文件的 URL。
3.  *头*保存 API 密钥和内容类型。

*   **获取转录结果**

在您提交一个文件进行转录后，它的状态(上面 JSON 响应中的一个键)将从“**排队**变为“**处理中**，最后变为“**完成**”

在这里，您可以发出 GET 请求，如下所示，以检查您发布的转录请求的状态:

传递给 GET 请求的参数有:

1.  端点指定调用的服务和 API 调用标识符。
2.  头像以前一样保存 API 密钥。

根据音频长度，转录时间可能会有所不同。因此，您必须重复进行 GET 请求，直到状态变为“**已完成**或“**错误**”一旦状态键显示为“ **Completed** ”，您将看到文本、单词和其他键，包括您启用的任何音频智能特性(如上所述)的结果，以及 JSON 响应中填充的转录结果。JSON 响应将如下所示:

请注意，上述响应中的状态是“**已完成**”以下是关于转录的一些见解:

1.  由于音频文件属于单个扬声器，我们看到`words`键内的所有`speaker`键都是**无**。
2.  由 AssemblyAI 模型解释的音频的整个转录结果存储在响应的`text`键中。
3.  我们可以看到所有单个单词和整个转录文本的置信度得分。分数范围从 0 到 1，0 为最低，1 为最高。
4.  由于在 POST 请求中没有指定 AssemblyAI 提供的任何音频智能特性，我们看到了与这些关键字(language_detection、perspective _ analysis、chapters 等)相对应的值。)为假。

## 完整代码

总之，在这篇文章中，我们讨论了 AssemblyAI API 来帮助你转录和理解音频。具体来说，我们讨论了它能为您做什么，以及如何对预先录制的音频文件使用异步转录服务。

在接下来的文章中，我将从技术和实践的角度讨论它的具体用例，比如情感分析、实体检测等等。因此，请继续关注。

下次见。感谢阅读。