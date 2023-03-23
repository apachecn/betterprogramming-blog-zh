# 如何在 Python 环境中运行 Meta 的新人工智能系统‘Galactica’

> 原文：<https://betterprogramming.pub/how-to-run-metas-new-ai-system-galactica-in-a-python-environment-57e6b96420b1>

## 测试有争议的人工智能仅两天后就被关闭

![](img/91c1f7e2dc7a707b3cff0bd81ff95221.png)

[斯蒂夫·约翰森](https://unsplash.com/@steve_j?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

11 月 15 日，卡拉狄加正式呈现在世人面前。它是 Meta AI 为科学开发的大型语言模型，应该能够自动*组织*科学知识。

在实践中，这意味着能够[*总结学术文献，解决数学问题，生成维基文章，编写科学代码，注释分子和蛋白质，以及更多的*](https://twitter.com/paperswithcode/status/1592546933679476736?s=20&t=DJ6184tl4871w-Guwfhstg) *。*

Meta AI 由深度学习巨头 Yann LeCun、Mark Zuckerberg 和 Rob Fergus 创立。预印本[论文](https://galactica.org/static/paper.pdf)不缺少最高级，仅举几个例子:训练了 4800 万篇论文、教科书、课堂笔记等等。首先引用伽利略·伽利雷的一句话来描述数据集。

在某些任务上，以 68.2%对 49.0%的分数显著击败 GPT-3。当然，一些能激发很多兴趣的东西。对于此次发布，网页 galactica.org 允许测试人工智能。然而，当第一批结果发布在社交媒体上时，很明显有些不对劲。

很明显，产生的许多结果没有意义。有人称之为 [*编东西的 AI 知识库*](https://www.aiweirdness.com/galactica/)*；还有人把它描述成一个什么 [*的例子，危险的 AI 长得像*](https://towardsdatascience.com/galactica-what-dangerous-ai-looks-like-f31366438ca6) 。而且上市才两天，就[被关停](https://www.cnet.com/science/meta-trained-an-ai-on-48-million-science-papers-it-was-shut-down-after-two-days/)。*

现在，如果你，尽管公共反斜杠变得好奇，仍然有一种方法在你自己的系统上运行卡拉狄加，尽管公共演示被关闭。这个故事会引导你度过难关。

我们将概括一个演示提示，尝试我们自己的提示，并查看 GPU 和 CPU 上的结果和计算时间。

# 要求

训练卡拉狄加所需的硬件非同寻常。

有几个不同大小的型号，从`Mini` (125 M 参数)到`Huge` (120 B 参数)。

`Huge`模型是在 128(！)NVIDIA A100 80 GB 显卡。虽然推断应该在一台设备上运行，但每张卡的价格都超过 10，000 美元。

因此，在消费级电脑上运行完整的模型在短期内是不可能的。然而，我们将能够运行一些较小的模型。

# **可用型号**

让我们先来看看不同的小型模型应该如何表现:我们可以通过比较不同模型的验证损失(本文图 6)来估计它们与`Huge`模型的比较情况:

```
 Model     Parameters   Loss   
 ---------- ------------ ------- 
  Huge       120B         ~1.8   
  Large      30B          ~1.81  
  Standard   6.7B         ~2     
  Base       1.3B         ~2.25  
  Mini       125M         ~2.8
```

比较这些型号，`Mini`型号的损失比`Huge`型号高 50%以上。虽然这为我们提供了损失的定量估计，但很难看出这对结果的质量意味着什么。稍后，我们将在不同的模型尺寸上测试相同的提示。

为了下载模型的权重，您需要足够的磁盘空间。对于标准模型，您将需要大约 25 GB 的空间，并且所需空间与参数的数量成比例。

## 装置

卡拉狄加运行在 Python 上，所以你需要一个工作的 Python 环境。

此处给出的说明适用于包管理系统，如 [miniconda](https://docs.conda.io/en/latest/miniconda.html) 或 [anaconda](https://www.anaconda.com) 。 [Galactica GitHub](https://github.com/paperswithcode/galai) 提供了通过 pip 和`pip install galai`安装 Galactica 的快速入门指南。

对我来说，这不工作，并显示 CUDA 的问题，虽然我已经在一个非 CUDA 系统上测试过。

幸运的是，模型重量也可以在[拥抱面部中枢](https://huggingface.co/models?other=galactica)中获得，并且它们可以在变形金刚库中开箱使用。

这对我来说完美无缺，这是我们将在这里描述的路线。顺便说一下，虽然该模型也在 CPU 上运行，但强烈建议使用 GPU 加速，但安装可能会很棘手。我在装有 GeForce 2080 Ti 的 Windows 系统和装有苹果 M1 Max 的 Macintosh 系统上进行了测试。

首先，确保您已经正确设置了 GPU 环境。对于 Nvidia GPUs，您可以遵循其中一个 CUDA 指南，例如[这一个](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html)用于 Windows。通过在命令行执行`nvidia-smi`可以快速检查是否安装了 CUDA。您应该看到驱动程序和安装的 CUDA 工具包版本。对于 M1 Mac 系统，您将需要 Xcode 命令行工具。一个好的指南可以在这里找到[。](https://towardsdatascience.com/installing-pytorch-on-apple-m1-chip-with-gpu-acceleration-3351dc44d67c)

*   从在 conda 中创建一个新环境开始:`conda create -n torch-gal python=3.8`
*   然后激活它:`conda activate torch-gal`

卡拉狄加使用 PyTorch，所以按照 PyTorch 网站上的安装说明[进行系统配置](https://pytorch.org/get-started/locally/)。对于 Windows 和 CUDA 显卡，这意味着您需要确保选择正确的 CUDA 工具包。对于 M1 Mac 系统，如果您想要 GPU 加速，您将需要选择预览(夜间)-版本。

*   CUDA 11.7 的例子:`conda install pytorch torchvision torchaudio pytorch-cuda=11.7 -c pytorch -c nvidia`
*   M1 的例子:`conda install pytorch -c pytorch-nightly`。
*   安装一些实用程序包:`conda install numpy tokenizers`
*   接下来，我们将安装变形金刚库:`pip install transformers accelerate`
*   另外，如果你喜欢用 Jupyter 笔记本做编码:`conda install -c conda-forge jupyter jupyterlab`
*   您可以使用以下函数测试 Python 中的 torch 是否可以访问 GPU。Mac: `torch.backends.mps.is_available()`，CUDA: `torch.cuda.is_available()`

# 提示

一旦一切都设置好了，我们可以概括一下[卡拉狄加演示](https://galactica.org/explore/)中的提示。我们将使用以下提示作为示例:

*   `The Transformer architecture [START_REF]`

其中包含参考关键字[START_REF]并应该给我们一个参考著名的 [*注意是你所需要的*](https://arxiv.org/abs/1706.03762) 论文。

## 中央处理器

我们可以通过变形金刚的高级 API 在 CPU 上运行卡拉狄加。用相应的型号替换型号串(例如，`facebook/galactica-6.7b`是**标准**型号，`facebook/galactica-125m`是**迷你**型号)。)注意，在第一次执行时，库会将权重下载到缓存中。这可能是您的主驱动器，而不一定是保存代码的地方，所以请确保有足够的磁盘空间。如上所述，模型可能非常大。

```
from transformers import pipeline
model = pipeline("text-generation", model="facebook/galactica-6.7b")
input_text = "The Transformer architecture [START_REF]"
model(input_text)
```

这将为我们提供以下结果，这是对 Transformers 论文的预期参考。

> [{ ' generated _ text ':' The Transformer architecture[START _ REF]Attention is All you Need，Vaswani is a sequence-to-'}]

## GPU: CUDA 和 MPS

为了在 GPU 上运行相同的提示，我们将 CUDA 设备作为参数传递给模型初始化，例如，`pipeline(..., device=0),`在第一个 GPU 上运行。目前，使用`pipeline`时不支持苹果的 MPS。

为了克服这一点，我们可以使用一个更低级的 API，让我们能够更好地控制代码的执行位置:`OPTFForCausalLM,`，它也适用于 MPS。我发现它工作更稳定，消耗更少的内存。这是这样的:

```
from transformers import AutoTokenizer, OPTForCausalLM

tokenizer = AutoTokenizer.from_pretrained("facebook/galactica-6.7b")
model = OPTForCausalLM.from_pretrained("facebook/galactica-6.7b", device_map="auto")
input_ids = tokenizer(input_text, return_tensors="pt").input_ids.to("cuda")

outputs = model.generate(input_ids)
r = tokenizer.decode(outputs[0])
print(r)
```

输出:

> 变压器架构[START_REF]注意是你所需要的，Vaswani[END_REF]是一个序列到-

对于 Mac，您应该指定 MPS 而不是 cuda:。`input_ids.to("mps")`。你也可以使用 CPU。`input_ids`。

## 计时

在标准架构上运行该提示符大约需要一分钟:

```
 Device           Execution Time  
 ------------------------ ---------------- 
  i9-9920X                 55s             
  NVIDIA GeForce 2080 Ti   68s             
  Apple M1 MAX (CPU)       105s            
  Apple M1 MAX (GPU)       29s 
```

# 测试卡拉狄加

## 问题

查找参考资料似乎可以。可以说这里有一点好处；用传统的搜索引擎找到相关文献可能要快得多。那么一些更有挑战性的问题怎么样？我们来试试一个基本的科学问题:*一个细胞的细胞核有多大？* Google 返回 233.000.000 个结果，第一个答案告诉我们大概的大小。6 微米和对维基百科文章的引用。这个需要 0.6s。

卡拉狄加建议以`Question: [xxx] Answer:`的形式公式化问题，让我们看看“*问题:细胞核有多大？”*

回答:

```
(Trained on Apple M1 w. GPU)

   Model     Time   Question: How large is the nucleus of a cell? Answer:  
 ---------- ------ ------------------------------------------------------- 
  Mini        7.5   100,0                                                  
  Base       10.6   10000                                                  
  Standard   29.2   The nucleus of a cell is
```

显然，结果没有太多意义。另一个带有修改提示的测试(没有问题): ***细胞核有多大？***

> 细胞核是遗传物质

这也没什么意义。

作为对比，我在基于 GPT-3 的人工智能写作工具 [Lex](https://lex.page/) 上运行了同样的问题。这大约需要。大约 5s:

> 细胞核的大小因细胞的具体类型而有很大差异。通常，大多数细胞核的大小在约 1 至 10 微米(微米)的范围内。有些原子核可以大到 100 μm。

这显然是一个更好的结果。

## 关键词

Galactica 提供了不同的关键字，可以添加到文本提示中以定制输出。我们已经有了用于引用的`[START_REF]`，用于提问的`Question`，但是还有几个[更多的](https://github.com/paperswithcode/galai)，比如用于总结文本的`TLDR,`。

# 摘要

尽管有一些限制，人们可以执行卡拉狄加——甚至在消费级计算机上。

我们可以重现一个演示提示，虽然引用查找对于文本生成来说似乎可以，但结果确实值得怀疑。

人工智能写作工具似乎给出了更合理的结果。我们无法测试据称最好的巨型模型——因为它超出了消费级硬件的能力范围。我认为，这样的硬件可能连一些研究实验室都负担不起。虽然我们没有测试卡拉狄加的全部可能性以及论文中所声称的，但我认为这一瞥给了我们一些可以期待的视角。

从根本上说，大型语言模型是否适合这类任务是有争议的。一种思路认为，大模型会变得太大，我们最终得到的是 [*随机鹦鹉*](https://dl.acm.org/doi/10.1145/3442188.3445922) 而不是有所收获的模型。这里展示的结果肯定是有道理的。

*我目前正在探索这种形式，以分享更多的数据科学思想，并从算法的角度讨论话题。你有什么改进的建议吗，或者你有一个我应该调查的话题吗？让我知道！*