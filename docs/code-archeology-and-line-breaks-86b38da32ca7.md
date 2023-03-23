# 代码、考古学和换行符

> 原文：<https://betterprogramming.pub/code-archeology-and-line-breaks-86b38da32ca7>

## 你知道\r 和\n 是打字机的残留物吗？

![](img/f44fa324c0420d2493c3145a8f627a6e.png)

照片由[伯纳德·赫尔曼](https://unsplash.com/@bernardhermant?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

你有没有想过一个新的行是如何存储在计算机上的？你知道，当你在文本编辑器中点击“回车”会发生什么吗？

在 Windows 上，新的一行被存储为奇怪的序列`\r\n`。例如，以下文本:

```
Hello
World!
```

在 Windows 系统中实际上存储为`Hello\r\nWorld!`。

原来`\r`叫做*回车*而`\n`叫做*换行*。它们是起源于打字机的特殊字符。过去，当人们在打字机上读到一行的末尾时，他们会推动一个杠杆，这个杠杆有两个作用:

1.  它返回承载纸张的托架，因此打字位置被移动到一行的开始。
2.  它换了一行，所以打字位置向下移动了一行。

回车和换行的组合有效地将当前的输入位置移到了新行的开头。

今天，现代计算机和文字处理器的出现已经不再需要打字机了。剩下的就是我们机器里那些晦涩难懂的特殊字符，`\r`和`\n`。当我第一次知道这件事的时候，我惊呆了——在日常代码中隐藏着多么丰富的人类历史啊！

*边注:Mac 和 Linux 系统简单地使用换行字符* `*\n*` *代替更冗长的序列* `*\r\n*` *来表示新的一行。虽然它的存储效率更高，但从技术上讲，它并不“历史准确”*

还有更多例子。术语“编程错误”被公开是因为一个实际的错误[曾经导致一台机器出现故障](https://en.wikipedia.org/wiki/Software_bug#/media/File:First_Computer_Bug,_1945.jpg)。编程语言 Fortran 忽略空白的原因是人们习惯在穿孔卡上编码，很容易错误地插入空白。想想有多少人类行为的轶事和模式隐藏在像计算机软件这样机械而没有生命的东西中，这是很有趣的。

![](img/ecbb13420f047acaf8e57145aca21c3f.png)

来源:[贝弗利山语言学院](https://www.bhlingual.com/who-misses-skeuomorphism-blog)

另一方面，我们有时会有意在软件中加入现实生活中的元素。设计师称之为 *skeuomorphism* ，意思是将旧的熟悉的想法融入新的想法。在计算机用户界面(UI)中，文件夹类似于纸质文件夹的形状。删除按钮看起来像垃圾桶。保存按钮看起来像软盘。从现实世界的对象到 UI 组件的映射帮助人们快速理解它们的功能。它给人们带来了安慰，因为他们能够在与新技术互动的同时从过去的经验中吸取教训。在向新技术过渡的最初阶段，Skeuomorphism 是最有帮助的，但是当人们最终习惯了新技术，它就失去了意义。所有剩下的都是遗留的设计，有可识别的意义，但不是起源(我肯定不是每个阅读这篇文章的人都知道软盘是什么)。

2020 年，地球上最大的软件托管服务 GitHub 宣布了存档计划。其倡议之一是[将所有现存的代码库](https://archiveprogram.github.com/arctic-vault/)存档到北极山脉深处的一个金库中。我怀疑在一百年后，考古学家将不再研究骨头和古代文物，而是我们的软件。他们将不再挖掘化石，而是硬盘。他们将不再检查洞穴壁画，而是检查激光玻璃碎片(是的，这就是[微软的二氧化硅项目](https://www.microsoft.com/en-us/research/project/project-silica/)计划将大量数据保存超过 1 万年的方式)。我们的后代会从我们的代码中学到什么？

从更哲学的角度来说，我们的后代会从我们身上学到什么？我的谷歌搜索历史、亚马逊购买和 Spotify 播放列表被用来训练机器学习模型，这些模型可以准确预测我的行为。脸书和 YouTube 上不计其数的图像和视频可以被模特用来通过我的外表、声音，甚至我的走路姿势来识别我。

不要忘记公司用来培训语音助手、翻译服务、语法检查器等的文本海洋。就在最近，根据超过 1000 亿个参数训练的 [GPT-3 模型](https://arxiv.org/abs/2005.14165)，已经显示出不可思议的通用语言能力，可以与真人相媲美。它已经被用来创建机器人，这些机器人[从简单的英语中生成代码](https://twitter.com/sharifshameem/status/1282676454690451457?ref_src=twsrc%5Etfw%7Ctwcamp%5Etweetembed%7Ctwterm%5E1282676454690451457%7Ctwgr%5E%7Ctwcon%5Es1_&ref_url=https%3A%2F%2Fmachinelearningknowledge.ai%2Fopenai-gpt-3-demos-to-convince-you-that-ai-threat-is-real-or-is-it%2F) , [根据简短的提示撰写完整的博客文章](https://maraoz.com/2020/07/18/openai-gpt3/)，或者[与人类进行完全实时的对话](https://www.youtube.com/watch?v=PqbB07n_uQ4&t=63s)。

我们存在的越来越多的方面现在可以简化为神经网络中的参数。我们死后，我们仍然会以比特和数字的形式继续存在。

后代能了解我们什么？留给后代了解我们的是什么？我们空前的科技繁荣对未来的考古学家有帮助吗？把所有的东西都赤裸裸地展示出来会不会让它失去一些美感？只有时间能证明一切。