# 应用于 JavaScript 的干净代码(第 7 部分:实用重构)

> 原文：<https://betterprogramming.pub/clean-code-applied-to-javascript-part-7-practical-refactoring-4f18eff33350>

## 关于编写和维护干净代码的惊人好处的系列文章

![](img/34749ea1482570e186a9bd988e177106.png)

梅勒妮·范·卢文在 [Unsplash](https://unsplash.com/s/photos/caesar?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

本文是深入研究干净代码主题的系列文章的第 7 部分，应用于 JavaScript。

在本系列中，我们将讨论每个程序员都应该知道的关于干净代码的经典技巧，并将它们应用于特定的 JavaScript/TypeScript 语言。

*   [第 1 部分:开始之前](https://medium.com/better-programming/clean-code-applied-to-javascript-part-1-9f3badd5715)
*   [第二部分:变量](https://medium.com/better-programming/clean-code-applied-to-javascript-part-ii-variables-8302f01e539c)
*   [第 3 部分:功能](https://medium.com/@ccaballero/clean-code-applied-to-javascript-part-3-functions-f9f26b221736)
*   [第 4 部分:注释](https://medium.com/better-programming/clean-code-applied-to-javascript-part-4-3f6db21ee4e5)
*   [第 5 部分:例外情况](https://medium.com/better-programming/clean-code-applied-to-javascript-part-5-exceptions-d46e07691c19)
*   [第 6 部分:避免条件复杂性](https://medium.com/better-programming/clean-code-applied-to-javascript-part-6-avoid-conditional-complexity-5ee9cbb1b26a)
*   [第七部分:实际重构示例:凯撒密码](http://www.carloscaballero.io/clean-code-applied-to-javascript-part-vii-practical-refactoring-example-ceaser-chipher)

在这一系列文章中，我们介绍了允许我们生成更易维护的代码的编程技术。大多数编程技术和建议来自于[“干净的代码”一书](https://www.goodreads.com/book/show/3735293-clean-code)以及这些技术在多年经验中的应用。

在本文中，我将一步一步地描述重构技术的应用，我已经将这种技术应用到为我的一个编程基础类编写的代码中。如果你开始开发软件，我建议你先尝试用你知道的技术和工具解决问题(我们将使用 JavaScript 作为编程语言)。

如果你已经有编程知识，解决问题很容易，练习就不一样了。在这种情况下，提供了一个解决方案——起始代码——挑战是应用不同的重构技术来深入理解代码并使其更易于维护。

我准备了一个 GIT 存储库，在这里你可以找到我们将在本文中使用 JavaScript 一步一步解决的算法的所有版本。它还有一系列 npm 脚本，允许您使用以下术语执行每个步骤的代码:

这里是 [Git 库](https://github.com/Caballerog/blog/tree/master/refactoring-caesar)，在这里你可以找到代码。

# 问题:凯撒密码

这个问题的描述摘自[维基百科](https://en.wikipedia.org/wiki/Caesar_cipher)。

> 凯撒密码是最简单也是最广为人知的加密技术之一。这是一种替代密码，明文中的每个字母都被替换为字母表中某个固定位置的字母。例如，右移 3，E 将被 H 替换，F 将变成 I，依此类推。
> 
> 这种转换可以通过对齐两个字母来表示；密码字母表是向右旋转一些位置的普通字母表。

例如，这是一个凯撒密码，它使用了 6 位的右旋转，相当于右移 6 位:

加密时，一个人在“明文”行中查找消息的每个字母，并在“密码”行中写下相应的字母。

*   明文:敏捷的棕色狐狸跳过懒惰的狗
*   密文:QEB NRFZH 约尔特克 CLU GRJMP LSBO QEB IXWV ALD

破译是反向进行的，左移 6。

# 什么是重构，为什么要重构？

重构是软件开发行业中一个众所周知的话题。至此，我们对这个话题做一个介绍，但我推荐你阅读[这篇文章](https://www.cuelogic.com/blog/what-is-refactoring-and-why-is-it-important)。

> *重构不是* **银弹** *但是它是一件有价值的武器，它有助于你很好地掌握你的代码&以至于项目(软件/应用)。*
> 
> [“什么是重构，为什么它很重要？”](https://www.cuelogic.com/blog/what-is-refactoring-and-why-is-it-important)
> 
> *这是一个获取现有代码并对其进行改进的科学过程，同时使代码更具可读性、可理解性和整洁性。此外，添加新功能、构建大型应用程序和修复 bug 也变得非常方便。*
> 
> [“什么是重构，为什么它很重要？”](https://www.cuelogic.com/blog/what-is-refactoring-and-why-is-it-important)

## **为什么重构很重要**

*   它改进了软件/应用程序的设计。
*   它使软件更容易理解。
*   它有助于发现错误。
*   它有助于修复现有的遗留数据库。
*   它为用户提供了更大的一致性。

一旦我们知道了我们想要解决的问题，我们就执行一个实现，任何一个刚开始开发的人都可以用很少的时间来实现:

我们要开发的代码有两种方法:

*   `cipher` —将文本和偏移应用于一个方向。
*   `decipher` —执行与`cipher`相反的操作。也就是说，它解读文本。

我建议，无论何时你对代码执行重构，你都要有一系列的自动化测试来帮助你验证你没有“破坏”代码。在这个具体的例子中，我没有创建测试套件，而是使用标准的`console.assert`创建了两个检查。

因此，将通过以下断言来检查算法是否稳定:

我们已经有了要进行的挑战，让我们开始吧！

第一步是通过赋予代码语义值的变量名来删除代码中出现的幻数。这样，下列数字将被修改:

*   字母表中的字母数(26)。
*   属于算法应该循环的极限的每个字母，即:

```
a: 65
z: 90
A: 97
Z: 122
```

因此，我们定义了以下常量，这些常量使我们能够了解这些数字所代表的含义:

这样，更改后的代码将如下所示:

# 第二步。从 if-else 中提取类似的代码

下一步是识别那些重复的代码行，以便可以将它们提取到函数中。具体来说，存在于`if`控制结构主体中的赋值在整个代码中重复出现——这些是可以提取的。

也就是说，下面的代码片段`cipher = cipher.concat (String.fromCharCode (`可以从代码中存在的不同`if`中提取。该行在`if`结构之后执行，而`if`仅包含根据每种情况的不同逻辑。

当然，我们对`cipher`功能执行的操作也同样适用于`decipher`功能。

以下是应用此重构后的代码:

# 第三步。避免否则

下一步是避免与`else`控制结构块相关的代码。避免它是相当容易的。我们所要做的就是在循环开始之前将代码从`else`移动到变量`character`，所以这个值被指定为默认值。

重构后的代码如下:

# 第四步。合并 IF 逻辑

接下来的路是曲折的。我们必须合并对应于`if-elseif`的逻辑，这样我们只有两个控制结构`if`。

这个动作将允许我们在后面的步骤中观察到，我们真的有两条可选择的路径，而不是我们看起来的那些。

合并后的代码`if`逻辑如下:

# 第五步。简化算法的逻辑

在这一步，我们要推理出我们的算法不需要两个`if` 控制结构。相反，`cipher`和`decipher`功能都有一个`if-else`控制结构。关注函数`cipher` ，可以观察到有两种可能的选项来为变量`character`赋值。第一种可能性是从相应的第一个`if`获得的可能性:

第二个可能的值，在默认情况下和从另一个控制结构`if`中获得，如下所示:

因此，可以移除第二个`if`的逻辑，并将控制结构转换为`else`，对应于第一个控制结构`if`，因为，在不满足此`if`的条件的情况下，将为变量`character`分配第二个可能的值——无论第二个`if`在由默认值分配时是否满足。

下面是这次重构后的[代码。](https://gist.github.com/42d7ad086daf7d39506598c04f502742)

# **第六步。封装条件**

我们算法的条件相当复杂，难以理解，因为它缺乏语义值。因此，代码中的下一步被称为“封装条件”

具体来说，我们专注于封装`cipher`和`decipher`条件。

`cipher`:

`decipher`:

事实上，这种逻辑可以概括为以下四个功能:

执行此封装后的代码如下:

# 第七步。移除 if-else 结构控制

控制结构`if-else`对同一变量(`character`)进行赋值。因此，您可以从`if`中提取条件逻辑并将其存储在一个变量中，如下所示:

变量`character`的赋值仅由旋转值修改，旋转值有两个可能值:

1.  `NUMBER_LETTERS (26)`
2.  `NO_ROTATION (0)`

因此，我们可以定义变量`rotation`,以便它允许我们提高代码中的粒度级别，如下所示:

生成的代码如下:

这一步之后产生的两个函数的代码如下:

# 第八步。变量命名

完成重构算法的最后一步是将循环中的变量`i`重命名为一个更合适的名称，比如`position`(这个变化看起来很小，但重要的是我们要为变量赋予语义值，包括循环中经典的`i`、`j`和`k`)。

应用这些简单的步骤后，我们算法的最终结果如下:

# 结论

在本文中，我们提出了一些从基本解决方案到可理解代码的重构建议。

我已经一步一步地展示了我的推理。当然，从你的角度来看，还有其他方式和一些决定可能不是最合适的。出于所有这些原因，只要你的想法有建设性，我邀请你与社区分享你的想法！

这个挑战旨在让我的业内同事(他们认为这对他们来说很难)看到其他同事如何一步一步地执行重构任务。

在下一篇文章中，我将继续改进代码，尝试从函数式编程的角度给出解决方案的愿景。

最后，我们提出了以下几点:

*   神奇的数字
*   从 if-else 中提取类似的代码
*   避免其他
*   合并 IF 逻辑
*   简化算法的逻辑
*   封装条件
*   移除 if-else 结构控制
*   变量命名

当然，我给你留下了代码，包括原始代码和最终代码，这样你就可以对它进行最后的平衡:

最终代码:

[*。*](http://www.carloscaballero.io/clean-code-applied-to-javascript-part-vii-practical-refactoring-example-ceaser-chipher)