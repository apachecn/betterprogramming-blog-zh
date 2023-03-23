# Python 中的平行句对齐

> 原文：<https://betterprogramming.pub/parallel-sentence-alignment-in-python-3ca022d7d3d7>

## 利用基于机器翻译的句子对齐工具

![](img/70e5b64161215a8ed3df7f5459e5f580.png)

由 [michal dziekonski](https://unsplash.com/@poloska25?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/align?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

通过阅读这篇文章，你将学会对齐两个单语文件中的平行句子(有序的句子，但没有正确对齐)。假设你有下面的英语句子(摘自弗朗兹·卡夫卡的《变形记》的英译本)

```
One morning, as Gregor Samsa was waking up from anxious dreams, he discovered that in bed he had been changed into a monstrous verminous bug.He lay on his armour-hard back and saw, as he lifted his head up a little, his brown, arched abdomen divided up into rigid bow-like sections.From this height the blanket, just about ready to slide off completely, could hardly stay in place.His numerous legs, pitifully thin in comparison to the rest of his circumference, flickered helplessly before his eyes."What's happened to me," he thought.It was no dream.
```

和相应的意大利语句子如下:

```
Gregorio Samsa, svegliandosi un mattino da sogni agitati, si trovòtrasformato, nel suo letto, in un enorme insetto immondo.Giacevasulla schiena, dura come una corazza e, sollevando un po' latesta, vide un addome arcuato, scuro, attraversato da numerosenervature.La coperta, in equilibrio sulla sua punta, minacciavadi cadere da un momento all'altro; mentre le numerose zampe,pietosamente sottili rispetto alla sua mole, gli ondeggiavanoconfusamente davanti agli occhi."Che mi è successo?" pensò.Non era un sogno.La sua camera, unavera camera per esseri umani, anche se un po' piccola, stava benferma e tranquilla tra le sue quattro note pareti.
```

我注意到英语和意大利语的句子都没有逐行对齐，因为翻译的版本使用了分号(；)把两个句子合二为一。

因此，如果您打算训练机器翻译模型，这个数据集是不可用的，因为翻译的句子偏移了一行。在正常情况下，您总是可以手动修复它，但对于以数百万句计算的大型语料库来说，这可能是压倒性的。

让我们来探索如何通过一个名为 [Bleualign](https://github.com/rsennrich/bleualign) 的 Python 包创建包含对齐句子的原始数据集的子集。

# Bleualign

这个 Python 包有助于在句子级别上对齐和配对源文本及其翻译的目标文本。然而，它要求源文本事先被自动翻译，以便与翻译文本进行比较。此外，句子不能被打乱，要按顺序排列。

## 设置

强烈建议您在继续之前设置一个虚拟环境。激活它并运行以下命令通过`pip install`进行安装:

```
pip install git+[https://github.com/rsennrich/Bleualign.git](https://github.com/rsennrich/Bleualign.git)
```

下一步是将以下文件复制到您的目录中:

*   [command_utils.py](https://github.com/rsennrich/Bleualign/blob/master/command_utils.py) :从命令行解析输入参数的实用模块
*   [bluealign.py](https://github.com/rsennrich/Bleualign/blob/master/bleualign.py) :执行句子对齐的主脚本

## 概念

在此之前，让我们探索一下执行一次句子对齐的底层过程。它的工作原理如下:

1.  使用任何机器翻译 API(谷歌翻译等)翻译源文本。).翻译文本必须逐行与源文本相对应。两个文件中不应有任何换行符或空行。
2.  运行脚本来计算目标文本和自动翻译的源文本之间的相似性(修改的 BLEU)。
3.  系统将根据相似性得分排列句子

如果同时翻译源文本和目标文本，Bleualign 也可以工作。然后，它将简单地获得两个结果的交集作为最终输出。这提供了高质量的对齐。

## 资料组

出于实验目的，我从一本小说中提取了文本，并通过正则表达式将其拆分成句子。最终数据集由以下数字行组成:

*   英语源句子:6149 行
*   意大利语目标句子:5579 行

之后，我通过 Google Translate 翻译内容(行数必须与源句或目标句相同):

*   翻译的源句子:6，149 行
*   翻译的目标句子:5579 行

## 句子对齐

假设数据集(源、目标、翻译源)位于与`bluealign.py`脚本相同的目录中，运行以下脚本开始句子对齐过程:

```
python bleualign.py -s sourcetext.txt -t targettext.txt --srctotarget sourcetranslation.txt --targettosrc targettranslation.txt -o outputfile
```

它接受以下参数:

*   `-s`:源文件路径
*   `-t`:目标文件路径
*   `--srctotarget`:翻译后的源文件路径
*   `--targettosrc`:翻译后的目标文件路径(可选)
*   `-o`:输出文件前缀

为了更准确，您必须翻译目标文本并执行以下命令:

```
python bleualign.py -s sourcetext.txt -t targettext.txt --srctotarget sourcetranslation.txt --targettosrc targettranslation.txt -o outputfile
```

您应该在终端上看到以下输出:

```
reading in article 0:
processing
computing alignment between srctotarget (file 0) and target text
Evaluating sentences with bleu
finished
searching for longest path of good alignments
finished
filling gaps
finished
computing alignment between targettosrc (file 0) and source text
Evaluating sentences with bleu
finished
searching for longest path of good alignments
finished
filling gaps
finished
intersecting both directionsfinished with article
```

# 结果

除此之外，它还会根据您设置的前缀生成另外两个文件:

*   `output-s`:句子对齐后输出源文本
*   `output-t`:句子对齐后输出目标文本

结果并不完美，但作为初始并行数据集已经足够好了。在这种情况下，该脚本仅用几秒钟就对齐了 4442 行平行句子(忽略翻译源句子和目标句子所需的时间)。想象一下，如果您自己手动调整它，需要多长时间。您需要做的只是验证并对输出进行一些最后的润色，以确保机器翻译模型的高质量并行数据集。

当短句没有被正确分解时，这种算法并不能很好地工作。例如，会出现以下问题:

```
# English
Yes.
Go ahead. I'll wait for you.# Italian
Sí. Allora vai.
Ti aspetto.
```

# 成批处理方式

还有[一个脚本](https://github.com/rsennrich/Bleualign/blob/master/batch_align.py)批量处理文件。假设您具备以下条件:

*   `raw_files`:名为的文件夹，包含所有数据集
*   `0.en`:源文件
*   `0.it`:目标文件
*   `0.trans`:翻译后的源文件
*   `1.en`:源文件
*   `1.it`:目标文件
*   `1.trans`:翻译后的源文件

只需运行以下命令:

```
# syntax
python batch_align.py directory source_suffix target_suffix translation_suffix# example
python batch_align.py raw_files en it trans
```

它将生成以下文件作为输出:

*   0.en .对齐
*   0 .对齐
*   1 .对齐
*   1 .信息技术

# 笔记

一个主要的缺点是，在执行句子对齐之前，您需要获得源句子的翻译版本。除了使用外部 API(如 Google Translate)进行自动翻译之外，您还可以使用开源数据集来训练简单的机器翻译，以协助初始翻译。

有关该算法的更多信息，请阅读以下文章:

> Rico Sennrich，Martin Volk(2010):OCR 生成的平行文本的基于机器翻译的句子对齐。在:2010 年 AMTA 会议录，丹佛，科罗拉多州。
> 
> 里科·森里奇；Martin Volk (2011):迭代的、基于机器翻译的平行文本句子对齐。载于:NODALIDA 2011，北欧计算语言学会议，里加。

# 结论

让我们回顾一下你今天所学的内容。

本文从一个简单的问题陈述开始，强调了为机器翻译模型构建平行语料库时所面临的问题。

它转移到安装和数据准备步骤，要求源句子事先翻译。

然后，探讨了 Bleualign 背后的基本概念，它计算目标句子和翻译的源句子之间的相似度(修正的 BLEU)。

最后，介绍了示例命令和句子对齐的结果。此外，Bleualign 支持通过它的一个脚本批量处理文件。

感谢你阅读这篇文章。祝你有美好的一天！

# 参考

1.  [GitHub — Bleualign](https://github.com/rsennrich/bleualign)
2.  [弗兰茨·卡夫卡。(1915)变形记](https://www.kafka-online.info/the-metamorphosis.html)