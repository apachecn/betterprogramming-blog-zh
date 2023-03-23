# 为什么你的文本编辑器插件属于一个锁文件

> 原文：<https://betterprogramming.pub/your-text-editor-plugins-belong-in-a-lock-file-simple-thread-f847a0d7aac>

我刚刚更新了你所有的文本编辑器插件。你的反应是什么？

类似这样的？

![](img/09c6ca858ad4c0adc786dc66ccff33ab.png)

[女同胞](https://unsplash.com/@cowomen?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

你已经很多年没有更新那些插件了，是吗？谁会关心那些新特性、错误修复或者性能和安全改进呢？只是交叉手指，希望你的笔记本电脑不会死，逼着你升级。更好的是，您有完整的映像备份，对吗？这样你就可以永远保留那些老插件了。

想到要更新[那些插件](https://www.simplethread.com/simple-threads-favorite-developer-things/)，退缩是可以理解的。大多数开发人员都因为这样或那样的原因被烧伤了。另外，[谁有时间](https://www.simplethread.com/dear-client-heres-why-that-change-took-so-long/)听那些废话？在你更新后，你遇到了插件之间的一些模糊的冲突，或者作者推了一个测试版。更糟糕的是，新版本的插件是一个真正的性能瓶颈。

当然，问题似乎从来不会在更新后立即出现，而是在最不合适的时候出现。几天后，你的最后期限[临近](https://www.simplethread.com/dear-client-heres-why-that-change-took-so-long/)。您没有简单的方法来恢复您的更新。见鬼，你甚至不知道哪个插件是罪魁祸首。现在你必须做出选择。你是否苦于调试你的编辑器或禁用插件，一只手绑在背后继续你的工作？

不过更新你的插件并不需要这么痛苦。如果你的编辑器插件像你的应用程序依赖一样有一个锁文件，那不是很好吗？(您锁定了您的应用程序依赖项，不是吗？)然后您可以将该文件添加到版本控制中。回到原来的设置就像打个响指。只需检查之前的提交并运行 install 命令。事后看来很明显，对吧？

我已经将我的插件保存在 plugin.lock 文件中大约一年了，这真是太棒了。它的工作方式与您预期的完全一样。我每隔几个月就会更新我的插件。如果我遇到一个问题，不想马上处理，我可以很快回复。为了调试，我可以做一个快速的 git diff 来查看最近更新了哪些插件。如果一个插件行为不当，我会从我的 git 历史中取出旧版本，继续我的工作。如果我非常想确定哪个插件行为不当，我甚至可以做一个二分搜索法(递归地恢复一半的版本，检查问题是否仍然存在)。

创建和使用你自己的 plugin.lock 文件非常简单。下面我有几个热门编辑的例子。

# 精力

使用流行的 VIM 插件管理器 [vim-plug](https://github.com/junegunn/vim-plug)

创建您的锁文件…
`:PlugSnapshot plugin.lock`

从您的锁定文件中恢复…
`:source plugin.lock`

# 原子

创建你的锁文件…
`apm list --installed --bare | xargs -L 1 echo apm install > plugin.lock`

从您的锁定文件中恢复…
`sh plugin.lock`

# VS 代码

Visual Studio 代码的[版本支持这一点。](https://code.visualstudio.com/insiders/)

禁用自动扩展名更新(如果您还没有这样做)

![](img/dc722a10f33c59ab9ef1a69e595a5324.png)

创建你的锁文件…
`code-insiders --list-extensions --show-versions | xargs -L 1 echo code-insiders --install-extension > plugin.lock`

从您的锁定文件中恢复…
`sh plugin.lock`

如果你有在其他编辑器中创建`plugin.lock`的说明，请在评论中告诉我！