# 负责任的程序员不会做的四件事

> 原文：<https://betterprogramming.pub/four-things-responsible-programmers-dont-do-88101e3b176>

## 让我们开始掌控我们的代码库

![](img/89007441b648893d0f370946d2a5727b.png)

阿尔文·马赫穆多夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

编程已经逐渐成为许多职业中的一项关键技能。随着它越来越受欢迎，越来越多的人乘风破浪，涌入 1 和 0 的世界。他们中的很大一部分人没有受过任何系统的编程培训——他们掌握的知识只够完成他们的工作。当然，一些积极自学的人可以在几年后取得巨大的进步，但其他人则停留在平庸的水平。

我们应该尊重前一类人，我想称他们为职业程序员，因为他们对自己的全部工作负责——本质上是代码。他们明白他们的代码代表了他们是什么样的人。

我并没有夸大代码库相对于编码者特征的重要性。我相信负责任的程序员不会做以下四件事。

# 1.给事物起不合逻辑的名字

在阅读别人的代码时，最不能容忍的事情就是变量、类、函数等等的不合逻辑的名字。对我来说，弄清楚像`var0`和`var1`这样的变量是什么意思一点都不好玩。一个函数被称为`doStepOne`是没有意义的。所有这些名字都有一个相同的问题——缺乏特异性。你可以在代码的不同部分有多个`var0`和`var1`，它们可能意味着完全不同的东西，导致歧义。

另一个问题是这些名字含糊不清或者不精确——缺乏清晰度。这里有一个简单的例子:假设您需要跟踪应用程序中加载的图像的宽度。您可以给出几个可能的名称，如下所示:

```
float w;
float width;
float imageWidth;
float imageWidthInPixels;
```

你认为哪一个最好？除非在非常局部受限的范围内使用，否则像`w`和`width`这样的名字可能是不明确的——它们可以指任何有大小信息的东西。大多数情况下，单位不重要时的`imageWidth`，或者单位重要时的`imageWidthInPixels`更好。您很少需要担心变量的长度，因为大多数现代 ide 都支持自动完成提示。

第三个问题是有些人不尊重命名惯例。每种语言都有自己独特的习惯，请遵循它们。您可以拥有自己的风格，但是遵循命名约定不仅会使您的代码连贯，还会使您的代码更具可读性和可理解性。比如，在大多数语言中，你不用小写来命名一个类，比如`employee_record`。相反，您使用`EmployeeRecord`来明确地表明您声明一个新类的意图。

# 2.编写笨拙的函数

程序员总是在谈论重构。程序员们似乎已经达成共识，我们应该一直重构我们的代码。但是重构是一个非常广泛的话题，可以包括几乎所有使代码库易于管理和维护的东西——算法、用户界面、文档和许多其他方面。然而，我想把重点放在一个特别的角度:重构函数。

在大多数情况下，我应用于函数重构的经验法则是保持函数小。在我自己的实践中，我很少编写超过 20 行的函数(我实际上并不跟踪我所编写的函数的代码行数，但是出于某种原因，当我写这篇文章时，我想到了这个数字！).如果你的函数大大超过这个数字，这是一个明确的信号，你可能要重构你的函数。一些可能的考虑包括:

*   通过创建更小的函数来分解大函数。当然，分解必须是合乎逻辑的，使每个较小的函数执行整个算法的特定任务或步骤。
*   如果几个函数计算彼此相关的输出值，请考虑创建一个捕获这些值的类。许多函数的计算可以移到类中，该类可以为这些计算实现单独的方法。

# 3.提交不带消息的代码

如今，可能没有这样的场景，在你完成代码后，没有人，包括你自己，会再去碰它。迟早有人，很可能是你自己，将不得不阅读你的代码，要么是为了调试，要么是为了提高性能的重构。经验丰富的程序员强调代码本身良好文档的重要性。此外，我们经常强调使用版本控制工具，比如 GitHub，来跟踪我们的代码版本。

当我们在一个非平凡的项目中工作时，我们通常在不同的分支中处理特定的方面。因此，我们需要不时地向分支机构提交我们自己的部分。有多少次你看到类似“一个更新”、“修复了一个 bug”或“写了另一个函数”这样的提交消息时感到烦恼

负责任的开发者绝对不应该这么做！你的提交信息简洁是没有问题的，但是清晰应该永远是首要的。例如，不要说“修复了一个错误”，你可以说“修复了一个错误，该错误计算了 getUserBalance 函数的输出值。”不要说“编写了另一个函数”，你可以说“编写了另一个在 UserAccount 类中检查用户身份验证的函数。”

提交消息的中心思想是通过提及最基本的组件来总结更新。通常情况下，使用一个没有任何歧义的肯定陈述是一个好主意。

# 4.不修复错误代码

你听说过破窗理论吗(维基百科链接:[https://en.wikipedia.org/wiki/Broken_windows_theory](https://en.wikipedia.org/wiki/Broken_windows_theory))？这里有一个来自维基百科页面的快速定义。本质上，这个想法是，如果一栋建筑有一些破碎的窗户，可能会发生故意破坏甚至更严重的犯罪，因为人们可能会认为这栋建筑没有得到维护——事情可能会变得越来越糟。

> **破窗理论**是一种[犯罪学](https://en.wikipedia.org/wiki/Criminology)理论，该理论认为[犯罪](https://en.wikipedia.org/wiki/Crime)、[反社会行为](https://en.wikipedia.org/wiki/Anti-social_behavior)、[社会秩序混乱](https://en.wikipedia.org/wiki/Civil_disorder)创造了一种[城市环境](https://en.wikipedia.org/wiki/Urban_area)，鼓励进一步的犯罪和混乱，包括严重犯罪。该理论认为，针对轻微犯罪的警务方法，如[故意破坏](https://en.wikipedia.org/wiki/Vandalism)、[游荡](https://en.wikipedia.org/wiki/Loitering)、[公共饮酒](https://en.wikipedia.org/wiki/Public_drinking)、[乱穿马路](https://en.wikipedia.org/wiki/Jaywalking)和[逃票](https://en.wikipedia.org/wiki/Fare_evasion)，有助于创造一种有序和合法的氛围，从而防止更严重的犯罪。
> 
> —维基百科

如果我们将这一理论应用到编码中，如果不修复“坏掉的窗口”,代码库会逐渐变得不可维护。更具体地说，代码库中破碎的窗口是坏代码。无论代码库是由您还是您的团队单独开发的，如果您遇到了糟糕的代码，请修复它或通知负责修复它的团队成员。

负责任的开发人员不应该让糟糕的代码不被修复。规则应该是——看到什么，说什么。我们必须始终管理我们的代码库。如果我们在那里留下一些糟糕的代码，我们团队的新成员可能会想当然地认为他们不必尽可能保持代码库的整洁，毕竟已经有很多糟糕的代码了。越来越多的坏代码积累在代码库中，直到无法维护——完全重写变得必要。这会有多痛苦和代价有多大？资深开发者看得出来！

# 结论

有责任心是一个职业程序员的核心特征。实践责任是开发干净且可维护的代码库的基础。如果我们这个行业的每个人都负起责任，我们的工作就会变得容易得多。