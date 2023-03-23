# 如何在 Python 中播放音频文件

> 原文：<https://betterprogramming.pub/how-to-play-audio-files-in-python-3a430b6b2657>

## 利用 SimpleAudio 包，这是一个用于 Python 的跨平台音频库

![](img/f0b6ac80667857cb78f8ed6e2f6cb4ad.png)

由[乔希·索伦森](https://unsplash.com/@joshsorenson?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/speaker?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

通过阅读本文，您将学会在 Python 应用程序中播放音频文件。根据[官方文档](https://simpleaudio.readthedocs.io/en/latest/)，[简单音频包](https://simpleaudio.readthedocs.io/en/latest/capabilities.html):

> “…为 Python 3 在 OSX、Windows 和 Linux 上提供跨平台、无依赖性的音频回放功能。
> 
> Simpleaudio 旨在一站式购买用于声音剪辑播放的跨平台音频接口。"

它具有以下特点:

*   异步接口——音频回放开始后，程序立即继续执行，剩下的工作由后台线程完成。
*   音频格式—标准整数(8 位、16 位和 24 位整数格式)以及 32 位浮点。8 位无符号。16 位和 24 位是带符号的小端字节序。24 位是每个打包样本的三个字节。32 位是小端浮点。

本教程分为三个部分:

1.  设置
2.  履行
3.  结论

让我们继续下一部分，开始安装必要的模块。

# 1.设置

使用`pip install`命令安装非常简单。强烈建议您在继续之前设置一个虚拟环境。

## Windows 操作系统

运行以下命令安装`simpleaudio`模块:

```
pip install simpleaudio
```

## Linux 操作系统

对于 Linux 用户，除了上面的命令之外，您还必须安装额外的依赖项。对于 Debian 变体，您可以使用以下命令来完成。我们将首先安装 python3 开发包:

```
sudo apt-get install python3-dev
```

然后，我们得到 ALSA 发展一揽子计划:

```
sudo apt-get install libasound2-dev
```

完成安装后，继续下一节，开始编写一些 Python 代码。

# 2.履行

## 导入

首先，在 Python 文件的顶部添加以下导入声明:

```
import simpleaudio as sa
```

## 波浪物体

下一步是创建一个`WaveObject`。基于[文档](https://simpleaudio.readthedocs.io/en/latest/simpleaudio.html)，一个`WaveObject`代表准备好回放的音频片段。它封装了音频数据缓冲区、回放参数(如采样率)，并提供了启动回放的方法。它接受以下输入参数。

*   `audio_data` —带有音频数据的对象(必须支持缓冲接口)
*   `num_channels` ( *int* ) —音频通道的数量
*   `bytes_per_sample` ( *int* ) —每个单通道样本的字节数
*   `sample_rate` ( *int* ) —采样率，单位为赫兹

幸运的是，这个模块还为我们提供了`from_wave_file`函数，允许我们输入文件路径，它会将相应的数据加载到`WaveObject`中。在 Python 文件中添加以下代码。根据您拥有的内容修改输入路径:

```
wave_obj = sa.WaveObject.from_wave_file('alarm.wav')
```

强烈建议使用一个`wav`文件作为输入。如果您碰巧有一个`mp3`而不是`wav`，那么只需将其转换为一个`wav`文件即可。

## 玩

下一步是调用`play`函数:

```
play_obj = wave_obj.play()
```

这个函数将返回一个`PlayObject`，它主要处理音频播放任务，比如停止和检查是否还在播放。最后，添加下面的函数，您可以运行这个 Python 文件来测试音频回放。对 play 函数的多次调用将在后台异步并行运行所有回放。

```
play_obj.wait_done()
```

## 正在播放

要检查`PlayObject`是否还在播放，可以调用`is_playing`函数:

```
play_obj.is_playing()
```

## 停止

虽然在编写时没有提供暂停或恢复功能，但您仍然可以使用`stop`功能来结束音频播放:

```
play_obj.stop()
```

还有一个全局`stop_all`功能可以停止所有现有的回放:

```
simpleaudio.stop_all()
```

查看以下要点，了解异步工作方式的概况:

# 3.结论

让我们回顾一下今天所学的内容。

我们通过 pip 安装开始安装`simpleaudio`模块。为 Linux 用户安装了额外的包，如 Python 和 ALSA 开发。

接下来，我们深入探讨了所提供的可用功能。我们创建了一个`WaveObject`来读取和加载一个`wav`文件。然后，我们播放它，并获得了一个可以用来控制音频播放的`PlayObject`。

感谢你阅读这篇文章。希望在下一篇文章中再见到你！

# 参考

1.  [SimpleAudio GitHub](https://github.com/hamiltron/py-simple-audio)
2.  [简单音频文档](https://simpleaudio.readthedocs.io/en/latest/index.html)