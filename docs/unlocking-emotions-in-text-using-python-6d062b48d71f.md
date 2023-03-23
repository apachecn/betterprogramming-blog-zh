# 使用 Python 释放文本中的情感

> 原文：<https://betterprogramming.pub/unlocking-emotions-in-text-using-python-6d062b48d71f>

## LeXmo:第一个对英语文本中的情感进行分类的 Python 包

![](img/8df02e33753d41ac57bde6d07bd4ca79.png)

普卢奇克的情感之轮- *作者/版权所有者:机器精灵 1735。版权条款和许可:公共领域。*

我的论文是关于网上劝说的分类。作为特征选择的一部分，我寻找一种对文本中的情感进行分类的方法。我的研究把我带到了 [NRC 目录](https://www.saifmohammad.com/WebPages/AccessResource.htm)，这是一个手动创建的词典:

> **NRC 单词-情感联想词典又名 NRC 情感词典又名 EmoLex:** 单词与八种情感(愤怒、恐惧、期待、信任、惊讶、悲伤、快乐和厌恶)和两种情感(消极和积极)的联想，在亚马逊的机械土耳其人上手工注释。有 40 种不同的语言版本。
> 版本:0.92
> 词条数:14182 个单字(单词)~ 25000 个词义
> 联想得分:二元(关联与否)
> **创作者:**赛义夫·穆罕默德(Saif M. Mohammad)和彼得·特尼(Peter D. Turney)

令人惊讶的是，我找不到一个对情绪进行分类的 Python 包，我找到的唯一实现是这里的。

我决定基于 EmoLex 创建并发布一个用于情感分类的 python 包——引入 [LeXmo，](https://pypi.org/project/LeXmo/)一个基于 EmoLex 的包。我按照这个[伟大的指南](https://towardsdatascience.com/publishing-your-own-python-package-3762f0d268ec)创建了我的第一个 python 包。

# 关于包裹

LeXmo 包将目录转换为 pandas 数据框，它接收文本，对其进行标记，对每种情感的联想词的数量进行求和，并返回文本和情感权重的字典——情感关联除以文本字数的计算结果。

# 如何使用该软件包

## 1.导入并安装

使用 LeXmo 时，首先需要安装支持模块，如下所示:

```
import nltknltk.download('punkt')
```

和

```
import pandas as pdfrom nltk import word_tokenizefrom nltk.stem.snowball import SnowballStemmerimport requests
```

然后您需要安装这个包，然后从 LeXmo 包中导入 Lexmo 模块，如下所示:

```
!pip install -U LeXmo
from LeXmo import LeXmo
```

比方说，你想对这句经典名言的情感进行分类，这句话引自**查尔斯·狄更斯、** [**【艰难时期】**](https://www.goodreads.com/work/quotes/6751955) :

> “从一开始，她就坐在那里，目不转睛地看着他。当他现在靠在椅子上，把他深陷的眼睛转向她时，也许他已经看到了她犹豫的时刻，当她被迫扑到他的胸前，把她内心压抑的秘密告诉他。但是，要看到它，他必须跳过他多年来在自己和所有那些微妙的人类本质之间建立的人工障碍，这些障碍将躲避代数的最大狡猾，直到最后一个喇叭响起来，甚至将摧毁代数。对于这样一个飞跃来说，障碍太多、太高。他用他那张不屈不挠的、功利的、实事求是的脸，再次使她变得冷酷无情；这一瞬间被抛入过去的无尽深渊，与淹没在那里的所有失去的机会混为一谈。”

## 2.定义包输入

在我们的例子中，引用。

## 3.调用包

在这个例子中，您可以看到我通过使用语法`LeXmo.LeXmo(t)`使用了一个操作符来保存名为`emo`的包输出。

它看起来会像这样:

这个包试图从我参加的一个黑客马拉松的论文中提取一个附带项目。这是给你用来释放文本中的情感的。

![](img/c362c9aecb539145b6c2fc35860556b5.png)

由[格伦·卡斯滕斯-彼得斯](https://unsplash.com/@glenncarstenspeters?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

正如你所看到的，使用这个包有三个阶段:

1.  安装和导入
2.  设置输入
3.  只是使用包装

以下是本教程的完整笔记本:

关于 LeXmo 包的更多信息可以在 GitHub 的 [LeXmo 库中找到。](https://github.com/dinbav/LeXmo)

我希望你会像我打算的那样喜欢这个。