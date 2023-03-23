# 构建自定义语音助手

> 原文：<https://betterprogramming.pub/building-your-custom-voice-assistants-an-overview-of-the-current-solutions-and-integrations-d8db227a325>

## 当前解决方案和集成概述

![](img/b8c26c336dc2de4eac46c4dfff93a180.png)

照片由[西瓦·卡梅什](https://unsplash.com/@kamesi9?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/speaker?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

*这篇文章的更新版本可以在* [*Platypush 博客*](https://blog.platypush.tech/article/Build-custom-voice-assistants) *上找到。*

不久前，我写了一篇文章，描述了如何使用 RaspberryPi、platypush、扬声器和麦克风制作自己的谷歌语音助手。

它还展示了如果您不想说“Ok Google”，或者如果您希望不同的热门词汇以不同的语言触发不同的助手，如何创建您自己的自定义热门词汇模型来触发助手。它还展示了当某些短语被识别时，如何挂钩您自己的定制逻辑和脚本，而无需编写任何代码。

自从我写了那篇文章，一些事情已经改变了:

*   我写文章的时候，platypush 只支持谷歌助手作为语音后端。与此同时，我也致力于支持 Alexa 的[工作。如果你是 Alexa 的粉丝，可以随意使用 platypush 中的`assistant.echo`集成，但要记住，它比现有的基于谷歌助手的选项更有限 AVS(亚马逊语音服务)也有限制。例如，它不会提供检测到的文本的副本，这意味着它不可能插入定制的钩子或呈现的响应的副本，因为 AVS 主要使用音频文件作为输入，并提供音频作为输出。它也可能遇到一些小的音频故障，至少在 RasbperryPi 上是这样。](https://github.com/BlackLight/platypush/issues/80)
*   虽然不赞成，[Google Assistant 库的新版本已经可用](https://github.com/googlesamples/assistant-sdk-python/releases/tag/0.6.0)来修复 RaspberryPi 4 上的分段错误问题。在过去的一年里，我经常给开发者打电话，我很高兴这已经完成了！这是个好消息，因为助手库拥有我见过的最好的热词检测引擎。我试过的其他 SDK——Snowboy、DeepSpeech 或 pico voice——都没有接近原生的“Ok Google”热词检测准确性和性能。然而，并不是所有的消息都是好的:这个库仍然被弃用，目前还没有替代方案。新版本主要是为了响应用户的请求来修复新的 RaspberryPi。但是，至少在一段时间内，构建语音助手的最佳选择之一仍将有效。那些对构建一个 100%像原生谷歌助手一样的定制语音助手感兴趣的人可以阅读我的[前一篇文章](https://medium.com/swlh/build-your-own-customizable-voice-assistant-with-platypush-cede33725cba)。
*   与此同时，官方语音助手 SDK 的不稳定状况促使我研究更多最先进的替代方案。我是 [Snowboy](https://snowboy.kitt.ai/) 的长期粉丝，它有一个得到很好支持的 platypush 集成，我已经用它作为一个 hotword 引擎来触发其他助手集成很长时间了。然而，当谈到实时场景的准确性时，即使是最好的模型也不能令人满意。我还试验了 [Mozilla DeepSpeech](https://github.com/mozilla/DeepSpeech) 和 [PicoVoice](https://github.com/Picovoice) 产品，用于语音检测并在 platypush 中建立集成。在这篇文章中，我将尝试提供一个全面的概述，看看 DIY 语音助手目前可能实现的功能，并对我已经构建的集成进行比较。

# DIY 语音助手的案例

在廉价的谷歌或 Alexa 助手随处可见的情况下，为什么还会有人费心打造自己的语音助手呢？尽管这些产品已经变得如此普及，我还是决定用几个 DIY 助手为我的整个房子供电，原因如下:

*   **隐私**。最容易猜到的一个！我不确定房子里的一个麦克风，通过互联网连接到一家私营公司，一天五到十次互动，以切换灯泡，打开恒温器，或播放 Spotify 播放列表，是否是相称的价格。我在 platypush 中建立了语音助手集成，目标是让人们可以选择语音服务，而不用通过私有的盒子在私有的通道上发送所有的日常语音交互。
*   **兼容性**。谷歌助手设备只能与支持谷歌助手的设备一起工作。Alexa 驱动的设备也是如此。一些设备可能会失去一些语音功能，这可能是暂时的，取决于云连接的可用性，也可能是永久的，因为硬件或软件过时或其他商业因素。我梦想中的语音助手可以在任何设备上自然工作，只要它有一个 SDK 或 API 可以与之交互，并且不依赖于商业决策。
*   **灵活性**。即使当一个设备与你的助手一起工作时，你仍然受到双方已经同意并实现的功能的约束。通过语音命令实现更复杂的例程通常很棘手。在大多数情况下，它涉及创建将在云上运行的代码(以 Actions 或 Lambdas 或 IFTTT 规则的形式)，而不是在您自己的网络中运行，这限制了实际的可能性。我梦想中的助手必须能够在我想要的任何设备上运行我想要的任何逻辑，使用我想要的任何自定义快捷方式(即使有正则表达式匹配)，不管有多复杂。我还旨在建立一个助手，可以提供多种服务(谷歌，Alexa，Siri 等。)在同一台设备上使用多种语言，只需使用不同的热门词汇。
*   **硬件约束**。我从来都不理解销售嵌入麦克风和扬声器的塑料盒是为了进入语音服务的世界。这是展示想法的好方法。经过几年的实验，可能是时候期待行业提供可以在任何设备上运行的语音助手体验了，只要它有麦克风和可以处理代码的控制器单元。至于兼容性，应该没有兼容谷歌或兼容 Alexa 的设备。任何设备都应该与任何助手兼容，只要该设备有办法与外界通信。控制该设备逻辑应该能够在该设备所属的同一网络上运行。
*   **云端 vs .本地处理**。大多数商业语音助手的工作方式是定期捕获音频流，通过云服务扫描音频块中的热门词汇，一旦检测到热门词汇，就打开另一个到云服务的连接，以解析语音并提供响应。在某些情况下，即使是热门词汇检测，至少部分是在云端运行的。换句话说，大多数语音助手都是哑终端，旨在与实际上完成大部分工作的云提供商进行通信，他们通过互联网交换大量信息以进行操作。当您的目标是在快速网络中运行的低功耗设备，并且您不需要太多灵活性时，这可能是明智的。但是，如果您能够负担得起在更强大的 CPU 上处理音频，或者如果您想要在连接受限的设备上操作，或者如果您想要做一些通常无法使用现成解决方案完成的事情，您可能需要尽可能多地处理设备上的负载。当谈到语音助手时，我理解以云为导向的方法的情况，但不管技术如何，我们应该总是在分散和集中计算之间进行选择。我的梦想助手必须能够在设备上或云上运行热门词汇和语音检测逻辑，这取决于用例以及用户的偏好。
*   **扩展性**。如果我在另一个房间或房子里需要一个新的语音助手，我只需拿起一个 RaspberryPi，将我的助手驱动的操作系统映像的副本闪存到 SD 卡，插上麦克风和扬声器，就可以完成了。而无需购买新的塑料盒。如果我需要一个语音驱动的音乐扬声器，我只需将现有的扬声器插入 RaspberryPi。如果我需要一个语音驱动的显示器，我只需要把现有的显示器插到 RaspberryPi 上。如果我需要一个语音开关，我只需在我的 RaspberryPi 上直接编写一个语音命令控制它的规则，而不必担心我的 Google Home 或 Alexa 应用程序是否支持它。任何设备都应该被赋予成为智能设备的可能性。

# 语音助手集成概述

语音助手通常由两部分组成:

*   一个**音频记录器**，从音频输入设备捕获帧
*   一个**语音引擎**跟踪当前上下文。

语音引擎有两个主要类别:热门词汇检测器，扫描音频输入中特定热门词汇的存在(如“Ok Google”或“Alexa”)，以及语音检测器，使用声学和语言模型进行适当的语音到文本的转录。可以想象，连续运行完整的语音检测比只运行热门词检测的开销高得多，后者只需将捕获的语音与通常很短的存储热门词模型列表进行比较。此外还有语音转换引擎，比如 PicoVoice 的 Rhino。这些不是提供文本转录作为输出，而是提供演讲意图的结构化分解*。*例如，如果你说“*我能要一杯加很多糖和一些牛奶的小双份浓缩咖啡吗*”他们可能会返回类似于`{"type":"espresso", “size”:”small", “numberOfShots":2, “sugar":"a lot", “milk":"some"}`的话。

在 platypush 中，我构建了集成，为用户提供了语音到文本处理器和引擎的广泛选择。让我们浏览一些可用的集成，并评估它们的优缺点。

# 原生谷歌助手库

## **集成**

*   `[assistant.google](https://platypush.readthedocs.io/en/latest/platypush/plugins/assistant.google.html)`插件和`[assistant.google](https://platypush.readthedocs.io/en/latest/platypush/backend/assistant.google.html)`后端(启动时持续检测)。

## **配置**

*   创建一个 Google 项目，并从 [Google 开发者控制台](https://console.cloud.google.com/apis/credentials)下载`credentials.json`文件。
*   安装`google-oauthlib-tool`:

```
pip install --upgrade 'google-auth-oauthlib[tool]'
```

*   认证使用`assistant-sdk-prototype`范围:

```
export CREDENTIALS_FILE=~/.config/google-oauthlib-tool/credentials.jsongoogle-oauthlib-tool --scope https://www.googleapis.com/auth/assistant-sdk-prototype \
      --scope https://www.googleapis.com/auth/gcm \
      --save --headless --client-secrets $CREDENTIALS_FILE
```

*   安装带有 HTTP 后端和 Google Assistant 库支持的 platypush:

```
pip install 'platypush[http,google-assistant-legacy]'
```

*   创建或添加行到`~/.config/platypush/config.yaml`以启用网络服务器和助手集成:

```
backend.http:
    enabled: Truebackend.assistant.google:
    enabled: Trueassistant.google:
    enabled: True
```

*   启动`platypush`，说“Ok Google”，享受你的助手。在`[http://localhost:8008](http://localhost:8008)`的网络面板上，你应该能够实时看到你的语音交互。

# **引擎**

*   *热词检测*:是(“Ok Google”或“嘿 Google”)。
*   *语音检测*:是(一旦检测到热门词)。
*   *本地运行检测*:否

# 赞成的意见

*   它实现了你能在任何谷歌助手产品中找到的大部分功能。这包括对定时器、日历的原生支持，基于您的个人资料和位置的定制响应，与 Google Home 中配置的设备的原生集成，等等。对于更复杂的特性，你必须编写自定义的 platypush 钩子，例如检测到语音或对话开始/结束事件。
*   热门词汇检测和语音检测都非常可靠，因为它们依赖于谷歌云的功能。
*   即使在较旧的 RaspberryPi 模型上也有良好的性能(尽管该库不适用于 Zero 模型或其他基于 arm6 的设备)，因为大多数处理任务实际上都发生在云中。在 RaspberryPi 4 上，音频处理线程占用大约 2–3%的 CPU。

# 骗局

*   被集成用作后端的 Google Assistant 库已经被 Google 弃用。只要使用最新版本，它仍然可以在我试过的大多数设备上工作，但请记住，它不再由谷歌维护，它可能会在未来崩溃。不幸的是，我仍在等待官方的替代方案。
*   如果您的主要目标是在安全的环境中运行支持语音的服务，而不在其他人的云上进行处理，那么这不是您的最佳选择。assistant library 使您的计算机的行为或多或少像一个完整的 Google Assistant 设备，包括捕获音频并将其发送到 Google 服务器进行处理，并可能进行审查。

# 谷歌助手一键通集成

## 集成

*   `[assistant.google.pushtotalk](https://platypush.readthedocs.io/en/latest/platypush/plugins/assistant.google.pushtotalk.html)`插件。

## 配置

*   创建一个项目并从 [Google 开发者控制台](https://console.cloud.google.com/apis/credentials)下载`credentials.json`文件。
*   安装`google-oauthlib-tool`:

```
pip install --upgrade 'google-auth-oauthlib[tool]'
```

*   认证使用`assistant-sdk-prototype`范围:

```
export CREDENTIALS_FILE=~/.config/google-oauthlib-tool/credentials.jsongoogle-oauthlib-tool --scope [https://www.googleapis.com/auth/assistant-sdk-prototype](https://www.googleapis.com/auth/assistant-sdk-prototype) \
      --scope [https://www.googleapis.com/auth/gcm](https://www.googleapis.com/auth/gcm) \
      --save --headless --client-secrets $CREDENTIALS_FILE
```

*   安装带有 HTTP 后端和谷歌助手 SDK 支持的 platypush:

```
pip install 'platypush[http,google-assistant]'
```

*   创建或添加行到`~/.config/platypush/config.yaml`以启用 web 服务器和助手集成:

```
backend.http:
    enabled: Trueassistant.google.pushtotalk:
    language: en-US
```

*   开始`platypush`。与原生的谷歌图书馆集成不同，一键通插件没有热词检测引擎。您可以通过编程方式启动或结束对话，例如通过 platypush 事件挂钩、过程或通过 HTTP API:

```
curl -XPOST -H 'Content-Type: application/json' -d '
{
    "type":"request",
    "action":"assistant.google.pushtotalk.start_conversation"
}' http://localhost:8008/execute
```

## 发动机

*   *热门词汇检测*:否(从您的逻辑或从 Snowboy、DeepSpeech 或 PicoVoice 等热门词汇集成的上下文中调用`start_conversation`或`stop_conversation`来触发或停止助手)。
*   *语音检测*:是。
*   *本地运行检测*:否

# 赞成的意见

*   它实现了你可以在任何谷歌助手产品中找到的许多功能，即使没有热词检测功能，并且助手库中目前可用的一些功能也没有提供(如定时器或闹钟)。
*   坚如磐石的语音检测，使用与谷歌助手产品相同的语音模型。
*   即使在较旧的 RaspberryPi 模型上也有相对较好的性能。它还支持 arm6 架构，因此也适用于 RaspberryPi Zero 或其他低功耗器件。没有热词引擎运行意味着它只在你调用`start_conversation`的时候使用资源。
*   它提供了谷歌助理语音引擎的好处，而不需要在你的麦克风和谷歌服务器之间建立 24/7 开放的连接。该连接仅在`start_conversation`时打开。如果隐私是一个问题，或者如果你想建立更灵活的助手，可以通过不同的热门词汇引擎触发(甚至根据你使用的热门词汇建立不同语言触发的助手)，或者根本不是由热门词汇触发的助手，这是一个很好的选择——例如，你可以在按钮按压、运动传感器事件或网络呼叫时调用`start_conversation`。

## 骗局

*   在 Google Assistant 库被弃用后，我构建了这个集成，但没有提供官方的替代方案。我通过重构 Google 在其示例( [pushtotalk.py](https://github.com/googlesamples/assistant-sdk-python/blob/master/google-assistant-sdk/googlesamples/assistant/grpc/pushtotalk.py) )中提供的不太精炼的代码，并用它制作了一个合适的插件。它可以工作，但是请记住，它是基于一些丑陋的代码，等待被谷歌取代。
*   不支持热门词汇。如果你想获得热门词汇支持，你必须将它连接到 Snowboy、PicoVoice 或 DeepSpeech。

# Alexa 集成

## 集成

*   `[assistant.echo](https://platypush.readthedocs.io/en/latest/platypush/plugins/assistant.echo.html)`插件。

## 配置

*   安装带有 HTTP 后端和 Alexa 支持的 platypush:

```
pip install 'platypush[http,alexa]'
```

*   运行`alexa-auth`。它将在`[http://localhost:3000](http://localhost:3000.)` [启动您机器上的本地 web 服务器。](http://localhost:3000.)在您的浏览器中打开它，并使用您的亚马逊帐户进行验证。应该在`~/.avs.json`下生成一个凭证文件。
*   创建或添加行到您的`~/.config/platypush/config.yaml`以启用 web 服务器和助手集成:

```
backend.http:
    enabled: Trueassistant.echo:
    enabled: True
```

*   开始`platypush`。Alexa 集成没有热词检测引擎。您可以通过编程方式启动或结束对话，例如通过 platypush 事件挂钩、过程或通过 HTTP API:

```
curl -XPOST -H 'Content-Type: application/json' -d '
{
    "type":"request",
    "action":"assistant.echo.start_conversation"
}' [http://localhost:8008/execute](http://localhost:8008/execute)
```

# 发动机

*   *热门词汇检测*:否(从您的逻辑或 Snowboy 或 PicoVoice 等热门词汇集成的上下文中调用`start_conversation`或`stop_conversation`来触发或停止助手)。
*   *语音检测*:是(虽然有限:不会提供处理后音频的转录)。
*   *本地运行检测*:否

# 赞成的意见

*   它实现了你会在任何 Alexa 产品中找到的许多功能，即使热门词汇检测不可用。此外，对技能或媒体控制的支持可能是有限的。
*   良好的语音检测能力，虽然在准确性方面不如谷歌助手。
*   即使在低功耗设备上也有良好的性能。没有热词引擎运行意味着它只在你调用`start_conversation`时使用资源。
*   它提供了 Alexa 设备的一些好处，但不需要你的麦克风和亚马逊服务器之间的 24/7 开放连接。该连接仅在`start_conversation`时打开。

# 骗局

*   当涉及到 Alexa voice SDKs 时，情况是极其分散的。亚马逊最终重新发布了 AVS (Alexa 语音服务)，主要是出于商业用途的考虑，但与谷歌助理产品相比，其功能仍然相当有限。最大的限制是 AVS 处理原始音频输入，并传回原始音频响应。这意味着请求或响应的文本转录将不可用。这就限制了你能用它做什么。例如，您将无法通过事件挂钩捕获定制请求。
*   不支持热门词汇。如果你想获得热门词汇支持，你必须将它连接到 Snowboy、PicoVoice 或 DeepSpeech。

# 雪球集成

## 集成

*   `[assistant.snowboy](https://platypush.readthedocs.io/en/latest/platypush/backend/assistant.snowboy.html)`后端。

## 配置

*   安装带有 HTTP 后端和 Snowboy 支持的 platypush:

```
pip install 'platypush[http,snowboy]'
```

*   选择您的热门词汇模型。有些在`SNOWBOY_INSTALL_DIR/resources/models`下有。否则可以从 [Snowboy 网站](https://snowboy.kitt.ai/)训练或者下载模型。
*   在您的`~/.config/platypush/config.yaml`中创建或添加行来启用 web 服务器和助手集成:

```
backend.http:
    enabled: Truebackend.assistant.snowboy:
    audio_gain: 1.2 models:
# Trigger the Google assistant in Italian when I say "computer"
        computer:
            voice_model_file: ~/models/computer.umdl
            assistant_plugin: assistant.google.pushtotalk
            assistant_language: it-IT
            detect_sound: ~/sounds/bell.wav
            sensitivity: 0.4# Trigger the Google assistant in English when I say "OK Google"
        ok_google:
            voice_model_file: ~/models/OK Google.pmdl
            assistant_plugin: assistant.google.pushtotalk
            assistant_language: en-US
            detect_sound: ~/sounds/bell.wav
            sensitivity: 0.4# Trigger Alexa when I say "Alexa"
        alexa:
            voice_model_file: ~/models/Alexa.pmdl
            assistant_plugin: assistant.echo
            assistant_language: en-US
            detect_sound: ~/sounds/bell.wav
            sensitivity: 0.5
```

*   开始`platypush`。说出与你的一个模型相关的热门词，检查日志中的`HotwordDetectedEvent`是否被触发，如果有一个与热门词相关的助手插件，相应的助手会被正确启动。

## 发动机

*   *热词检测*:是。
*   *语音检测*:否。
*   *本地运行检测*:是。

## 赞成的意见

*   我是 Snowboy 项目的早期粉丝和支持者。我真的很喜欢众包机器学习的想法。你可以从他们的网站上免费下载任何热门词汇模型，只要你录下三段你说那个词的音频样本，以帮助改进模型。你也可以创建你自己的热门词汇模型，如果有足够多的人对使用它感兴趣，那么他们会贡献他们的样本，这个模型会随着时间的推移变得更加健壮。我相信，更多的机器学习项目可以真正受益于这种“只要你帮助改进模型，就免费使用它”的范式。
*   Platypush 是 Snowboy 的早期支持者，所以它的集成得到了很好的支持，并有大量的文档。您可以原生配置自定义助手插件，以便在检测到某个热门词汇时执行，从而轻松制作多语言和多热门词汇的语音助手。
*   良好的性能，即使在低功耗设备上。我在单核 RaspberryPi Zero 设备上结合使用 Snowboy 和 Google Assistant 一键通集成有一段时间了，hotword 处理的 CPU 使用率从未超过 20–25%。
*   热门词汇检测在本地运行，在本地下载的模型上运行。这意味着不需要运行网络连接，也不需要与任何云交换数据。

## 骗局

*   尽管众筹声音模型的想法肯定很有趣，并且有很大的潜力扩大规模，但他们网站上最受欢迎的模型已经用最多 2000 个样本进行了训练。而且(可悲的也是意料之中的)大多数语音样本属于年轻的白人男性，这使得这些模型在处理不属于这一类别的任何个人(以及母语不是英语的人)的语音记录时表现很差。

# Mozilla DeepSpeech

## 集成

*   `[stt.deepspeech](https://platypush.readthedocs.io/en/latest/platypush/plugins/stt.deepspeech.html)`插件和`[stt.deepspeech](https://platypush.readthedocs.io/en/latest/platypush/backend/stt.deepspeech.html)`后端(用于持续检测)。

## 配置

*   安装带有 HTTP 后端和 Mozilla DeepSpeech 支持的 platypush。记下安装的 DeepSpeech 版本:

```
pip install 'platypush[http,deepspeech]'
```

*   下载已安装的 DeepSpeech 版本的模型文件。这可能需要一段时间，具体取决于您的连接:

```
export MODELS_DIR=~/models
export DEEPSPEECH_VERSION=0.6.1wget https://github.com/mozilla/DeepSpeech/releases/download/v$DEEPSPEECH_VERSION/deepspeech-$DEEPSPEECH_VERSION-models.tar.gztar zxvf deepspeech-$DEEPSPEECH_VERSION-models.tar.gz
x deepspeech-0.6.1-models/
x deepspeech-0.6.1-models/lm.binary
x deepspeech-0.6.1-models/output_graph.pbmm
x deepspeech-0.6.1-models/output_graph.pb
x deepspeech-0.6.1-models/trie
x deepspeech-0.6.1-models/output_graph.tflitemv deepspeech-$DEEPSPEECH_VERSION-models $MODELS_DIR
```

*   在您的`~/.config/platypush/config.yaml`中创建或添加行以启用 web 服务器和 DeepSpeech 集成:

```
backend.http:
    enabled: Truestt.deepspeech:
    model_file: ~/models/output_graph.pbmm
    lm_file: ~/models/lm.binary
    trie_file: ~/models/trie # Custom list of hotwords
    hotwords:
        - computer
        - alexa conversation_timeout: 5backend.stt.deepspeech:
    enabled: True
```

*   开始`platypush`。语音检测将在启动时开始运行。`SpeechDetectedEvent`说话的时候会触发 s。`HotwordDetectedEvent` s 将在您说出一个已配置的热门词汇时触发，`ConversationDetectedEvent` s 将在您说出一个热门词汇后触发，并提供`speech`作为参数。您也可以禁用连续检测，仅通过调用`stt.deepspeech.start_detection`和`stt.deepspeech.stop_detection`以编程方式启动。您还可以使用它从音频文件中执行离线语音转录:

```
curl -XPOST -H 'Content-Type: application/json' -d '
{
    "type":"request",
    "action":"stt.deepspeech.detect",
    "args": {
        "audio_file": "~/audio.wav"
    }
}' [http://localhost:8008/execute](http://localhost:8008/execute){
    "type":"response",
    "target":"http",
    "response": {
        "errors":[],
        "output": {
            "speech": "This is a test"
        }
    }
}
```

## 发动机

*   *热词检测*:是。
*   *语音检测*:是。
*   *本地运行检测*:是。

## 赞成的意见

*   老实说，我对 DeepSpeech 的功能以及它们从 0.6.0 版本开始取得的进步印象深刻。Mozilla 使得在设备上运行热门词汇和语音检测变得很容易，不需要任何第三方服务或网络连接。完整的代码库是开源的，Tensorflow 语音和语言模型也非常好。令人惊讶的是，他们已经向社区免费发布了全部内容。这也意味着您可以通过用自己的样本训练 Tensorflow 模型来轻松扩展它。

## 骗局

*   DeepSpeech 对 CPU 资源的要求相当高。它在笔记本电脑或 RaspberryPi 4 上运行正常(但在我的测试中，它在 RaspberryPi 4 上花费了 100%的内核进行语音检测)。在功能不太强大的机器上运行可能会耗费太多资源。
*   DeepSpeech 比其他解决方案有更多的延迟。Mozilla 的工程师们已经做了很多工作来使这个模型尽可能的小和高性能，他们声称已经在 RaspberryPi 4 上实现了实时性能。实际上，我所有的测试在语音捕获和检测之间都有 2 到 4 秒的延迟。
*   DeepSpeech 相对擅长检测语音，但不擅长解释语义上下文(这是谷歌仍然轻而易举获胜的地方)。如果你说“这是一个测试”，模型实际上可能会捕捉到“这是一个测试。”“这个”和“这些”在英语中听起来确实几乎一样，但是谷歌助手有一个更好的语义引擎来检测这种模糊情况的正确解释。DeepSpeech 非常适合语音到文本的转录，但是在这种模糊的情况下，它缺乏一些语义上下文。
*   尽管可以使用 platypush 的 DeepSpeech 作为热词检测引擎，但请记住这不是该引擎的预期用途。热词引擎通常运行在更小、更高性能的模型上，只用于检测一个或几个词，而不是全功能的语言模型。DeepSpeech 的最佳用途可能是用于离线文本转录，或者与另一个热门词汇集成并利用 DeepSpeech 进行语音检测。

# 微微之声

[PicoVoice](https://github.com/Picovoice/) 是一家非常有前途的公司，已经发布了几款用于在设备上执行语音检测的产品。其中包括:

*   [*豪猪*](https://github.com/Picovoice/porcupine) ，一款热门词引擎。
*   [*豹*](https://github.com/Picovoice/leopard) ，语音转文本离线转录引擎。
*   [*猎豹*](https://github.com/Picovoice/cheetah) ，实时应用的语音转文本引擎。
*   [*Rhino*](https://github.com/Picovoice/rhino) ，一个语音到意图引擎。

到目前为止，platypush 提供了与豪猪和猎豹的集成。

## 集成

*   *Hotword 引擎* : `[stt.picovoice.hotword](https://platypush.readthedocs.io/en/latest/platypush/plugins/stt.picovoice.hotword.html)`插件和`[stt.picovoice.hotword](https://platypush.readthedocs.io/en/latest/platypush/backend/stt.picovoice.hotword.html)`后端(用于持续检测)。
*   *语音引擎* : `[stt.picovoice.speech](https://platypush.readthedocs.io/en/latest/platypush/plugins/stt.picovoice.speech.html)`插件和`[stt.picovoice.speech](https://platypush.readthedocs.io/en/latest/platypush/backend/stt.picovoice.speech.html)`后端(用于持续检测)。

## 配置

*   安装带有 HTTP 后端和 PicoVoice hotword 集成和/或语音集成的 platypush:

```
pip install 'platypush[http,picovoice-hotword,picovoice-speech]'
```

*   在您的`~/.config/platypush/config.yaml`中创建或添加行以启用 web 服务器和 DeepSpeech 集成:

```
stt.picovoice.hotword:
    hotwords:
        - computer
        - alexa# Enable continuous hotword detection
backend.stt.picovoice.hotword:
    enabled: True# Enable continuous speech detection
# backend.stt.picovoice.speech:
#     enabled: True# Start speech detection when a hotword is detected
event.hook.OnHotwordDetected:
    if:
        type: platypush.message.event.stt.HotwordDetectedEvent
    then:
# Start a timer that stops the detection in 10 seconds
        - action: utils.set_timeout
          args:
              seconds: 10
              name: StopSpeechDetection
              actions:
                  - action: stt.picovoice.speech.stop_detection - action: stt.picovoice.speech.start_detection
```

*   启动`platypush`，享受您的设备语音助手。

## 发动机

*   *热词检测*:是。
*   *语音检测*:是。
*   *本地运行检测*:是。

## 赞成的意见

*   说到设备上的语音引擎，PicoVoice 产品可能是最好的解决方案。他们的 hotword 引擎比 Snowboy 更精确，而且它还能减少 CPU 的使用。他们的语音引擎比 DeepSpeech 延迟小得多，功耗也低得多——即使在旧型号的 RaspberryPi 上，它也仍然运行良好，延迟低。

## 骗局

*   虽然 PicoVoice 提供了 Python SDKs，但是它们的原生库是闭源的。这意味着我不能深入了解他们是如何解决这个问题的。
*   他们的 hotword 引擎(Porcupine)可以在任何设备上免费安装和运行，供个人使用，但如果你想扩展默认提供的关键字集，或者添加更多样本来训练现有模型，那么你必须获得商业许可。相反，他们的语音引擎(Cheetah)只能在 x86_64 架构的 Linux 上免费安装和运行，供个人使用。任何其他架构或操作系统，以及任何扩展模型或使用不同模型的机会，都只能通过商业许可来实现。虽然我理解他们的观点和商业模式，但我非常乐意通过更友好的流程购买许可证，而不是依赖老式的“联系我们获得商业许可证/我们会联系您”的模式。
*   在语义上下文/意图检测方面，猎豹的语音引擎仍然存在 DeepSpeech 的一些问题。“这个/这些”的歧义也发生在这里。然而，这些问题可以通过使用 Rhino 得到部分解决，这是 PicoVoice 的语音到意图引擎，它将提供语音意图的结构化表示，而不是逐个字母的转录。然而，我还没有将 Rhino 整合到 platypush 中。

# **结论**

语音技术的民主化是人们梦寐以求的，它终于(慢慢地)到来了。不过，目前的情况仍然相当分散，一些商业 SDK 可能仍然会在短时间内或根本没有通知的情况下被弃用。但至少一些解决方案正在出现，将语音检测引入所有设备。

我在 platypush 中为所有这些服务建立了集成，因为我相信应该由用户而不是企业来决定人们应该如何使用语音技术并从中受益。此外，在同一产品中拥有如此多的语音集成，尤其是拥有公开所有相同 API 并生成相同事件的语音集成，这使得编写与助手无关的逻辑变得非常容易，并真正将语音识别任务与语音命令可以运行的业务逻辑分离开来。

查看[我以前的文章](https://medium.com/swlh/build-your-own-customizable-voice-assistant-with-platypush-cede33725cba)，学习如何在 platypush 中编写自己的关于语音检测、热门词汇检测和语音开始/停止事件的定制钩子。

总结一下我目前的发现:

*   如果你想拥有完整的谷歌体验，如果你对谷歌服务器处理你的音频以及在未来的某个地方废弃的谷歌助手库不再工作的可能性没有意见，请使用原生的**谷歌助手**集成。
*   如果您只想使用助手，而不想检测热门词汇，或者您希望助手由备选热门词汇触发，请使用 **Google 一键通**集成。
*   如果你已经有一个亚马逊驱动的生态系统，并且你可以接受由于 AVS 中不可用的语音转录功能而在定制挂钩方面缺乏灵活性，请使用 **Alexa** 集成。
*   如果你想使用一个灵活的、开源的、众包引擎来检测热门词汇，并在设备上运行和/或通过不同的热门词汇模型同时使用多个助手，即使模型可能不是那么准确，也可以使用 **Snowboy** 。
*   如果您想要一个由健壮的 Tensorflow 模型支持的完全在设备上的开源引擎，请使用 **Mozilla DeepSpeech** ，即使这需要更多的 CPU 负载和更多的延迟。
*   如果你想要一个在设备上运行的完整的语音解决方案，它既准确又高性能，即使你需要一个商业许可证才能在一些设备上使用它或扩展/改变模型，也可以使用 **PicoVoice** 解决方案。

请告诉我您对这些解决方案的想法以及您对这些集成的体验！