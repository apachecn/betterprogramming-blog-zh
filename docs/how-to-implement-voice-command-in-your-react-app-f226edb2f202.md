# 如何在 React 应用中实现语音命令

> 原文：<https://betterprogramming.pub/how-to-implement-voice-command-in-your-react-app-f226edb2f202>

## 使用语音命令控制嵌入式视频

![](img/94b192b28950d7aed942467ac521f49c.png)

托马斯·勒在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

语音命令是如何工作的？你能用语音命令控制视频吗？如何在 React 应用中实现语音命令功能？这些是我在开发一个嵌入视频的应用程序时问自己的问题。我希望能够使用语音命令免提控制视频。

我遇到了一个常见的问题:我没有找到为我的特定用例设置它所需的步骤的资源。我没有发现一个网站有内置的语音命令来控制内容，我也没有发现许多博客走过设置这种功能的过程。

尽管如此，现在我已经找到了一个解决方案，下面是我关于如何在 React 应用程序中设置语音命令功能来控制视频的演练。

# 1.集成 Web 语音 API

对于语音识别，我使用了 React 钩子 [react-speech-recognition](https://www.npmjs.com/package/react-speech-recognition) ，它在幕后使用 Web Speech API。这让我可以非常快速地使用电脑麦克风的功能来跟踪用户在说什么。

要安装它，请在终端中使用以下命令:

```
npm install — save react-speech-recognition
```

然后在 React 文件的顶部，使用以下代码导入它:

```
import SpeechRecognition, { useSpeechRecognition } from 'react-speech-recognition'
```

我的应用程序是一个食谱和烹饪网站，我有一个每个食谱嵌入的视频。我想使用语音命令来控制那个视频，所以我将这个语音识别功能导入到我的`Recipe Detail Page`组件中。对于您的项目，将其直接导入到将访问语音识别功能的组件中。

# 2.查找并保存视频元素

下一步是瞄准你想要控制的视频。在我的应用中，我通过我的 Rails API 访问视频文件，我使用 [Cloudinary](https://cloudinary.com/) 来管理视频上传和存储。我将组件的状态设置为 recipe 对象，如下所示:

从 RecipeDetail.js 组件文件中的 fetch 设置状态

使用 state，我能够使用来自 state 的视频作为源，在组件的`return`语句中创建一个视频元素:

RecipeDetail.js 组件文件中的 Return 语句

使用`video`的 ID，我创建了一个变量:

```
let video = document.getElementById('video')
```

现在我们有了一个访问视频元素的变量，我们准备好设置命令了。

# 3.设置语音命令

所有视频都有标准控制的编码命令，包括播放、暂停、快进等。在做研究时，我发现 W3 的一个[示例站点已经设置了所有的视频命令，它们能够通过使用按钮来控制视频。在检查每个按钮后，我发现了如何使用命令来控制视频，我可以在我的应用程序中应用这些命令:](https://www.w3.org/2010/05/video/mediaevents.html)

在 RecipeDetail.js 组件文件中设置语音控制命令

首先，我设置了一组命令。我决定播放，暂停，倒回十秒，快进十秒，静音和取消静音。我还包含了一个用于语音命令脚本的`clear`命令，但是更多的是在测试和实现过程中使用。对于每个命令，您包括要监听的命令词以及实际控制嵌入视频的回调函数。

然后我设置了两个回调函数(一个名为`listen`，一个名为`stopListening`，用于切换语音识别的开关。打开后，`SpeechRecognition`将从电脑的麦克风中拾取脚本，并聆听您设置的特定命令。关闭后，`SpeechRecognition`将停止收听，并且不再跟踪记录。

如您所见，我还调用了以下内容:

```
setVoiceOn(true)
```

并且:

```
setVoiceOn(false)
```

除了在我的 recipe 详细信息页面上将 recipe 对象的状态设置为 access 之外，我还有一个语音命令的状态:

```
const [voiceOn, setVoiceOn] = useState(false);
```

正如你在我的`return`声明中所提到的，我已经设置了一个显示按钮的开关。当`voiceOn`为`false`时，app 上出现一个按钮，可以让你“打开语音命令”。当`voiceOn`为`true`时，按钮将变为允许您“关闭语音命令”这是使用`listen`和`stopListening`功能打开和关闭麦克风的地方。

在遵循关于设置 react-speech-recognition 的 [npm 指南](https://www.npmjs.com/package/react-speech-recognition#basic-example)中，有一个使用命令的示例实现，因此您也可以参考它。

就是这样！

一旦你设置了这些与你的视频相关的命令，你就可以运行你的应用程序并进行测试了！请随意观看我的演示，了解语音命令的实际效果(跳到 2:53):

# 来源

[](https://www.npmjs.com/package/react-speech-recognition#basic-example) [## 反应语音识别

### 一个 React 挂钩，将来自麦克风的语音转换为文本，并使其可用于您的 React 组件…

www.npmjs.com](https://www.npmjs.com/package/react-speech-recognition#basic-example)  [## HTML5 视频

### 这个页面演示了新的 HTML5 视频元素、它的媒体 API 和媒体事件。在…中播放、暂停和搜索

www.w3.org](https://www.w3.org/2010/05/video/mediaevents.html)