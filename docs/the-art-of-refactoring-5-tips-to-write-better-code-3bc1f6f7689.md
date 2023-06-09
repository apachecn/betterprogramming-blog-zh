# 重构的艺术:写出更好代码的 5 个技巧

> 原文：<https://betterprogramming.pub/the-art-of-refactoring-5-tips-to-write-better-code-3bc1f6f7689>

## 糟糕的代码是有代价的，写好的代码并没有那么复杂

![](img/a6b4e773aafff0e0888942ef3a9037fb.png)

lloorraa via [need.pix](https://www.needpix.com/photo/download/1785916/way-winter-snow-landscape-travel-forest-snow-cold-frost-frozen)

糟糕的代码是有用的。我们都知道这一点。开发人员多年来一直在编写代码，却从来没有考虑过他们做得是否正确。可以理解，不是吗？毕竟，我们已经不得不应对跟上行业和工作要求的压力。

答案是否定的。编写糟糕的代码是有代价的。你有没有遇到过这样的问题:几个星期后你不理解你自己的代码，而不得不花几个小时，甚至几天的时间去弄清楚到底发生了什么？

这个(极其)常见问题的解决方案是让你的代码尽可能的清晰和。我甚至可以说，即使是非技术人员也应该理解您的代码。是时候放下我们的借口，提高你的代码质量了。

编写干净的代码并不复杂。本教程将向您展示五种简单的技术来改进您的代码，并提供实际的例子:

1.  去掉 switch 语句。
2.  让你的条件句具有描述性。
3.  使用 guard 子句避免嵌套的 if 语句。
4.  避免代码重复。
5.  函数应该只做一件事。

# 1.去掉 Switch 语句

我们通常使用 switch 语句来避免大型 if-else if 语句。然而，switch 语句非常冗长，难以维护，甚至更难调试。它们弄乱了我们的代码，而且，以我的拙见，有一个奇怪的，不舒服的语法。当添加更多的 case 时，我们必须手动添加每个 case 和 break 语句，这很容易出错。

让我们来看一个 switch 语句的例子:

交换语句

假设我们需要向 switch 语句中添加更多的事例。我们需要编写的代码量相当可观。我们很可能会以复制粘贴代码而告终，我们都知道这是如何结束的。

那么，我们如何避免 switch 语句呢？通过使用对象文本。对象文字简单，易于编写、读取和维护。我们都习惯于在 JavaScript 中处理对象，语法比 switch 语句要新鲜得多。这里有一个例子:

用对象文字替换开关

如您所见，我们可以通过使用 **||** 操作符来添加默认值。如果在 pokemon 对象中找不到该类型，`getPokemon` 函数将返回 Mew 作为默认值。

注意:你可能已经注意到了，我们在函数外部声明了 pokemon 对象，而不是在函数内部。我们这样做是为了防止每次执行函数时都创建它。

我们也可以使用地图来达到同样的效果。映射是键值对的集合，就像对象一样。区别在于 map 允许任何类型的键，而 objects 只允许字符串作为键。此外，map 还有一系列有趣的属性和方法。你可以在这里阅读更多关于[地图结构的信息](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)。

以下是如何使用地图:

用 Map 替换开关

正如您所看到的，当我们用对象文字或映射替换 switch 语句时，我们的代码看起来更加简洁明了。

# 2.让你的条件句具有描述性

编写代码时，条件语句是绝对必要的。然而，它们会很快失控，最终变得让人无法理解。这导致要么不得不写注释解释语句做什么，要么不得不花费宝贵的时间追溯我们自己的代码来理解发生了什么。这很糟糕。

看一下下面的陈述:

复杂条件语句

如果我们只看前面函数中 If 语句内部的代码，就很难理解发生了什么。我们的代码不清晰，不清晰的代码只会导致技术债务、错误和严重的问题。

我们如何提高我们的条件？通过**将它提取成一个函数。方法如下:**

将条件提取到函数中

通过将条件提取到具有描述性名称`isGameLost()`的函数中，我们的`checkGameStatus`函数现在一目了然。为什么？因为我们的代号是告知性的。它告诉我们正在发生什么，这是我们应该永远为之奋斗的。

# 3.使用 Guard 子句避免嵌套的 If 语句

嵌套的 if 语句是我们在代码中遇到的最糟糕的事情之一。我见过十层深的嵌套 if。相信我，当我告诉你，能够完全掌握代码中发生的事情绝对是一场噩梦。下面是一个嵌套 if 语句的例子(虽然只有三层——我不是怪物):

嵌套 if 语句

你可能需要花几分钟，上下阅读，来理解函数的流程。嵌套的 if 语句很难阅读和理解。那么我们如何摆脱讨厌的嵌套 if 语句呢？通过颠倒逻辑和使用我们所谓的保护条款。

> 在计算机编程中，*保护*是一个布尔表达式，如果*程序*的执行要在所讨论的分支中继续，则该表达式的值必须为真— [维基百科](https://en.wikipedia.org/wiki/Guard_(computer_science))

通过反转我们函数的逻辑，将导致提前退出的条件放在函数的开始处，它们将充当守卫，只有当所有条件都满足时，才允许我们的函数继续执行。这样，我们可以避免 else 语句。下面是如何重构我们之前的函数以使用 guard 子句:

使用守卫进行重构

如您所见，代码更加简洁，也更容易理解。我们可以简单地通过向下阅读，跟随函数的自然流程，来了解函数做了什么，不像以前，我们必须上下阅读。

# 4.避免代码重复

复制代码总是以失败告终。它会导致这样的情况:“我在这里修复了这个 bug，但是忘记在那里做了”或者“我需要做一个改变/添加一个新特性，并且必须在五个不同的地方做。”

正如[干](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) (不要重复自己)原理所说:

在一个系统中，每一个知识或逻辑都必须有一个单一的、明确的表示。

因此，拥有更少的代码是好的:它节省了我们的时间和精力，更容易维护，并且减少了 bug 出现的机会。

那么，我们如何去除重复的代码呢？答案并不总是简单的，但是将逻辑提取到函数/变量中通常就可以了。让我们来看看下面的代码，这是我在重构一个应用程序时遇到的:

重复代码的示例

您可能已经注意到，for 循环在两个函数**、**中完全相同，除了一个小细节:我们想要的新闻类型，要么是 JavaScript，要么是 Rust news。为了避免这种重复，我们可以将 for 循环提取到函数**、**中，然后从`getJavascriptNews`、*、*、`getRustNews`、*、*和`getGolangNews`函数中调用它们。方法如下:

将 for 循环提取到`getNewsContent`函数中后，我们的`getJavaScriptNews` *、* `getRustNews` *、*、`getGolangNews`函数都变成了简单明了的小程序。

## 进一步重构

然而，您是否意识到，除了我们传递给`getNewsContent`函数的类型字符串之外，这两个函数又一次完全相同？这是我们重构代码时经常发生的事情。通常情况下，一个变化会导致另一个变化，以此类推，直到重构后的代码只有原来的一半大。让您的代码告诉您它需要什么:

进一步重构

我们的`getJavaScriptNews`*`getRustNews`*`getGolangNews`函数去哪里了？我们用一个`getNews`函数代替它们，该函数接收新闻类型作为参数。这样，无论我们添加多少类型的新闻，我们总是使用相同的功能。这被称为抽象，允许我们重用函数**、**，因此非常有用。抽象是我在代码中最常用的技术之一。**

## **好处:使用 ES6 特性使 for 循环更具可读性**

**这是最后一次重构，我发誓。**

**For 循环不是精确可读的。随着 ES6 数组函数的引入，我们可以在 95%的时间里避免使用它们。在我们的例子中，我们可以使用`Array.filter`结合`Array.map`来替代原来的循环:**

**使用 Array.filter 和 Array.map 替换循环**

*   **使用`Array.filter`，我们只返回其类型等于作为参数传递的类型的元素。**
*   **使用`Array.map`，我们只返回项目*或*对象的内容**或**属性，而不是整个项目。**

**恭喜你，经过三次简单的重构，我们最初的三个功能减少到了两个，这就更容易理解和维护了。此外，通过使用抽象，我们使得`getNews`函数可重用。**

# **5.函数应该只做一件事**

**函数应该只做一件事。做多件事的函数是万恶之源，也是我们在代码中遇到的最糟糕的事情之一(还有嵌套的 if)。它们很乱，使我们的代码难以理解。下面是一个真实应用中复杂函数的例子:**

**庞大、复杂、令人困惑的函数**

**注意:因为这个例子不需要事件监听器的处理程序，所以我选择删除它们。**

**正如你所看到的，很难理解里面发生了什么。如果出现任何错误，找到并修复它们将会非常困难。怎样才能提高我们的`startProgram` 功能？通过将公共逻辑提取到函数中。方法如下:**

**将逻辑提取到函数中**

**让我们来看看对`startProgram`函数所做的更改:**

**首先，我们通过使用一个 guard 子句去掉了 if-else 语句。然后我们将启动数据库所需的逻辑提取到一个`initDatabase`函数中，并将事件监听器添加到一个`setListeners`函数中。**

**打印员工名单的逻辑稍微复杂一些，所以我们创建了三个函数:`printEmployeeList`、*、*、`getEmployeeList`。**

**`getEmployeeList`负责向`employeeList.json` 发出 GET 请求，并返回`json`格式的响应。**

**然后由`printEmployeeList`函数调用，该函数获取雇员列表并将其传递给 `*formatEmployeeList*` 函数，后者格式化并返回该列表。然后打印该列表。**

**如您所见，每个函数只负责做一件事。**

**我们仍然可以对函数进行一些修改，老实说，应用程序正在请求将视图从控制器中分离出来，但是总的来说，我们的`startProgram`函数现在提供了更多的信息，理解它的功能绝对没有困难。如果我们必须在几个月之后回到这段代码，我们一点问题都没有。**

# **结论**

**程序员是唯一负责编写优质代码的人。我们都应该养成从第一行就写好代码的习惯。编写干净的代码并不复杂，这样做对你和你的同事都有帮助。**

**通过应用本教程中展示的五个简单的技巧，您的代码质量将会大大提高，您的生产率也会提高。**

**如果你有任何问题，不要犹豫地问。感谢您的阅读。**