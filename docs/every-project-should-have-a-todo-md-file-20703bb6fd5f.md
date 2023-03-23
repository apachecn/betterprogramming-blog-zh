# 每个项目都应该有一个 TODO.md 文件

> 原文：<https://betterprogramming.pub/every-project-should-have-a-todo-md-file-20703bb6fd5f>

## 从代码编辑器中快速管理您的项目

![](img/2b558ff6f41bd97a21808cdeeb8396a8.png)

用 DALLE-2 生成的图像

每个项目都有路线图，无论是小型业余软件还是复杂的代码库。您从设计高级方面开始，然后通过将项目分解成要逐步完成的更小的任务来关注实现细节。

在开发的时候，记录你的进度和下一步要做的事情是很有用的，这会让你对整个项目有更多的了解。通过记住所有的微步骤，您可以做出更好的实现选择，而不会因为需要重写部分代码而影响未来的开发，因为它们的编写方式不允许您有效地达到您的目标。

有许多方法可以处理路线图、微步骤和进度跟踪。在本文中，我将向您展示`TODO.md`文件格式，并解释为什么您应该使用它。

# TODO.md 是什么？

`TODO.md`是一种简单的轻量级格式，基于广泛使用的 Markdown 文件格式。一个`TODO.md`文件包含组织在多个部分和子任务中的项目任务。保存一个`TODO.md`文件对于开发人员绘制项目大纲和跟踪进度以及外部查看者了解项目的计划和当前状态非常有用。

假设你已经很久没有做代码库了。可能很难记住已经完成了什么和下一步要做什么。在这种情况下，您经常需要检查提交历史，检查代码，寻找一些隐藏的`TODO`注释，或者运行程序，找出是否有需要修复的错误。

此外，如果你计划为一个项目做贡献，了解当前的状态是很有用的:最终要修复的 bug，要实现的特性，计划的重构，等等。如果你没有公开的计划或路线图，就很难参与进来。

# TODO.md 格式

尽管您可以自由使用自己的 TODO 样式，但为了清晰和与自动化工具兼容，保持文件标准化是明智的。

`TODO.md`文件通常放在项目的根文件夹中，以便于访问。一个`TODO.md`文件被分成几个部分。每个部分包含按主题、进度状态或其他标准分组的固有任务。如果`README.md`文件中没有项目名称和简短描述，您也可以包含它们。

如您所见，这种格式只是 Markdown 的一个非常基本的子集。在下面，你可以看到结果:

现在，每个人都可以简单地看一眼你的`TODO.md`，了解事情的大概。使用`TODO.md`文件，结合`README.md`用于演示，结合`DOCS.md`用于参考，让任何人只需阅读几个降价文件就能完全理解你的项目。

此外，还有一个有用的 VSCode 扩展，叫做 [Todo 看板](https://marketplace.visualstudio.com/items?itemName=coddx.coddx-alpha&ssr=false)，它从一个`TODO.md`文件中呈现一个任务板，并允许您直接在 VSCode 选项卡中使用极简 GUI 来管理您的任务。这里有一个简短的视觉演示:

![](img/46bdae3c8918be97da4b817191f8ad01.png)

来自[Visual Studio market place](https://marketplace.visualstudio.com/items?itemName=coddx.coddx-alpha&ssr=false)的待办事项看板演示

# 其他管理工具

还有数不清的其他项目管理工具，通常功能更强大，功能更丰富。对于多人同时工作的大型项目，建议使用一种更高级的替代方案，它支持最先进的功能，如截止日期的日历集成、实时通知和统计仪表板。

然而，在大多数情况下，更简单的方法是首选。保存一个`TODO.md`文件不需要初始开销，这在启动一个不会成为下一个数十亿美元服务的项目时非常有用。

增加一行降价通常是最快和最便宜的选择，不需要登录任何在线管理应用程序，离线时也无法使用。除此之外，`TODO.md`是高度可移植的，本质上是一个小的纯文本文件。

由于`TODO.md`被放在项目的根目录下，任何人都可以很容易地访问它，而不仅仅是它的开发者和管理者。这对开源项目来说非常好，因为它可以让感兴趣的人更容易参与进来并做出贡献。此外，您可以通过 Git 免费获得版本控制和并发冲突解决方案。

# 最后的话

总结一下，你应该在你的项目的根中包含一个`TODO.md`。这是管理开发过程的一种简单而有效的方式，不需要额外的软件或服务。这对于团队和个人程序员保持专注于目标和跟踪进展是有用的。

概括一下，这里有一个保存`TODO.md`文件好处的综合列表:

我希望你喜欢这篇文章。如果你有什么要补充的，请在评论中分享你的想法。感谢阅读！

如果你有兴趣学习[技能](https://heytdep.github.io/comp_posts/2)--Rust:-Groth16-Proof-Verification-From-Scratch/post.html)成为更好的软件开发人员，你应该看看下面这个故事:

[](/the-language-every-programmer-should-master-8d0dfc461284) [## 每个程序员都应该掌握的语言

### 软件开发不仅仅是写代码

better 编程. pub](/the-language-every-programmer-should-master-8d0dfc461284) 

# 资源

*   [GitHub 上的 todo . MD](https://github.com/todomd/todo.md)
*   [Visual Studio market place 上的 TODO.md 看板](https://marketplace.visualstudio.com/items?itemName=coddx.coddx-alpha&ssr=false)