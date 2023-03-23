# 使用 Pydub 在 Python 中进行简单的音频处理

> 原文：<https://betterprogramming.pub/simple-audio-processing-in-python-with-pydub-c3a217dabf11>

## 在 Python 中过滤、混合、加载和保存音频

![](img/e309fe834463c73e8b93163bc9dfc381.png)

照片由 [Adi Goldstein](https://unsplash.com/@adigold1?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

有时，在脚本或应用程序中，我们需要执行音频处理任务。

这些可能包括:

*   加载和保存不同类型的音频文件。
*   分段分割或附加音频。
*   混合来自两个不同音频文件的音频。
*   更改音量或声相设置。
*   应用简单的效果，如滤镜。
*   产生音频音调。

所有这些都可以使用 [Pydub](https://github.com/jiaaro/pydub) 来实现，这是一个简单、设计良好的 Python 模块，用于音频操作。Pydub 是我学习基本音频脚本的首选工具。用 PyDub 作者的话说:

> " Pydub 让你以一种不愚蠢的方式对音频做一些事情."

# 装置

Pydub 可以和 [pip](https://pypi.org/project/pip/) 一起安装，这是 Python 所有最新版本都附带的。只需键入:

```
pip install pydub
```

我们准备使用 Pydub。

# 加载和播放音频

Pydub 中的主类是`AudioSegment`。一个`AudioSegment`作为一个容器来加载、操作和保存音频。

为了创建我们的第一个音频脚本，我们需要一个测试音频文件，这可以是任何支持的格式，如 WAV，MP3 或 AIFF。出于本教程的目的，我们将使用`urllib.request`下载一个文件作为脚本的一部分。

```
import urllib.requestfrom pydub import AudioSegment
from pydub.playback import play# Download an audio file
urllib.request.urlretrieve("[https://tinyurl.com/wx9amev](https://tinyurl.com/wx9amev)", "metallic-drums.wav")# Load into PyDub
loop = AudioSegment.from_wav("metallic-drums.wav")# Play the result
play(loop)
```

结果听起来是这样的:

# 基本音频操作

现在我们已经加载了一些音频，我们可以进行各种形式的操作:

```
# Repeat 2 times
loop2 = loop * 2# Get length in milliseconds
length = len(loop2)# Set fade time
fade_time = int(length * 0.5)# Fade in and out
faded = loop2.fade_in(fade_time).fade_out(fade_time)
```

注意，在最后一行代码中，我们是如何链接`fade_in()`和`fade_out()`操作的。这是因为每个操作都返回一个`AudioSegment`实例。

结果听起来是这样的:

# 分层音频

除了操纵单个音频段，我们还可以用不同的音量和声相设置对不同的段进行分层和混合。

```
# Download another loop
urllib.request.urlretrieve("https://tinyurl.com/yx3k5kw5", "beat.wav")# Load into PyDub
beat = AudioSegment.from_wav("beat.wav")# Mix with our original loop
mixed = beat[:length].overlay(loop2)
```

注意，在最后一行，我们使用 Python“slice”操作符将`beat`分割为`length`毫秒。

这是因为我们的`beat` `AudioSegment`是`loop2`的两倍长。通过`length`切片意味着我们将`beat`的前半部分与`loop2`混合。

结果听起来是这样的:

另一种方法是向`overlay()`提供`loop=True`参数。这将根据需要自动循环较短的线段，以与较长的线段对齐。

# 应用效果

在下一个示例中，我们将通过对音频应用声相、过滤和反转效果来将所有内容组合在一起。

```
# Filter the beat at 3kHz
filtered = beat.low_pass_filter(3000)# Mix loop2 with a reversed, panned version
loop = loop2.reverse().pan(-0.5).overlay(loop2.pan(0.5))# Mix our filtered beat with the new loop at -3dB
final = filtered.overlay(loop2 - 3, loop=True)
```

在我们对`pan()`方法的论证中，我们提供了一个在`-1`和`1`之间的值，其中`-1`在左边，`1`在右边，`0`在中间。

结果听起来是这样的:

最后一步，我们可以将新的音频循环保存到一个新文件中。这可以是任何支持的音频格式:

```
final.export("final.mp3", format="mp3")
```

# 合成音调

除了加载和处理音频文件，Pydub 还可以合成新的音调。

这些可以是任何频率的正弦波、方波或锯齿波。它还会产生白噪声。音调可以变成`AudioSegment`并像常规音频文件一样组合。

在下面的例子中，我们将使用`Sine`类为和声序列中的前 15 个音程生成正弦音调。

```
# Create an empty AudioSegment
result = AudioSegment.silent(duration=0)# Loop over 0-14
for n in range(15): # Generate a sine tone with frequency 200 * n
    gen = Sine(200 * n) # AudioSegment with duration 200ms, gain -3
    sine  = gen.to_audio_segment(duration=200).apply_gain(-3) # Fade in / out
    sine = sine.fade_in(50).fade_out(100) # Append the sine to our result
    result += sine# Play the result
play(result)
```

注意我们如何使用`+=`操作符将每个 200 毫秒的正弦音调附加到空段的末尾。

结果听起来是这样的:

# 缺点

Pydub 非常适合简单的音频任务。然而，如果你想做更复杂的处理，如加快或减慢声音，改变音高，或应用时变效果，这不是最好的选择。

另外，Pydub 的优势之一是它的纯 Python 实现。这最大限度地减少了本机依赖性。

然而，这样做的一个缺点是 Pydub 不是很高效。为了尽可能快地处理大量文件，最好使用 [SoX](http://sox.sourceforge.net) 或 [PySoX](https://github.com/rabitt/pysox) 。

# 完整代码示例

本教程的完整代码可以在这个要点中找到。

完整代码示例

感谢阅读。如果你对好的 Python 音频框架或者 Pydub 的好应用有什么建议，我很乐意在下面的评论中听到。