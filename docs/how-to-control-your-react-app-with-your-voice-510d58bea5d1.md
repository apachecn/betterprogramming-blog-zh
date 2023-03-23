# 如何用声音控制你的 React 应用

> 原文：<https://betterprogramming.pub/how-to-control-your-react-app-with-your-voice-510d58bea5d1>

## 使用 Chrome 语音识别 API 构建声控界面

![](img/601d0a2a88709d63a2144eadb06ce7ef.png)

照片由 [Gift Habeshaw](https://unsplash.com/@gift_habeshaw?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/microphone?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

几个月前，我用 TensorflowJS 写了一篇关于[网络语音识别的文章。尽管实现起来非常有趣，但对你们中的许多人来说，扩展起来非常麻烦。原因很简单:如果你想检测比我提供的模型更多的单词，这需要训练一个深度学习模型，这是非常基本的。](https://towardsdatascience.com/speech-recognition-with-tensorflow-js-66608355376e)

对于那些需要更实际的方法的人来说，那篇文章是不够的。根据您的要求，我今天写了一篇关于如何使用 web Speech API 为您的 Web 应用程序带来完整的语音识别的文章。

但是在我们讨论实际的实现之前，让我们先了解一下这个功能可能有用的一些场景:

*   为无法使用键盘或触摸设备的情况构建应用程序。例如，在野外工作或戴着特殊手套的人可能会发现很难与输入设备进行交互。
*   来支持残疾人。
*   因为太牛逼了！

# 用语音识别支持 Web 应用的秘密是什么？

鼓…秘诀就是 Chrome(或 Chromium) [Web Speech API](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition) 。这个与基于 Chromium 的浏览器一起工作的 API 非常棒，它为我们做了所有繁重的工作，让我们只关心使用语音构建更好的界面。

不管这个 API 有多不可思议，截止到 2020 年 11 月，它还没有得到广泛的支持，根据您的需求，这可能是一个问题。以下是当前的支持状态，承蒙[允许，我可以使用](https://caniuse.com/speech-recognition)吗？此外，它只在联机时工作，所以如果您脱机，您将需要一个不同的设置。

自然，这个 API 可以通过 JavaScript 获得，并且它不是唯一的或者只限于 React。尽管如此，有一个很棒的 [React 库](https://github.com/JamesBrill/react-speech-recognition#readme)可以进一步简化 API，这就是我们今天要使用的。

如果您想在普通 JS 或任何其他框架上实现，请随意阅读 MDN 文档上的语音识别 API 文档。

# 你好，世界，我正在抄写

我们将从基础开始。我们将建立一个 Hello World 应用程序，它将实时转录用户所说的话。在做所有好的事情之前，我们需要一个良好的工作基础，所以让我们从建立我们的项目开始。为了简单起见，我们将使用 create-react-app 来设置我们的项目。

```
npx create-react-app voice-command
```

接下来，安装 [react-speech-recognition](https://openbase.io/js/react-speech-recognition/documentation) 库，包括:

```
npm i react-speech-recognition
```

接下来，我们将处理文件`App.js`。CRA 为我们创造了一个很好的起点。开个玩笑，我们不需要它，所以从一个空白的`App.js`文件开始，和我一起编码。

在我们做任何事情之前，我们需要`imports`:

```
import { useEffect } from 'react';
import SpeechRecognition, { useSpeechRecognition } from 'react-speech-recognition';
```

接下来，我们将创建一个简单的功能组件，带有几个按钮来与语音引擎交互。

很简单，对吧？让我们详细看看我们在做什么，从`[useSpeechRecognition](https://github.com/JamesBrill/react-speech-recognition/blob/master/docs/API.md#useSpeechRecognition)`钩开始。

这个钩子负责捕获语音识别过程的结果。这是我们获得预期结果的途径。最简单的形式是，我们可以提取麦克风启用时用户说话的`transcript`，如下所示:

```
const { transcript } = useSpeechRecognition();
```

即使我们激活了钩子，我们也不会立即开始听；为此，我们需要与开始时导入的对象`[SpeechRecognition](https://github.com/JamesBrill/react-speech-recognition/blob/master/docs/API.md#SpeechRecognition)`进行交互。这个对象公开了一系列帮助我们控制语音识别 API 的方法，开始在麦克风上听，停止，改变语言的方法，等等。

我们的界面只暴露了两个控制麦克风状态的按钮；如果您复制了所提供的代码，那么您的界面应该是这样的:

![](img/89dfea0a1fd793629d9dcec2125efc1e.png)

在[现场代码流](https://livecodestream.dev/post/2020-11-22-how-to-control-your-react-app-with-your-voice/)尝试现场演示

如果您尝试了演示应用程序，您可能会注意到，如果您在听完之后停顿了一下，您可能会遗漏一些单词。这是因为库默认设置了这个行为，但是您可以通过在`startListening`方法上设置参数`continuous`来改变它，就像这样:

```
SpeechRecognition.startListening({ continuous: true });
```

# 兼容性检测

我们的 app 很好看！但是如果你的浏览器不被支持会怎么样呢？对于那些场景，我们能有一个回退行为吗？是的，我们可以。如果您需要根据是否支持语音识别 API 来改变应用程序的行为，react-speech-recognition 有一种方法可以实现这一目的。这里有一个例子:

```
useEffect(() => {
    if (!SpeechRecognition.browserSupportsSpeechRecognition()) {
      alert("Ups, your browser is not supported!");
    }  
  }, []);
```

# 检测命令

到目前为止，我们介绍了如何将语音转换为文本，但现在我们将通过识别应用程序中预定义的命令来更进一步。构建这一功能将使我们有可能构建完全通过语音运行的应用程序。

如果我们需要构建一个命令解析器，这可能需要大量的工作，但是谢天谢地，语音识别 API 已经有了一个内置的命令识别功能。

如[反应语音识别文档](https://openbase.io/js/react-speech-recognition/documentation)所述:

> 为了在用户说出一个特定短语时做出响应，你可以向`useSpeechRecognition`钩子传递一个命令列表。每个命令都是一个具有以下属性的对象:
> 
> `command`:这是一个字符串或`RegExp`，代表你想听的短语。
> 
> `callback`:说出命令时执行的功能。该函数接收的最后一个参数将始终是包含以下属性的对象:
> 
> `resetTranscript`:将抄本设置为空字符串的函数
> 
> `matchInterim`:决定“临时”结果是否应与命令匹配的布尔值。这将使您的组件更快地响应命令，但也使误报更有可能-即，命令可能会在它没有说出时被检测到。这是默认的`false`,应该只为简单的命令设置。
> 
> `isFuzzyMatch` : Boolean，确定 speech 和`command`之间的比较是否基于相似性而不是精确匹配。模糊匹配对于容易发音错误或被语音识别引擎误解的命令(例如，地名、运动队、餐馆菜单项)很有用。它适用于不带特殊字符的字符串命令。如果`command`是带特殊字符的字符串或者是`RegExp`，模糊匹配时会转换成不带特殊字符的字符串。匹配命令所需的相似性可通过`fuzzyMatchingThreshold`进行配置。`isFuzzyMatch`默认为`false`。
> 
> `fuzzyMatchingThreshold`:当`isFuzzyMatch`开启时，如果语音与`command`的相似度高于此值，将调用回调。只有当`isFuzzyMatch`是`true`时，你才应该设置这个。它取值在`0` (将匹配任何内容)和`1`(需要精确匹配)之间。默认值为`0.8`。"

以下是如何为您的应用程序预定义命令的示例:

您也可以使用如下所示的动态命令:

# 结论

多亏了 Chrome 的语音识别 API，构建语音激活的应用程序再简单不过了，也更有趣了。希望在不久的将来，我们会看到这个 API 被更多的浏览器支持，并具有离线功能。那么它将成为一个非常强大的 API，可能会改变我们构建 web 的方式。

感谢阅读！