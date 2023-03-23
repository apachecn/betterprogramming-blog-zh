# 编写不出意料的 Bash 脚本的 4 个技巧

> 原文：<https://betterprogramming.pub/top-tips-for-writing-unsurprising-bash-scripts-9b9f4f0cc30e>

## 编写健壮的 Bash 脚本的有用指南

![](img/65fe096c50fed669d1ac83e90c7bef33.png)

一种令人惊讶的编写 bash 脚本的方式(照片由 [Eddie Kopp](https://unsplash.com/@fiveohfilms?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄)

即使 Bash 的第一个版本是在 30 多年前发布的，人们仍然在发布编写 Bash 脚本的[最佳](https://www.taniarascia.com/how-to-create-and-use-bash-scripts/) [实践](https://sap1ens.com/blog/2017/07/01/bash-scripting-best-practices/) [指南](http://www.tothenew.com/blog/foolproof-your-bash-script-some-best-practices/)(甚至 Google 也有 [bash 风格指南](https://google.github.io/styleguide/shell.xml))。如果你刚刚开始使用 Bash，那里并不缺少好的选择(我强烈推荐[这个优秀的备忘单](https://devhints.io/bash))。

作为一名顾问，我经常看到为不同目的和环境编写的 Bash 脚本，以及经过不同阶段的知识衰退。

我想分享一些我发现在编写健壮的 Bash 脚本时最有帮助的指导原则，这些脚本可以跨平台运行，并且在几个月(或几年)后重新访问会很愉快。我已经把我所犯的错误的评论包括进去了，这样你就不会犯同样的错误了。

有四个主要提示:

*   告诉 Bash 在安全模式下运行
*   尝试使用选项的长格式
*   在变量周围使用引号
*   不要写程序

让我们详细地看一下每一个。

# 1)告诉 Bash 在安全模式下运行

这两个头都告诉 Bash 在以下情况下立即退出:

*   命令失败(`-e`或`-o errexit`)
*   使用了未定义的变量(`-u`或`-o nounset`

你可能以前见过这种情况(特别是如果你按照介绍中的链接)，所以这里有一些常见的问题:

如何将变量设置为空？

有了`nounset`，你可以使用`${VARIABLE_NAME:-}`，如果没有设置变量，它将安全地为空。您可以像这样在测试中使用它:

```
if [ -z "${VARIABLE_NAME:-}" ]; then
   # $VARIABLE_NAME is empty
else 
   # $VARIABLE_NAME is not empty
fi
```

**应该用** `**#!/bin/bash**` **还是** `**#!/usr/bin/env bash**` **？**

大部分导游会说`#!/usr/bin/env bash`更便携。以我的经验，现代环境(甚至可以执行 Bash 的 windows shells)都支持`#!/bin/bash`。

我个人更喜欢较短的版本，因为它在第一行自动包含了设置。这意味着你不会在`errexit`被设置之前意外添加代码。但是，如果我正在审查代码，我会接受其中任何一个。

**我不能两样都做吗？**

请注意，您不能同时使用这两种技术:

```
# THIS IS BROKEN
#!/usr/bin/env bash -eu
```

这是因为如果有人试图用`bash ./your-script.sh`直接执行脚本，它将不会设置选项，导致安全模式有时只能工作。

**`**set -o pipefail**`**呢？****

**使用`set -o errexit`的一个问题是，如果管道中的命令失败，它不会退出:**

```
# does not fail the script
some_failing_command | some_other_command
```

**您可以`set -o pipefail`解决这个问题，如果管道中的命令失败，让脚本失败。**

```
# Will fail the script
set -o pipefail
some_failing_command | some_other_command
```

**然而，这并不总是您想要的行为——例如，不匹配任何内容的`grep`会返回非零退出代码，如果在管道中使用，会使脚本失败。**

**因为`pipefail`并不总是你想要的，所以很难推荐一个明智的默认设置。**

**有些人推荐设置`pipefail`，把`|| true`放在你想一直通过的线上。**

```
# Will not fail the script
set -o pipefail
some_failing_command | some_other_command || true
```

**这里的问题是`some_other_command`中的失败是隐藏的，这几乎从来不是你想要的。**

**如果您需要大量管道，请仔细考虑失败案例。我推荐什么？**

**建议大家不要写太多管道。**

**![](img/d88f39e138ced6052e85e5ce25f2515d.png)**

**几乎太多的管道(照片由[塞缪尔·西亚尼帕](https://unsplash.com/@samthewam24?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄)**

# **2)尽量使用选项的长格式**

**注意，上面我们使用的是`set -o errexit`和`set -o nounset`的长形式，而不是`set -e`和`set -u`，这样对未来的读者来说，意思就更清楚了。**

**大多数 shell 脚本环境(以及许多更老的编程语言)会奖励用户记住神秘的选项序列。这可能会导致代码难以维护，并创建一种无益的团队文化，在这种文化中，人们会觉得自己很聪明或很有研究，而不是觉得自己是伟大的沟通者。一个团队成员说“看看这有多可读”总是比“看看这个聪明的一行程序”更好。**

**我们不能总是使用长格式选项——例如，`bash -o errexit`只适用于某些 bash 版本，所以如果我们想确保在脚本的最开始就设置安全模式选项，就必须使用短格式。**

**![](img/5f7eab6942789e0f7b8f3a148cb9e2dc.png)**

**你不需要这些工具，但它们放在你的桌子上会很酷(照片由[杰夫·谢尔登](https://unsplash.com/@ugmonk?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄)**

# **3)在变量周围使用引号**

**根据我的经验，脚本意外失败的最常见原因是路径名中的空格:**

```
# Don't do this
cp $FILE new-location# Do this
cp "$FILE" new-location
```

**在第一种情况下，文件名中的空格会将副本扩展为三个(或更多)参数的命令。第二种情况与预期的一样。**

**理想情况下，您的脚本不包含对目录或文件名的意见，引用您的参数意味着您的脚本不会假定文件名不包含空格。**

**我认为这是最重要的风格习惯，但当然，还有许多其他关于 Bash 风格的好意见。如果你想在你的脚本上有更深的林挺，我推荐优秀的 [shellcheck](https://www.shellcheck.net/) 实用程序。**

# **4)不要写程序**

**Bash 非常强大。您可以编写可重用的函数库(尽管 Bash 没有函数返回值，但是您可以使用[工作区](https://www.linuxjournal.com/content/return-values-bash-functions))，并且您可以创建复杂的逻辑来询问高度自动化的用户环境。**

**不要。**

**谷歌的风格指南建议[如果你的脚本超过 100 行就用 Python 重写](https://google.github.io/styleguide/shell.xml?showone=When_to_use_Shell#When_to_use_Shell)，我认为这是一个很好的指南。**

**Bash 不适合清晰、分离良好的代码，尤其是当您的逻辑变得复杂时。这可能很神秘——如果你在写剧本的时候不得不查找是使用`$@`还是`$#`,那么一些未来的读者也会这么做。关键是，没有简单的方法来为它编写测试。**

**我还建议不要有太多的 if/else 分支(尤其是当它们变成嵌套的时候)，并且不要使用一两个以上的函数。**

**如果你的脚本开始做很多事情，可能是时候用一种为做很多事情而设计的语言来写了。**

**![](img/8745ecb0b6ae853dfaaa429e5868b23e.png)**

**此外，尽可能在自然环境中编写 bash 脚本(图片由[伊利亚·安东内尔](https://unsplash.com/@elijahsad?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄)**

# **特殊情况**

## **何时改变这些指导方针？**

**你能做的最好的事情就是在离开自己的脚本好几个月后(或者第一次使用别人的脚本时)重新回到自己的脚本时，注意这些痛点。**

## **何时打破这些准则？**

**我的建议(对于任何一套指导方针)是遵循它们，除非你有充分的理由不这样做。这意味着你可以判断“一个好的理由”对你和你的团队意味着什么。**

**对我来说，不严格遵守这些准则的一个常见原因是在处理错误时——准则#5 的细节见下一篇文章:[给出有用的错误信息。](https://medium.com/@timothyjones_88921/helpful-errors-in-bash-scripts-c1e3c2c50bf8)**