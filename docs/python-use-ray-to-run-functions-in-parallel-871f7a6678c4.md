# 如何使用 Ray API 并行运行 Python 函数

> 原文：<https://betterprogramming.pub/python-use-ray-to-run-functions-in-parallel-871f7a6678c4>

## 构建分布式应用程序

![](img/71f8284ebd846b69b4b950ce617c98ad.png)

阿巴斯·特拉尼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

有许多方法可以并行运行函数，但是可能会有许多警告需要处理。我们可以通过使用 Ray is 来克服所有这些问题，这是一个用于构建分布式应用程序的简单、通用的 API。

雷能为你做什么:

*   *为构建和运行分布式应用提供简单的原语。*
*   *支持最终用户并行处理单个机器代码，几乎不做任何代码更改。*
*   *在 core Ray 之上包含一个庞大的应用程序、库和工具生态系统，以支持复杂的应用程序。*

如何安装 Ray:

```
sudo pip3 install Ray
```

一个基本的射线示例:

*   第 2 行将 ray 导入我们的项目
*   `@ray.remote decorator`用于定义哪些功能将并行运行
*   第 31 行`ray.init()`初始化射线
*   第 32 行`ray.get([func1.remote(),func2.remote()])`并行运行的函数列表

Ray 提供了执行函数的非常好的输出

```
./test1.py
(func2 pid=1596) func 2 Start Time = 11:51:48
(func1 pid=1591) func 1 Start Time = 11:51:48
(func2 pid=1596) func 2 End Time = 11:51:50
(func1 pid=1591) func 1 End Time = 11:51:50
```

这是一个非常基本的并行执行函数的例子，这些函数不返回任何东西，让我们看一个更高级的并行执行的例子，函数接受参数并返回结果

*   第 14 行—使用`ray.get`我们用参数并行执行函数，它们在变量`ret1`和`ret2`中返回结果。

`ray.get`函数以列表形式接受函数作为参数，也以列表形式返回结果，这意味着当每次要执行的函数数量不同时，我们可以执行以下非常有用的操作。

我希望这篇文章对你有用！