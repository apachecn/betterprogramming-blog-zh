# 在 Python 中将文本转换成音素

> 原文：<https://betterprogramming.pub/convert-text-to-phoneme-in-python-989b6e05b70f>

## 许多语言中单词和文本的简单拼音化

![](img/ac4a8740a3e01218c90b653d4d6cbb91.png)

照片由 [Denisse Leon](https://unsplash.com/es/@denisseleon?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/sound?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

通过阅读这篇文章，您将学习如何将输入的文本字符串转换成 Python 中相应的音素。音位代表语言中最小的声音单位。例如，这个词

```
tab
```

由三个音素组成:

```
/t/ /a/ /b/
```

元素`b`与以下单词相比是可区分的:

*   标签
*   黝黑色
*   轻敲，水龙头

```
/t/ /a/ | /b/
--------|----
/t/ /a/ | /g/
/t/ /a/ | /n/
/t/ /a/ | /p/
```

音素非常有用，因为它们是语音的视觉表现。一些语音相关的机器学习任务是基于音素转录而不是文本转录的。

有许多不同的音标，但最常用的系统是国际音标(IPA)。国际音标是基于拉丁文字的字母系统。它使用由字母和音调符号组成的符号。例如，术语 IPA 可以表示为

```
aɪ pʰiː eɪ
```

为了保持简洁，本教程使用了一个名为`[phonemizer](https://github.com/bootphon/phonemizer)`的开源 Python 包。根据[官方文件](https://github.com/bootphon/phonemizer) , `phonemizer`是一个

> "简单的文本到电话的多种语言转换器."

继续下一节，安装所有必要的模块。

# 设置

从版本 3.2.0 开始，`[phonemizer](https://github.com/bootphon/phonemizer)`包支持以下后端:

*   `[espeak-ng](https://github.com/espeak-ng/espeak-ng)` —基于国际音标输出的文本到语音(TTS)系统。它支持 100 多种语言。
*   `[espeak-ng-mbrola](https://github.com/espeak-ng/espeak-ng/blob/master/docs/mbrola.md)`—espeak 的替代版本，使用 SAMPA 音标代替 IPA。此外，它不会为输出保留单词边界。
*   `[festival](http://www.cstr.ed.ac.uk/projects/festival)` —美国英语的另类 TTS 系统。它支持音节级别的标记化。
*   `[segments](https://github.com/cldf/segments)` —Unicode tokenizer，根据用户定义的映射文件将字素转换为音素。

让我们用`espeak-ng`后端测试一下。

## Debian/Ubuntu

您可以按如下方式轻松安装它:

```
sudo apt-get install espeak-ng
```

## CentOS/Fedora

在 CentOS 或 Fedora 操作系统上，安装是通过`yum`命令完成的:

```
sudo yum install espeak-ng
```

## 马科斯

对于 macOS 用户，可以通过`brew`安装:

```
brew install espeak
```

## Windows 操作系统

在 Windows 操作系统上，您可以通过安装程序安装它。只需前往[发布页面](https://github.com/espeak-ng/espeak-ng/releases)并下载所需的`msi`安装程序。然后，运行它在您的机器上安装。

*请注意，mbrola 不适用于 Windows。*

## Python 包

强烈建议您在继续安装之前创建一个新的虚拟环境。

完成后，激活虚拟环境并运行以下命令来安装`phonemizer`:

```
pip install phonemizer
```

或者，您可以直接从源代码安装，如下所示:

```
git clone https://github.com/bootphon/phonemizer
cd phonemizer
python setup.py install
```

## Docker(可选)

phonemizer 包也自带 docker 设置。您可以通过以下命令构建 Docker 映像:

```
git clone https://github.com/bootphon/phonemizer
cd phonemizer
sudo docker build -t phonemizer .
```

然后，按如下方式交互运行它:

```
sudo docker run -it phonemizer /bin/bash
```

运行以下命令，确认您已经成功安装了`phonemizer`:

```
phonemize --version
```

您应该在终端上看到以下输出:

```
phonemizer-3.2.0
available backends: espeak-ng-1.49.2, segments-2.2.0
uninstalled backends: espeak-mbrola, festival
```

# 履行

创建一个名为`main.py`的新 Python 文件，并在其中添加以下代码:

您所需要做的就是导入`phonemize`函数，并传入一个字符串或字符串列表。如果你传入一个字符串，它将返回一个字符串。上面的示例使用了一个字符串列表，预期输出如下:

```
['həloʊ wɜːld ', 'wɛlkʌm tə miːdiəm ']
```

## 争论

此外，`phonemize`函数还接受以下参数:

*   `language`(str)—基于支持的后端的语言标识符。对于`espeak`后端，你可以在下面的链接找到所有[支持的语言。至于后端，语言是字素到音素映射的文件路径。](https://github.com/espeak-ng/espeak-ng/blob/master/docs/languages.md)
*   `backend` (str) —拼音化所需的后端。应该是以下选项之一:`espeak`、`espeak-mbrola`、`festival`或`segments`。
*   `separator` ( [分隔符对象](https://bootphon.github.io/phonemizer/api_reference.html#phonemizer.separator.Separator) ) —音素、音节和单词之间的字符串分隔符。不同的后端支持不同类型的分隔符。
*   `strip` (bool) —默认为`False`，它将输出令牌的最后一个单词和音素分隔符。
*   `preserve_empty_lines` (bool) —是否在最终输出中保留空行。默认为`False`，将删除所有空行。
*   `preserve_punctuation` (bool) —是否在最终输出中保留标点符号。默认为`False`，将删除所有标点符号。
*   `punctuation_marks`(字符串或环。Pattern) —一个字符串或正则表达式，表示删除或保留时要考虑的标点符号。
*   `with_stress` (bool) —是否保留音素上的重音。默认为`False`，仅适用于`espeak`后端。
*   `njobs` (int) —执行期间并行作业的数量。

*你可以在下面的* [*API 文档*](https://bootphon.github.io/phonemizer/api_reference.html) *找到完整的论据。*

看看下面基于不同输入参数的输出示例:

```
['həloʊ wɜːld ', 'wɛlkʌm tə miːdiəm ']# strip=True
['həloʊ wɜːld', 'wɛlkʌm tə miːdiəm']# preserve_punctuation=True
['həloʊ, wɜːld! ', 'wɛlkʌm tə miːdiəm! ']# with_stress=True
['həlˈoʊ wˈɜːld ', 'wˈɛlkʌm tə mˈiːdiəm ']
```

## 最佳化

请注意，音素化后端的初始化是一个昂贵的过程。您应该在调用`phonemize`函数之前构建一个大的列表。不要调用列表理解中的函数。错误如下所示:

```
# Bad code
phonemized = [phonemize(x, language='en-us', backend='espeak') for x in text]# Good code
phonemized = phonemize(text, language='en-us', backend='espeak')
```

或者，您可以初始化所需后端的新实例，并调用底层 phonemize 函数:

通过这样做，您可以重用同一个实例，并简单地对后续输入调用`backend.phonemize`函数。

# 结论

让我们回顾一下今天所学的内容。

本文从音素和国际音标(IPA)的简要解释开始。

然后，介绍了如何在我们的机器上安装`espeak`和`phonemizer`。

随后，它转向了`phonemizer` 包背后的基本概念。它还强调了一些最常用的论点。在最后一节中，它提供了一些使用`phonemizer.`时优化执行的技巧和诀窍

感谢阅读这篇文章。祝你有美好的一天！

# 参考

1.  [GitHub——phonemizer](https://github.com/bootphon/phonemizer)
2.  [文档— phonemizer](https://bootphon.github.io/phonemizer/index.html)