# 如何让你的 PyTorch 代码运行得更快

> 原文：<https://betterprogramming.pub/how-to-make-your-pytorch-code-run-faster-93079f3c1f7b>

## 让您的代码运行速度惊人！

![](img/407d3dc647282c8aa3eed8378c0d4b93.png)

珍妮·希尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

PyTorch 因其灵活性受到研究人员的高度赞赏，并已进入希望跟上最新突破性研究的主流行业。

简而言之，如果你是深度学习的实践者，你迟早要和 PyTorch 面对面。

今天，我将介绍一些技巧，它们将大大减少 PyTorch 模型的培训时间。

# 数据加载

为了给我们的模型加载数据，我们使用了`[torch.utils.data.DataLoader](https://pytorch.org/docs/stable/data.html#torch.utils.data.DataLoader)`，它在数据集上创建了一个 Python iterable。

让我们来看看它的签名:

```
DataLoader(dataset, batch_size**=**1, shuffle**=False**, sampler**=None**,
           batch_sampler**=None**, num_workers**=**0, collate_fn**=None**,
           pin_memory**=False**, drop_last**=False**, timeout**=**0,
           worker_init_fn**=None**, *****, prefetch_factor**=**2,
           persistent_workers**=False**)
```

正如我们所见，引擎盖下有许多东西。为了加快我们的训练，我们需要把注意力集中在两个方面。

## **工人数量**

默认情况下，其值为`0`。那是什么意思？这意味着数据将由运行训练代码的主进程加载。这是非常低效的，因为不是训练你的模型，主过程将只关注加载数据。

有更好的方法。如果我们设置`num_workers` > `0`，那么将会有一个单独的进程来处理数据加载。您的数据加载将是异步的(也就是说，它不会干扰模型加载)。

这会给我带来多大的速度提升？

工人越多越好。您可以用不同的`num_workers`运行一个时期，并测试哪个`num_workers`值最适合您。

最坏的情况，如果你只是保持`num_workers` = `1`，它还是会给你[1.2 倍的加速](https://www.youtube.com/watch?v=9mS1fIYj1So&ab_channel=ArunMallya)。

## pin _ 存储器

*注意:这必须连同*`*num_workers*`*>*`*0*`*条件一起完成。否则没有速度提升。*

默认情况下，其默认值为`False`。

那是什么意思？

CPU 到 GPU 的内存传输是同步的(即，您的模型训练将停止并进行 CPU 到 GPU 的内存传输，从而降低您的训练速度)。

当您将它设置为`True`时，这些传输将变成异步的，您的主过程将只关注模型训练。这又是一个推动因素。

提升了多少？

最坏的情况，当`num_workers` = `1`、`pin_memory` = `True`时，你会得到[一个 1.22 倍的加速](https://www.youtube.com/watch?v=9mS1fIYj1So&ab_channel=ArunMallya)。

# 使用 cuDNN 自动调优器

*注意:这仅适用于模型中有卷积的情况。*

cuDNN 有很多计算卷积的算法。根据您的硬件，不同的算法会执行得更快。但是由于默认情况下不使用自动调优器，所以速度没有提高。

## 如何打开自动调谐器:

```
torch.backends.cudnn.benchmark = True
```

这将导致模型的初始训练步骤变慢，因为它将尝试不同的算法并选择最好的一个。不过，总的来说，训练时间会减少。

# 使用 AMP(自动混合精度)

通常，权重和偏差的值存储在 GPU 上的 32 位浮点中。但是并不是所有的内存都被利用了(因为你在某些地方使用 32 位来存储 0)。这不是最佳的。

自动混合精度旨在通过存储不同精度的值来减少 GPU 内存的浪费。根据需要，您的值可以存储为 16 位或 32 位。这个过程称为自动铸造。

现在，将会发生的一件事是，一个 16 位变量的梯度也将存储在 16 位中。但如果它下溢(即梯度太小而无法存储在 16 位中，在这种情况下，它将变成零)呢？比例因子乘以这样的梯度，将零梯度值推到一个相当大的值，并且不影响训练。这个过程被称为梯度缩放。

这将释放 GPU 中的内存。

神经网络训练的基本原则是什么？尽可能使用*更大的批量*。

因此，AMP 帮助我们做到了这一点。更大的批量也意味着一个时期内更少的步骤，这又节省了我们的培训时间。

下面是来自[官方 PyTorch 文档](https://pytorch.org/docs/stable/notes/amp_examples.html)的一个小例子:

我们看到，为了利用 AMP 的能力，不需要做太多的改变。但是有一个警告:这将导致稍微差一点的准确性。也就是说，下跌幅度还不足以影响你的最终目标。

然而，速度的提高是真实的。在最坏的情况下，加速至少是 2 倍(见 PyTorch 博客)。

由于批量大小的变化，你将不得不调整你的学习率。您也可以加入[学习率预热](https://github.com/Tony-Y/pytorch_warmup#radam-warmup)和[学习率衰减](https://pytorch.org/docs/stable/optim.html)。除此之外，你可以在你的模型中使用[重量衰减](https://pytorch.org/docs/stable/optim.html)。

另一方面，您可以选择专门为较大批量设计的优化器，如 [LARS](https://paperswithcode.com/method/lars) 、 [LAMB](https://github.com/btahir/tensorflow-LAMB?utm_source=catalyzex.com) 或 [NovoGrad](https://pytorch-optimizer.readthedocs.io/en/latest/_modules/torch_optimizer/novograd.html) 。

# 禁用归一化层后直接卷积的偏差

如果卷积层之后是归一化层，则偏差的使用无效。为什么？

因为在归一化中，执行的第一个操作是减去平均值。这消除了偏见的使用。因此，如果你这样做，你只是在你的模型中引入了无用的参数。这毫无理由地减缓了你的训练。

只需为卷积层设置`bias=False`，然后是归一化层。

这将给你一个明确的速度提升，因为你减少了要计算的参数数量。

# 有效地将你的渐变设置为零

`model.zero_grad()`是每个 PyTorch 代码中都有的东西。这会将最后一步的渐变设置为零。这是一个巨大的问题。

当我们调用这个函数时，会启动单独的 CUDA 内核，从而产生不必要的开销。此外，该功能导致反向传播期间的读+写操作(由于使用了`+=`操作，读由`+`触发，写由`=`触发)。

另一方面，有一个更有效的方法来消除你的梯度。你应该*总是*使用这个:

```
for param in model.parameters():
    param.grad = None
```

为什么这样更好？它不会为每个变量设置不必要的内存开销。它直接设置梯度(即只进行写操作，与`model.zero_grad()`不同)。

您获得了速度提升，因为每次迭代发生时您都剔除了单个操作(读取操作)。

# 禁用最终培训的调试 API

一旦您完成了模型的调试，您应该停止使用所有的调试 API，因为它们有很大的开销。

在代码中的导入后添加以下行:

```
torch.autograd.set_detect_anomaly(False)
torch.autograd.profiler.profile(False)
torch.autograd.profiler.emit_nvtx(False)
```

第一行警告您当`True`时，任何梯度将获得 NaN 或无穷大值。

第二行告诉你`True`时 CPU 和 GPU 上每个操作花费的时间。

第三行为您的跑步创建一个带注释的时间线，当`True`时，NVIDIA Visual Profiler (NVP)可以可视化该时间线。

一旦你停止所有这些调试工具，你将再次减少你的培训时间。

# 结论

在本文中，我们看到了如何提高 PyTorch 代码的速度。这导致更好地利用资源和我们的时间。我也解释了为什么每一招都有效，这样你就不会在黑暗中射箭。

# 参考

*   [https://NV labs . github . io/ECC v2020-mixed-precision-tutorial/files/szy mon _ MIGA cz-py torch-performance-tuning-guide . pdf](https://nvlabs.github.io/eccv2020-mixed-precision-tutorial/files/szymon_migacz-pytorch-performance-tuning-guide.pdf)