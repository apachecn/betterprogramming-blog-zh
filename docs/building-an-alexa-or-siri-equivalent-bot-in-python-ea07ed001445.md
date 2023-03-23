# 使用 OpenAI 用 Python 构建一个相当于 Alexa 或 Siri 的机器人

> 原文：<https://betterprogramming.pub/building-an-alexa-or-siri-equivalent-bot-in-python-ea07ed001445>

## 构建人工智能助手的分步指南

![](img/f613663681172181af44209ba0a4c53d.png)

[来源](https://unsplash.com/photos/mbq0qL3ynMs)

```
**Table of Contents**Introduction
[APIs and Design](#23d0) 
[Code Breakdown](#fd1e)
[Conclusion](#e7f6)
```

# 介绍

构建一个听语音、回答问题的 AI 程序并没有你想象的那么难。通过利用一些 python 包和 API，我们可以在一个下午创造奇迹。

以下是我和机器人之间的一些问答:

我:巴西的人口是多少？

Bot:巴西的人口估计超过 2.09 亿

我:冰淇淋是什么味道？

机器人:大多数冰淇淋尝起来都是甜的，像奶油一样

我:我怎么用筷子？

机器人:要用筷子，你必须一手拿一只。将筷子放在你惯用手的拇指和食指之间，用中指固定住。将筷子放在你非惯用手的拇指和食指之间，用无名指和小指夹住。拿起食物时，用惯用手的筷子夹住食物，然后使用筷子

*诚然，这些不是最有启发性的答案。还有最后那个筷子的有点奇怪，lol。然而，这个应用程序可以翻译语音和回答问题的事实，无论看起来多么有限，在我看来都是惊人的。与主流的人工智能辅助机器人不同，我们可以看到引擎盖下的东西，并与之玩耍。*

# 这个程序做什么

*   当用户准备好提问时，通过命令提示符运行文件
*   Pyaudio 使电脑麦克风能够拾取语音数据
*   音频数据存储在一个名为“stream”的变量中，然后被编码并转换成 JSON 数据
*   JSON 数据被发送到 AssemblyAI API 转换成文本。然后文本数据被发送回来
*   文本数据被发送到 OpenAI API，以便导入 text-davinci-002 引擎进行处理
*   问题的答案将被检索并显示在您的问题下方的控制台上

# API 和高级设计

## 本教程使用了两个核心 API:

*   将音频转录成文本。
*   [OpenAI](https://openai.com/api/) 解读问题并返回答案。*我还注意到，你也可以利用 OpenAI 的 Whisper API 来执行转录功能。*

## 设计(高级)

这个项目分为两个文件:`main`和`openai_helper`。

“main”脚本用于语音到文本 API 连接。它包括设置一个 WebSockets 服务器，填充 pyaudio 所需的所有参数，并创建在我们的应用程序和 AssemblyAi 的服务器之间并发发送和接收语音数据所需的异步函数。

`openai_helper`文件很短，仅用于连接开放的 ai“text-da Vinci-002”引擎。此连接用于接收我们问题的答案。

# 代码分解

## `main.` py

首先，我们导入应用程序将使用的所有库。其中一些可能需要安装 Pip，这取决于您是否使用过它们。请参见下面的上下文注释:

然后我们设置我们的 pyaudio 参数。这些输入是默认设置，可以在网上的不同地方找到。如果需要的话，可以随意试验，但是默认设置对我来说很好。我们将流变量设置为音频数据的初始容器，然后将默认的输入设备参数打印为字典。字典的键反映了 PortAudio 结构的数据字段。代码如下:

接下来，我们将为发送和接收创建多个异步函数，以便将我们的口头问题转换成文本。这些函数同时运行，这使得语音数据能够转换为 base64 格式，转换为 JSON，通过 API 发送到服务器，然后以可读格式接收回来。WebSockets 服务器也是下面脚本的一个重要部分，因为这使得直接流尽可能无缝。

最后，我们有到 openai 的简单 API 连接。如果你看一下上面要点的第 44 行(`main3.py`)，你可以看到我们正在从另一个文件中提取函数`ask_computer`，并使用输出作为我们问题的答案。

# 结论

对于任何有兴趣尝试 Siri 或 Alexa 功能的技术的人来说，这是一个不错的项目。不需要太多的编码经验，因为我们利用 API 来完成我们的处理。如果任何读者想了解更多关于这些技术的知识，我强烈推荐这个项目的[回购](https://github.com/ahershy/Virtual_assistant)分支，并亲自体验。干杯！

*更新:如果你对这个项目感兴趣，请查看另一个类似的* [*项目*](/building-an-application-that-turns-voice-into-text-into-image-9efca8713e29) *。这与使用语音到文本转录来创建 dalle-mini 图像有关*

```
**Want to Connect?**Please check out some of my other articles if you found this one helpful/interesting.[*How to find land when you’re at sea using Python*](https://towardsdatascience.com/how-to-find-land-when-youre-at-sea-using-python-48111e5d9795)[*Using Python to make Journaling Effortless*](https://python.plainenglish.io/using-python-to-make-journaling-effortless-b1454753943a)[*Optimize your portfolio using math and Python*](https://link.medium.com/fk39T8NB8mb)
```