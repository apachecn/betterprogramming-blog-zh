# 更快地运行更漂亮的脚本

> 原文：<https://betterprogramming.pub/run-your-prettier-scripts-even-faster-ef85381cf28e>

## 充分利用 Apex 的漂亮配置

![](img/ff979b0ef12276f6d8637c83b7c4d463.png)

照片由 [RoonZ nl](https://unsplash.com/@roonz_nl?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

从[我的上一篇文章](/stop-manually-formatting-your-code-dcca34de7b1e)，你知道我是格式化和自动化任务的爱好者。我完全赞成多成就，少做事。这就是为什么我最近一直在互联网上搜索，试图找到运行 Prettier 的最佳方式，这是一个著名的代码格式化程序，它对性能较差的计算机也不友好。

> 我完全赞成多成就，少做事！

比方说，我没有最新的也没有最漂亮的电脑，尽管我可以抱怨这会降低我的工作效率，让我发疯……但我不会。我选择看到它对我的工作流的积极的附带损害，例如提高我的创造力以找到最有效的方式来运行脚本，并避免我的计算机在执行过程中崩溃。

但是，等等…为什么它会崩溃？漂亮有这么重吗？嗯，当使用默认配置并使用 Apex 的更漂亮的插件[时，就可以了。运行格式化脚本时，浏览每个文件大约需要一秒钟，在处理大型项目时，这可能是个问题。想想你每天提交多少次，你项目中的文件数量，然后乘以你团队中开发人员的数量。](https://github.com/dangmai/prettier-plugin-apex)

> 浏览每个文件大约需要一秒钟，在处理大型项目时，这可能是一个问题。

为了进一步理解为什么需要这么长时间，我将使用 Apex 插件对一个普通的 Apex 项目进行更漂亮的执行。执行顺序如下:

1.  我们执行漂亮的命令。
2.  更漂亮的 Apex 插件叫做。
3.  运行 apex 解析器—Apex 解析器在 Java 上运行，这是一种高成本的操作，因为 Java 虚拟机大约需要半秒钟才能启动。
4.  您的代码已格式化！对下一个文件重复该循环。

你认为这里有任何可能的改进吗？你猜对了！我们确实可以通过避免为每个文件运行一个解析器，而是让它们共享一个解析器来优化执行。让我引导你通过它。

我假设你已经使用了 prettier，并且熟悉它的安装和配置。所以只要确保你已经安装了 Apex 插件。

```
# Install locally Apex Prettier plug-in
npm install --save-dev prettier prettier-plugin-apex# Or install globally Apex Prettier plug-in
npm install --global prettier prettier-plugin-apex
```

我们现在准备用以下命令初始化终端中的服务器:

```
# initializes the server
node node_modules/prettier-plugin-apex/bin/start-apex-server.js
```

使用常用命令，通过一些加载项运行得更漂亮:

```
# run the prettier command
prettier --apex-standalone-parser built-in --list-different \"**/*.{cls,cmp,component,css,html,js,json,md,page,trigger,xml,yaml,yml}\"
```

你注意到我们命令中的标签了吗？这是我们必须确保更漂亮地使用内置的独立解析器以获得更好性能的方法。

一旦您看到所有的文件都被解析，您可以用下面的命令关闭解析器:

```
# stop server
node node_modules/prettier-plugin-apex/bin/stop-apex-server.js
```

# 脚本版本

如果您不想单独运行每个命令，或者您想将它添加到您的 CI/CD 流中，您可以将命令添加到`package.json`中，并在一个脚本中运行它们，如下例所示。它将运行解析器并等待它准备好，然后，它将更好地执行并关闭解析器。

# 额外提示

即使进行了优化，每天格式化整个项目也没有多大意义。这就是为什么我采用以下策略:

*   每隔一段时间运行一次完整的脚本，以保持整洁。
*   改变之前的脚本，只修饰你正在处理的文件。这将很容易验证你所做的改变和漂亮的人所做的改变。