# 使用 Python 将视频转换为 ASCII 电影艺术

> 原文：<https://betterprogramming.pub/convert-videos-to-ascii-movies-art-36073b0af5a6>

## 了解如何从视频中生成 ASCII 动画

![](img/aa165e6cdf045bf377696c87ca1d7f85.png)

照片由[雅各布·欧文斯](https://unsplash.com/@jakobowens1?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

有很多种艺术，从绘画到音乐。 [ASCII 艺术](https://en.wikipedia.org/wiki/ASCII_art)也不例外。它由仅使用可打印的 ASCII 字符[来表示概念和图片组成，这些字符是你可以在键盘上找到的，只是为了清楚起见。ASCII 艺术最简单的形式是表情符号，如`:-)`或`/:-(`。它们还可以扩展到更复杂的图像，比如由](https://en.wikipedia.org/wiki/ASCII)[疯狂铜斑蛇](https://www.codegrepper.com/profile/crazy-copperhead-pf2ju2na4oh3)制作的这棵树:

```
 _ ._  _ , _ ._
         (_ ' ( `  )_  .__)
       ( (  (    )   `)  ) _)
      (__ (_   (_ . _) _) ,__)
          `~~`\ ' . /`~~`
               ;   ;
               /   \
 _____________/_ __ \_____________
```

甚至是像这样的真实生活照片:

![](img/102d888eea412e87f074974628919798.png)

原始图像的 ASCII 版本由 [freestocks](https://unsplash.com/@freestocks?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上生成

如果你感兴趣，我不久前写了一篇关于将图像转换成 ASCII 艺术的文章。我建议你在继续之前检查一下，因为它可能会使我将要谈论的内容更加清晰。

[](https://towardsdatascience.com/convert-pictures-to-ascii-art-ece89582d65b) [## 将图片转换为 ASCII 图片

### 关于图像如何工作以及如何将像素表示为 ASCII 字符的简要指南——包括代码示例。

towardsdatascience.com](https://towardsdatascience.com/convert-pictures-to-ascii-art-ece89582d65b) 

下一步是创建一个完整的 ASCII 视频，这将在本文中讨论。

# 视频文件和帧

您可能已经知道，不考虑音频，视频文件由两个主要部分组成:

*   头，包含元数据，如[文件格式签名](https://en.wikipedia.org/wiki/File_format#:~:text=container)%20file%20formats.-,Magic%20number,-%5Bedit%5D)或帧速率。
*   数据部分，包含有关其帧和像素的信息。

每一帧实际上是一个像素矩阵(二维数组)，每个像素表示为一组[通道](https://en.wikipedia.org/wiki/Channel_(digital_image))，用于指示像素的颜色和不透明度。常见的通道配置是 RGB(红绿蓝)和 RGBA(红绿蓝 Alpha)。

# ASCII 转换算法

对于这个代码示例，为了简单起见，我将使用 Python，这样任何人都可以理解。

首先，你必须加载视频文件，将其转换为 ASCII 艺术。

一旦访问了它的内容，就必须读取它的头，以便获得一些有用的元数据，比如宽度、高度、帧速率(fps)和帧数。这些信息对下一步至关重要。

创建一个新文件来存储转换后的视频。为了清楚起见，我将把这样的文件称为`.ascii`文件，但是您可以给它指定您喜欢的任何扩展名。然后，您必须将之前收集的元数据作为它的头。为了以后能够读取和播放 ASCII 视频文件，这一步至关重要。

现在，一个一个地获取视频帧。对于每一帧，将其每个像素转换为相应的 ASCII 字符。我们将在后面详细讨论这个过程。然后，将新转换的帧存储在`.ascii`文件中。你也可以把每一帧都保存在 RAM 中，但是这需要大量的千兆字节的内存。解决方案是将帧存储在一个文件中，并在播放视频时根据需要加载它们。

现在，视频播放器将不得不加载`.ascii`文件并读取其文件头以获取视频的元数据，这些元数据将用于读取单个帧并以正确的帧速率显示它们。

最后，从文件中读取帧并将它们逐个打印到控制台，同时考虑帧速率。稍后我将讨论这个自定义打印功能。

# 将帧转换为 ASCII

现在，让我们更深入地研究如何将帧表示为 ASCII 艺术。为了进行解释，我将用 C 扩展 Python 来编写更多资源密集型函数，因为 Python 太慢了，无法在合理的时间内执行它们。我省略了用 C 函数扩展 Python 的具体步骤，因为这超出了本文的范围，但我将在最后链接 GitHub repo 和完整代码。

您可能已经知道，帧是一组像素。每个像素由三个值的数组表示:红色、绿色和蓝色的颜色通道。帧中的每个像素都必须被翻译成 ASCII 字符才能打印到控制台。

应该使用哪个 ASCII 字符取决于像素的“强度”。强度被定义为像素激活的百分比:因此 RGB 值越高，强度越高。例如，RGB 值为`255, 255, 255`的像素的强度为 100%，而`0, 0, 0`为 0%。

然后，您必须根据像素强度选择一个 ASCII 字符。字符从占据较小空间的字符到最大的字符排序。比如，“”肯定比“@”更纤细，因此也没有那么强烈。

考虑到这一点，您必须迭代帧中的每一行，并计算每个像素的亮度和相应的 ASCII 字符。因为大多数终端不允许你减少行与行之间的间距，框架会被拉长，所以有必要在每个字符后加一个空格来固定纵横比。

# 播放视频和帧速率

每个视频都有一个固定的帧速率，使帧的流动感觉自然。当将视频转换为 ASCII 艺术时，您必须将其保存在`.ascii`文件头中。当显示视频时，它将被用来改变每一帧的持续时间。

由于在将帧转换为 ASCII 时我们已经包含了换行符(`\n`)，所以我们不必介意长宽比或帧大小:您可以清除屏幕并将整个 ASCII 字符串帧打印到控制台。

棘手的部分是计算在移动到下一帧之前，该帧应该在屏幕上持续多久。出于这个原因，你必须测量绘制图像所用的时间，并从默认的帧持续时间中减去它，默认的帧持续时间是通过将 1 秒除以 FPS，然后将结果乘以`1e9`以纳秒为单位来表示的。

最后，在帧持续时间的剩余时间内休眠，如果有的话。如果打印帧的时间比默认的帧持续时间长，视频会以较慢的速度播放。

下面是一个用这个工具生成的 ASCII 电影艺术的例子。[在这里](https://www.youtube.com/watch?v=My2puqWOVqw)你可以找到原始视频。

请注意，要使影片适合您的终端，您可能需要减小字体。另外，我建议你使用方形的 ASCII 字体。我推荐的字体包含在 GitHub 库中。

# 结论

总结一下，这是一个将视频文件转换为 ASCII 艺术电影的算法解释，并附有代码示例。如果您对完整的、经过彻底注释的代码感兴趣，请查看 GitHub 上的项目。

我希望你喜欢这篇文章。感谢阅读！