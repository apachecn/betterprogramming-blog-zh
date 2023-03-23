# 以下是如何不要在 JavaScript 上太差劲

> 原文：<https://betterprogramming.pub/js-reliable-fdea261012ee>

## 实话实说吧。很多 JavaScript 代码都很烂。改变它！

![](img/47818007303d4dba1bc21092d421d6cf.png)

[罗伯特·贝克](https://unsplash.com/@vegasphotog?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

JavaScript 是一股不可忽视的力量。它是世界上最广泛使用的编程语言。它的简单性和丰富的学习资源使它对初学者来说很容易上手。庞大的人才库使得 JavaScript 对各种规模的公司都有吸引力。工具和库的大型生态系统对于开发人员的生产力来说是一个真正的福音。用一种语言来管理前端和后端是一个巨大的好处，相同的技能可以在整个堆栈中使用。

## JavaScript 的力量是核能

JavaScript 提供了很多工具和选项，这很好！然而，令人痛苦的事实是，它几乎没有对开发人员施加任何限制。把 JavaScript 交给没有经验的人就像把一盒火柴和一罐汽油交给一个两岁的孩子…

JavaScript 的力量是核能——它可以用来为城市供电，也可以用来破坏。构建使用 JavaScript 的东西很容易。构建既**可靠**又**可维护**的软件则不然。

# 代码可靠性

![](img/f35ce5514b7f0ff8bed28acb9fe5e48f.png)

Jaromír Kavan 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

建造大坝时，工程师们首先关心的是可靠性。在没有任何计划或安全措施的情况下修建大坝是危险的。同样的道理也适用于造桥、飞机、汽车……只要是你能想到的。如果一辆车不安全、不可靠，那么这些花哨的功能都不重要——马力、排气音量，甚至内饰使用的皮革类型都不重要。

同样，每个软件开发人员的目标都是编写*可靠的*软件。如果代码有问题并且不可靠，其他的都不重要。编写可靠代码的最佳方式是什么？*简单*。简单是复杂的反义词。因此，作为软件开发人员，我们首要的责任应该是降低代码的复杂性。

有经验的开发人员和没有经验的开发人员的区别在于是否能写出可靠的软件。是的，可靠也包括可维护性——只有可维护的代码库才是可靠的。整篇文章的重点是编写可靠的代码。

虽然我是函数式编程的坚定信徒，但我不会鼓吹任何东西。相反，我将从函数式编程中获取一些有用的概念，并演示如何在 JavaScript 中应用它们。

我们真的需要软件可靠性吗？这由你决定。有些人会争辩说，软件只应该好到足以让客户继续使用它。我倾向于不同意。事实上，我坚信，如果软件不可靠且不可维护，其他一切都无关紧要。谁会买一辆不可靠的车，它会坏掉并随意加速？有多少人会使用一部一天会失去几次连接并随机重启的手机？可能不会太多。软件没什么不同。

# 内存不够了

![](img/615bfc90c226df9ca43c8aa04e690f58.png)

利亚姆·布里斯在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

我们如何开发可靠的软件？我们必须考虑可用内存的数量。任何开发人员都知道，我们应该将程序设计成内存高效的，并且永远不要消耗所有可用的 RAM。如果发生这种情况，那么内存交换将开始发生——任何不适合 RAM 的内容都将存储在硬盘上，并且所有运行的程序的性能都将开始下降。

这与编写可靠的软件有什么关系？人脑有自己的 RAM 版本，称为工作记忆。是的，我们的大脑是已知宇宙中最强大的机器，但它也有自己的局限性——在任何给定的时间，我们的工作记忆中只能容纳大约五条信息。

这直接转化为编程——简单的代码消耗更少的精神资源，使我们更有效率，并产生更可靠的软件。本文以及一些可用的 JavaScript 工具将帮助您实现这一目标！

# 初学者注意事项

在本文中，我将大量使用 ES6 的特性。在阅读之前，确保你熟悉 ES6。简单回顾一下:

# 工具作业

![](img/a3a89ea670cb08741c73dc111bf1d767.png)

照片由 [NeONBRAND](https://unsplash.com/@neonbrand?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

JavaScript 最大的优势之一是可用的工具。没有其他编程语言可以拥有如此庞大的工具和库生态系统。

我说我们应该使用这样的工具，尤其是 [ESLint](https://eslint.org/) ，可能也不会让任何人感到惊讶。ESLint 是一个静态代码分析工具。它是最重要的工具，允许我们发现代码库中的潜在问题，并确保代码库的高质量。最好的部分是，林挺是一个完全自动化的过程，可以用来防止低质量的代码进入代码库。

许多人很少使用 ESLint——他们只是启用一个预建的配置，如 [eslint-config-airbnb](https://www.npmjs.com/package/eslint-config-airbnb) ，并认为他们已经完成了。这是一种看似合理的方法，但不幸的是，它仅仅触及了 ESLint 所能提供的皮毛。JavaScript 是一种没有限制的语言。不适当的林挺设置会产生深远的后果。

是的，熟悉一种语言的所有特征是有用的。然而，一个熟练的开发人员也是知道什么特性*不*可以使用的人。JavaScript 是一种古老的语言，它背负着大量的包袱，并试图完成所有的工作。能够区分好的部分和坏的部分是很重要的。

# ESLint 配置

如果您想继续学习并利用本文中的一些建议，那么您可以如下设置 ESLint。我建议逐一熟悉这些建议，并将 ESLint 规则逐一纳入您的项目中。最初将它们配置为`warn`，一旦你适应了，你可以将一些规则转换为`error`。

在项目运行的根目录中:

然后在项目的根目录下创建一个`.eslintrc.yml`文件:

如果你使用的是 VSCode 这样的 IDE，一定要设置一个 ESLint [插件](https://github.com/microsoft/vscode-eslint)。

您也可以从命令行手动运行 ESLint:

```
npx eslint .
```

# 重构的重要性

![](img/dab77cdfbf4c42e007307fcc95825e8c.png)

[Jason Leung](https://unsplash.com/@ninjason?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/organize?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

重构是降低现有代码复杂性的过程。如果运用得当，它会成为我们对付可怕的技术债务怪兽的最佳武器。没有持续的重构，技术债务会不断积累，反过来会让开发人员更慢，更沮丧。技术债务是开发者精疲力竭的主要原因之一。

重构归结为清理现有代码的过程，同时确保代码仍然正确运行。重构被认为是软件开发中的良好实践，它应该是任何健康组织中开发过程的正常部分。

提醒一句，在进行任何重构之前，最好用自动化测试覆盖代码。很容易无意中破坏现有的功能，拥有一个全面的测试套件是限制任何潜在问题的好方法。

# 复杂性的最大来源

![](img/acdc379551a3a74bf5c8b2208091d13d.png)

莱昂内尔·费尔南德斯在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 的照片

我知道这听起来很奇怪，但是代码本身是复杂性的最大来源。事实上，[无代码](https://github.com/kelseyhightower/nocode)是编写安全可靠软件的最佳方式。坦率地说，这并不总是可能的，因此第二好的事情是减少代码量。更少的代码意味着更少的复杂性，就这么简单！更少的代码也意味着附着虫子的表面面积更小。甚至有一种说法，初级开发人员写代码，高级开发人员删代码。完全同意。

## 长文件

我们承认吧，人类很懒。懒惰是植入我们大脑的一种短期生存策略。它通过避免对我们的生存不重要的事情来帮助我们保存能量。

我们有些人有点懒，不太守纪律。人们不断地将越来越多的代码放入同一个文件中…如果文件的长度没有限制，那么这样的文件往往会无限增长。根据我的经验，超过 200 行代码的文件对人脑来说太多了，很难理解和维护。长文件也是一个更大问题的征兆——代码可能做得太多，这违反了单一责任原则。

如何解决这个问题？轻松点。只需将大文件分解成更小更细粒度的模块。

建议的 ESLint 配置:

```
rules:
  *max-lines:
  - warn
  - 200*
```

## 长函数

复杂性的另一个主要来源是长而复杂的函数。这种功能很难推理。他们通常有太多的职责，很难测试。

让我们考虑下面这个更新博客条目的`express.js`代码片段:

函数体有 38 行长，它做几件事:解析文章 id，查找现有的博客文章，验证用户输入，在输入无效的情况下返回验证错误，更新文章集合，并返回更新的博客文章。

显然，它可以被重构为几个更小的函数。由此产生的路由处理程序可能如下所示:

建议的 ESLint 配置:

```
rules:
  max-lines-per-function*:
  - warn
  - 20*
```

## 复杂函数

复杂函数与长函数密切相关，长函数总是比短函数更复杂。是什么让函数变得复杂？很多事情，但是容易解决的是嵌套回调和高圈复杂度。

嵌套回调经常导致[回调地狱](http://callbackhell.com/)。通过[许诺](https://nodejs.org/dist/latest-v8.x/docs/api/util.html#util_util_promisify_original)回调，然后使用异步等待，可以很容易地减轻这个问题。

下面是一个深度嵌套回调函数的例子:

**圈复杂度**

函数复杂性的另一个主要来源是圈复杂性。简而言之，它指的是任何给定函数中语句(逻辑)的数量。想想`if`语句、循环和 switch 语句。这种函数很难推理，它们的使用应该受到限制。这里有一个例子:

建议的 ESLint 配置:

```
rules:
  complexity:
  - warn
  - 5

  max-nested-callbacks:
  - warn
  - 2 max-depth:
  - warn
  - 3
```

减少代码量和复杂性的另一个重要方法是什么？声明性代码，稍后会详细介绍。

# 可变状态

![](img/8a0af1e6e0a1d6c337f5f734f117bc35.png)

阿列克谢·图伦科夫在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

什么是状态？简单地说，状态是存储在内存中的任何临时数据。想想对象中的变量或字段。状态本身是无害的。可变状态是软件复杂性的最大来源之一。尤其是在与面向对象结合使用时(稍后将详细介绍)。

## 人类大脑的局限性

为什么可变状态是一个如此大的问题？正如我前面说过的，人脑是已知宇宙中最强大的机器。然而，我们的大脑真的不擅长处理状态，因为在我们的工作记忆中，我们一次只能容纳大约五个项目。如果你只考虑代码做什么，而不考虑它在代码库中改变了什么变量，那么对一段代码进行推理就容易得多。

用可变状态编程是一种精神 juggling️.行为我不知道你怎么样，但我可能会玩两个球。给我三个或更多，我肯定会放弃他们。编码是一样的，一旦我放弃可变状态，我变得更有效率，我的代码变得更可靠。

## 可变状态的问题

让我们在实践中看看可变性是如何使我们的代码出现问题的:

这个错误非常微妙，但是通过改变函数参数，我们意外地修改了原始商品的价格。它应该保持为 10，但实际上，该值已更改为 13！

我们如何避免这样的问题？通过构造并返回一个新的对象(不变性):

请记住，使用 ES6 spread `…`操作符进行复制是浅层复制，而不是深层复制——它不会复制任何嵌套属性。例如，如果上面的`item`有类似于`item.seller.id`的东西，新项目的`seller`仍然会引用旧项目，这是不可取的。在 JavaScript 中处理不可变状态的其他更健壮的替代方法包括 [immutable.js](https://github.com/immutable-js/immutable-js) 和 [Ramda lenses](http://randycoulman.com/blog/2016/07/12/thinking-in-ramda-lenses/) 。我将在另一篇文章中讨论这些选项。

建议的 ESLint 配置:

```
rules:
  fp/no-mutation: warn
  no-param-reassign: warn
```

## 不要推动您的阵列

使用像`push`这样的方法进行数组变异也存在同样的问题:

我想你可能希望数组`b`保持不变？如果我们创建一个新的数组而不是调用`push`，这个错误就可以很容易地避免。

这样的问题可以通过构建新的阵列来轻松避免:

```
const newArray = [...a, 'microsoft'];
```

## 非决定论

非确定性是一个奇特的术语，它简单地描述了在给定相同输入的情况下，程序无法产生相同的输出。如果你觉得这听起来不好，那是因为它一点也不好。您可能会认为是`2+2==4`，但对于非确定性程序来说，情况并非总是如此。二加二通常等于四，但有时也可能等于三、五，甚至可能等于 1004。

可变状态本质上是不确定的，这使得代码容易出现不确定性(如上所示)。具有讽刺意味的是，非确定性被普遍认为是编程中不可取的，然而最流行的编程范式(OOP 和命令式)天生就是非确定性的，因为它们依赖于可变状态。

## 不变

如果可变性并不总是最好的选择，那么有哪些选择呢？当然是不变性！利用不变性是一个非常好的实践，现在被许多人所鼓励。是的，有些人可能不同意，并说可变状态很棒(Rust 粉丝？).我可以肯定地说一件事——可变性并不会使代码库更可靠。

在本文中，我不会深入探讨不变性。这是一个很大的主题，稍后我可能会用一整篇文章来讨论不变性。

建议的 ESLint 配置:

```
rules:
  fp/no-mutating-assign: warn
  fp/no-mutating-methods: warn
  fp/no-mutation: warn
```

# 避免使用 Let 关键字

![](img/105f362bf3d36424824c6c5883818e5e.png)

罗布森·哈苏卡米·摩根在 [Unsplash](https://unsplash.com/search/photos/relax?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

是的，`var`永远不应该在 JavaScript 中用来声明变量，我这样说不会让任何人感到惊讶。然而，你可能会惊讶地发现应该避免使用关键字`let`。用`let`声明的变量可以被重新分配，这使得对代码的推理更加困难。到目前为止，我们在本文中讨论的许多不好的实践都遇到了人类 RAM(工作内存)的限制，使用`let`关键字也不例外。当用`let`关键字编程时，我们必须记住所有的副作用和潜在的边缘情况。无意中，我们可能会给变量赋值不正确，浪费调试时间。

这尤其适用于单元测试。在多个测试之间共享可变状态是灾难性的，因为大多数测试运行者并行运行测试。

`let`关键字的替代方法有哪些？当然是`const`这个关键词！尽管它不保证不变性，但通过不允许重新分配，它使代码更容易推理。老实说，你并不真的需要`let`——在大多数情况下，将值重新分配给变量的代码可以提取到一个单独的函数中。让我们看一个例子:

同样的例子被提取到一个函数中:

不能使用`let`关键字进行重新赋值起初听起来可能很困难，但是这会使你的代码不那么复杂，可读性更好。我已经很久没有使用过`let`这个关键词了，从来没有错过。

养成不用`let`关键字编程的习惯有一个很好的副作用，那就是让你更加自律。这将迫使你将代码分解成更小更易管理的功能。反过来，这将使您的功能更加集中，将实施更好的关注点分离，也将使代码库更具可读性和可维护性。

建议的 ESLint 配置:

```
rules:
  fp/no-let: warn
```

# 面向对象编程

> *“Java 是自 MS-DOS 以来计算领域发生的最令人苦恼的事情。”*
> 
> *-艾伦·凯，面向对象编程的* [这一节是完全可选的，如果你认为在开发软件时使用 OOP 是必须的，那么可以随意跳过这一节。谢了。](http://www.cc.gatech.edu/fac/mark.guzdial/squeak/oopsla.html#、JavaScript、TypeScript 和其他 OOP 语言中的局限性。我不是在批评适当的面向对象程序设计(例如 SmallTalk)。</p><p id=)
> 
> ## [优秀的程序员与糟糕的程序员](http://www.cc.gatech.edu/fac/mark.guzdial/squeak/oopsla.html#、JavaScript、TypeScript 和其他 OOP 语言中的局限性。我不是在批评适当的面向对象程序设计(例如 SmallTalk)。</p><p id=)
> 
> [好的程序员写出好的代码，坏的程序员写出坏的代码，无论编程模式如何。然而，编程范例应该约束糟糕的程序员不要做太多的破坏。当然，这不是你，因为你已经在阅读这篇文章，并在努力学习。糟糕的程序员从来没有时间去学习，他们只会疯狂地不停地按键盘上的按钮。不管你喜不喜欢，你都会和糟糕的程序员一起工作，有些人真的非常非常糟糕。不幸的是，OOP 没有足够的约束来防止糟糕的程序员造成太大的破坏。](http://www.cc.gatech.edu/fac/mark.guzdial/squeak/oopsla.html#、JavaScript、TypeScript 和其他 OOP 语言中的局限性。我不是在批评适当的面向对象程序设计(例如 SmallTalk)。</p><p id=)
> 
> [为什么最初会发明 OOP？它旨在帮助组织过程代码库。具有讽刺意味的是，OOP 本应降低复杂性，然而，它所提供的工具似乎只是增加了复杂性。](http://www.cc.gatech.edu/fac/mark.guzdial/squeak/oopsla.html#、JavaScript、TypeScript 和其他 OOP 语言中的局限性。我不是在批评适当的面向对象程序设计(例如 SmallTalk)。</p><p id=)
> 
> ## [OOP 非确定性](http://www.cc.gatech.edu/fac/mark.guzdial/squeak/oopsla.html#、JavaScript、TypeScript 和其他 OOP 语言中的局限性。我不是在批评适当的面向对象程序设计(例如 SmallTalk)。</p><p id=)
> 
> [OOP 代码倾向于非确定性——它严重依赖可变状态。函数式编程保证了在给定相同输入的情况下，我们将总是得到相同的输出。OOP 不能保证太多，这使得对代码的推理更加困难。](http://www.cc.gatech.edu/fac/mark.guzdial/squeak/oopsla.html#、JavaScript、TypeScript 和其他 OOP 语言中的局限性。我不是在批评适当的面向对象程序设计(例如 SmallTalk)。</p><p id=)
> 
> [如前所述，在非确定性程序中，`2+2`或`calculator.Add(2, 2)`的输出通常等于 4，但有时也可能等于 3、5，甚至 1004。`Calculator`对象的依赖性可能会以微妙但深刻的方式改变计算的结果。当涉及到并发性时，这些问题变得更加明显。](http://www.cc.gatech.edu/fac/mark.guzdial/squeak/oopsla.html#、JavaScript、TypeScript 和其他 OOP 语言中的局限性。我不是在批评适当的面向对象程序设计(例如 SmallTalk)。</p><p id=)
> 
> ## [共享可变状态](http://www.cc.gatech.edu/fac/mark.guzdial/squeak/oopsla.html#、JavaScript、TypeScript 和其他 OOP 语言中的局限性。我不是在批评适当的面向对象程序设计(例如 SmallTalk)。</p><p id=)
> 
> > [*“我认为大型面向对象程序在构建这种包含可变对象的大型对象图时，会面临日益增加的复杂性。你知道，试图理解并记住当你调用一个方法时会发生什么，副作用会是什么。”*](http://www.cc.gatech.edu/fac/mark.guzdial/squeak/oopsla.html#、JavaScript、TypeScript 和其他 OOP 语言中的局限性。我不是在批评适当的面向对象程序设计(例如 SmallTalk)。</p><p id=)
> > 
> > [*—*](http://www.cc.gatech.edu/fac/mark.guzdial/squeak/oopsla.html#、JavaScript、TypeScript 和其他 OOP 语言中的局限性。我不是在批评适当的面向对象程序设计(例如 SmallTalk)。</p><p id=)[*Rich Hickey*](http://www.se-radio.net/2010/03/episode-158-rich-hickey-on-clojure/?source=post_page---------------------------)*，Clojure* 的创建者
> 
> 可变状态是困难的。不幸的是，OOP 通过引用(而不是通过值)共享可变状态进一步加剧了这个问题。这意味着几乎任何东西都可以改变给定对象的状态。开发人员必须记住当前对象与之交互的每个对象的状态。这很快就触及了人脑的极限，因为在任何给定的时间，我们的工作记忆中只能容纳大约五条信息。对人脑来说，对如此复杂的易变对象图进行推理是一项不可能完成的任务。它耗尽了宝贵而有限的认知资源，并不可避免地导致大量的缺陷。
> 
> 是的，共享对可变对象的引用是为了提高效率而做出的权衡，在几十年前可能很重要。硬件已经取得了巨大的进步，我们现在应该更担心开发人员的效率，而不是代码的效率。即使这样，使用现代工具，不变性对性能几乎没有任何影响。
> 
> OOP 宣扬全球国家是万恶之源。然而，具有讽刺意味的是，OOP 程序大多是一大块全局状态(因为一切都是可变的，并且通过引用共享)。
> 
> 德米特里定律并不十分有用——共享可变状态仍然是共享可变状态，无论你如何访问或改变那个状态。它只是掩盖了问题。领域驱动设计？这是一个有用的设计方法，它有助于降低复杂性。然而，它仍然没有解决非决定论的根本问题。
> 
> ## 信噪比
> 
> 过去，许多人一直关注 OOP 程序的不确定性所带来的复杂性。他们提出了大量的设计模式，试图解决这些问题。不幸的是，这只会进一步掩盖根本问题，并引入更多不必要的复杂性。
> 
> 正如我前面所说，代码本身是复杂性的最大来源，少代码总是比多代码好。OOP 程序通常携带大量样板代码，以及设计模式形式的“创可贴”，这对信噪比有不利影响。这意味着代码变得更加冗长，并且要理解程序的初衷变得更加困难。不幸的是，这会使代码库变得更加复杂，从而降低代码库的可靠性。
> 
> 在本文中，我不打算深入探讨使用面向对象编程的缺点。尽管可能有数百万人相信它，但我坚信现代 OOP 是软件复杂性的最大来源之一。是的，有一些用 OOP 构建的成功项目，但是，这并不意味着这些项目不会遭受不必要的复杂性。
> 
> JavaScript 中的 OOP 尤其糟糕，因为这种语言缺乏静态类型检查、泛型和接口。JavaScript 中的`this`关键字相当不靠谱。
> 
> OOP 的复杂性无疑是对大脑的一次很好的锻炼。然而，如果我们的目标是编写可靠的软件，那么我们应该努力降低复杂性，这在理想情况下意味着避免 OOP。如果你有兴趣了解更多，一定要看看我的另一篇文章[面向对象编程——万亿美元的灾难](https://medium.com/@ilyasz/object-oriented-programming-the-trillion-dollar-disaster-️-92a4b666c7c7)。

# 这个关键字

![](img/f32db2d34f8a2f46944194a8531cf81d.png)

奥斯汀·尼尔在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

`this`关键字的行为始终不一致。它很挑剔，在不同的上下文中可能意味着完全不同的东西。它的行为甚至取决于谁调用了给定的函数。使用`this`关键字经常会导致难以调试的微妙而奇怪的错误。

是的，问求职者可能是一个有趣的面试问题，但是对关键字`this`的了解并不能说明什么。只知道候选人花了几个小时学习最常见的 JavaScript 面试问题。

如果给我一段使用`this`关键字的复杂代码，我会怎么回答？作为一个加拿大人，我会说—“*对不起……我不知道*”。真实世界的代码不应该容易出错。它应该是可读的，而不是棘手的。`this`是一个明显的语言设计缺陷，不应该使用。

建议的 ESLint 配置:

```
rules:
  fp/no-this: warn
```

# 声明代码

![](img/b01c2c33269175110e91e6affc81fcab.png)

肖恩·斯特拉顿在 [Unsplash](https://unsplash.com/photos/FO7bKvgETgQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

声明性代码是当今的一个流行术语，但它真正的含义是什么呢？有什么好的吗？让我们找出答案。

如果您从事编程已经有一段时间了，那么很可能您一直在使用命令式编程风格，它描述了一组实现预期结果的步骤。另一方面，声明式风格描述的是期望的结果，而不是具体的指令。

常用的声明性语言的一些例子是 SQL 和 HTML。甚至还有 JSX 在反应过来！

我们不会通过指定具体的步骤来告诉数据库*如何* 获取我们的数据。我们用 SQL 来描述*什么* 取而代之:

```
SELECT * FROM Users WHERE Country='USA';
```

这大致可以用命令式 JavaScript 来表示:

或者在声明性 JavaScript 中，使用[实验性管道操作符](https://github.com/tc39/proposal-pipeline-operator):

你喜欢什么方法？对我来说，第二个似乎更干净，可读性更强。

## 更喜欢表达而不是陈述

如果我们的目标是编写声明性代码，表达式应该优先于语句。表达式总是返回值，而语句用于执行操作，不返回任何结果。这在函数式编程中也被称为“副作用”。顺便说一下，之前讨论的状态突变也是一种副作用。

有哪些常用的说法？想想`if`、`return`、`switch`、`for`、`while`。

让我们看一个简单的例子:

这可以很容易地重写为三元表达式(这是声明性的):

如果返回语句是 lambda 函数中唯一的东西，那么 JavaScript 也允许我们去掉 lambda 语句:

函数体从六行代码减少到一行代码。声明性代码超能力！

使用语句的其他缺点是什么？它们引起副作用和突变，容易产生非决定论。这会降低代码的可读性和可靠性。重新排序语句是不安全的，因为它们依赖于它们的使用顺序。语句(包括循环)很难并行化，因为它们会在其作用域之外改变状态。由于复杂性增加，使用语句意味着额外的精神负担。

另一方面，表达式可以安全地重新排序，不会产生副作用，易于并行化。

## 声明式编程需要努力

声明式编程不是一朝一夕就能学会的。特别是考虑到大多数人主要被教授命令式编程。声明式编程需要训练和学习以一种全新的方式思考。如何学习声明式编程？一个好的第一步是学习在没有可变状态的情况下编程——不使用`let`关键字，不改变状态。我可以肯定地说，如果你尝试一下声明式编程，你会惊奇地发现你的代码会变得多么优雅。

建议的 ESLint 配置:

```
rules:
  fp/no-let: warn
  fp/no-loops: warn
  fp/no-mutating-assign: warn
  fp/no-mutating-methods: warn
  fp/no-mutation: warn
  fp/no-delete: warn
```

# 避免向函数传递多个参数

![](img/59bb938fc2b905db187e618fedf6fc12.png)

由 [chuttersnap](https://unsplash.com/@chuttersnap?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/speed?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

JavaScript 不是一种静态类型的语言，没有办法保证使用正确和预期的参数调用函数。ES6 为表带来了许多很棒的特性，包括析构对象，这也可以用于函数参数。

你觉得下面的代码片段直观吗？你能马上说出参数是什么吗？我不能！

下面这个例子怎么样？

我认为后者比前者更具可读性。这尤其适用于从不同模块发出的函数调用。使用参数对象还有一个好处，就是使参数的顺序变得无关紧要。

建议的 ESLint 配置:

```
rules:
  max-params:
  - warn
  - 2
```

# 更喜欢从函数返回对象

下面的代码片段告诉了你多少关于函数签名的信息？它返回什么？它是否返回用户对象、用户 id、操作状态？不了解周围的环境，很难说。

从函数中返回一个对象使开发人员的意图非常清楚，代码的可读性也大大提高:

# 控制有异常的执行流

![](img/85b4632bde329ed4618bacdd3cc48dcc.png)

照片由[克里斯托夫·恩斯特伦](https://unsplash.com/@christoffere?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/crash?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

当您在表单中输入了无效输入时，看到内部 500 server 错误，您感觉如何？如何处理不给出任何细节的 API，而是抛出 500 个错误？我相信我们所有人都遇到过这样的问题，这种经历很可能并不愉快。

尽管我们被教导在意外发生时抛出异常，但这并不是处理错误的最佳方式。我们来看看为什么。

## 异常中断类型安全

异常会破坏类型安全，即使在静态类型语言中也是如此。根据它的签名，函数`fetchUser(id: number): User`应该返回一个用户。函数签名中没有说如果找不到用户就会抛出异常。如果预计会出现异常，那么更合适的函数签名应该是:`fetchUser(...): User|throws UserNotFoundError`。当然，这样的语法是无效的，不管是什么语言。

对抛出异常的程序进行推理变得很困难——人们可能永远不知道一个函数是否会抛出异常。是的，我们可以将每一个函数调用都包装在一个 try/catch 块中，但是这似乎不切实际，并且会严重影响代码的可读性。

## 异常中断函数合成

异常使得实际上不可能利用函数组合。在下面的例子中，如果找不到一篇博客文章，服务器将返回 500 内部服务器错误。

如果一个帖子被删除了，但是由于一些不为人知的错误，用户仍然试图访问这个帖子，该怎么办？这将大大降低用户体验。

## 元组作为错误处理的替代方式

无需深入函数式编程，处理错误的一个简单方法是返回一个包含结果和错误的元组，而不是抛出异常。是的，JavaScript 不支持元组，但是可以使用`[error, result]`形式的双值数组轻松模拟元组。顺便说一下，这也是 [Go](https://blog.golang.org/error-handling-and-go) 中错误处理的默认方法:

## 有时候，例外是好的

异常仍然在代码库中占有一席之地。根据经验，您应该问自己一个问题—我希望程序崩溃吗？任何抛出的异常都有可能导致整个进程停止。即使我们认为已经仔细考虑了所有潜在的边缘情况，这些异常仍然是不安全的，并且会导致程序在将来的某个时候崩溃。仅当您确实希望程序崩溃时才抛出异常，例如，由于开发人员错误或数据库连接失败。

异常被称为异常是有原因的。它们应该只在发生异常情况时使用，并且程序除了崩溃别无选择。抛出和捕获异常并不是控制执行流的好方法。我们应该只在发生不可恢复的错误时才抛出异常。例如，无效的用户输入不在其中。

## 让它崩溃——避免捕捉异常

这给我们带来了错误处理的终极规则——避免捕捉异常。没错——如果我们希望程序崩溃，我们可以抛出错误，但是我们永远不应该捕捉这样的错误。事实上，这是 Haskell 和 Elixir 等函数式语言推荐的方法。

唯一的例外是使用第三方 API。即使这样，最好还是利用一个帮助函数来包装函数，返回一个`[error, result]`元组。为此，你可以使用像[安全器](https://www.npmjs.com/package/saferr)这样的工具。我将在下一节部分函数应用程序中更详细地介绍这种包装器。

问问你自己——谁对这个错误负责？如果这是用户，那么应该优雅地处理错误。我们希望向用户显示一条好消息，而不是向他们展示一个 500 内部服务器错误。

不幸的是，没有`no-try-catch` ESLint 规则。它最接近的邻居是`no-throw`规则。确保在异常情况下，当您预计程序会崩溃时，您负责任地抛出错误。

建议的 ESLint 配置:

```
rules:
  fp/no-throw: warn
```

# 部分功能应用

![](img/fb761628db6717933e76959f9e1f542d.png)

照片由[本](https://unsplash.com/@b_sen?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/divided?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

部分功能应用可能是有史以来发明的最好的代码共享机制之一。它将 OOP 依赖注入打得落花流水。您可以在代码中注入依赖项，而无需求助于所有典型的 OOP 样板文件。

下面的例子包装了因抛出异常而臭名昭著的 [Axios](https://github.com/axios/axios) 库(而不是返回失败的响应)。使用这样的库是不必要的困难，尤其是在使用 async/await 时。

在下面的例子中，我们利用 currying 和部分函数应用程序使一个不安全的函数变得安全。

注意，`safeApiCall`函数被写成`func = (params) => (data) => {...}`。这是函数式编程中的一种常见技术，称为 currying，它总是与部分函数应用程序密切相关。这意味着当用`params`调用`func`函数时，它返回另一个实际执行任务的函数。换句话说，该功能部分应用了`params`。

也可以写成:

```
const func = (params) => (
   (data) => {...}
);
```

注意，依赖项(`params`)作为第一个参数传递，实际数据作为第二个参数传递。

为了使事情变得更简单，您可以使用 [saferr](https://www.npmjs.com/package/saferr) npm 包，它也支持承诺和 async/await:

# 一些小技巧

![](img/a6456d921e06b50e1d0fc1c11b43de67.png)

照片由 [Pierrick VAN-TROOST](https://unsplash.com/@vantroostpierrick?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

这里有一些小而方便的技巧。它们不一定会使代码更可靠，但可以让我们的生活变得更轻松。有些广为人知，有些则不然。

## 一点类型安全

是的，JavaScript 不是静态类型语言。然而，我们可以利用一个小技巧，通过根据需要标记函数参数来使我们的代码更加健壮。每当没有传入所需的值时，下面的代码将引发错误。请注意，它不会为空值工作，但仍然是一个伟大的防范未定义的值。

## 短路条件和评估

短路条件广为人知，对于访问嵌套对象中的值很有用。

如果某个值为假，短路评估有助于提供替代值:

```
const userCity = getUserCity(user) || "Detroit";
```

## 邦邦！！

两次否定一个值是将任何值转换为布尔值的好方法。请注意，任何 falsey 值都将被转换为`false`，这可能并不总是您想要的。这不应该用于数字，因为`0`也会被转换成`false`。

## 使用就地日志进行调试

我们可以利用短路和`console.log`的结果为 falsey 的事实来调试功能代码，甚至包括 React 组件:

# 下一步是什么？

![](img/28cb236906af52bc76dfd722f7cbd6d5.png)

照片由[马克西米利安·魏斯贝克尔](https://unsplash.com/@maximilianweisbecker?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/ship?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

你真的需要代码可靠性吗？这由你决定。你的组织把开发者的生产力等同于完成的 JIRA 故事的数量吗？你是否在一个只看重交付的特性数量的特性工厂中工作？希望不会，但如果他们会，那么你可能会考虑寻找一个更好的工作地点…

如果您试图一次性应用本文中的所有内容，这可能会让人不知所措。把这篇文章收藏起来，并不时地回头看看。每次，拿走一件你会刻意关注的事情。并启用相关的 ESLint 规则，为您的旅程提供帮助。

祝你好运！