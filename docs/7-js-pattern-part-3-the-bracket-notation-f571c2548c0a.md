# 7 JavaScript 模式第 3 部分:括号符号

> 原文：<https://betterprogramming.pub/7-js-pattern-part-3-the-bracket-notation-f571c2548c0a>

## 这是由七部分组成的系列文章中的第三篇，介绍了所有初级开发人员都应该知道的 JavaScript 模式

![](img/f792571a50f6a45a102ddc566f186894.png)

如果您想知道如何在 JavaScript 中用字符串访问对象属性，那么这篇文章就是为您准备的！括号符号是最简单、最强大和最危险的 JS 模式之一。MDN 文档可以在[这里](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Property_Accessors#Bracket_notation)找到。

本周不是关于一个需要复杂帖子的古怪 JS 特性。我们将讨论一些有趣的东西:括号符号。这是大多数面向对象语言(如 Ruby 和 Python)的一个特性。它既强大又危险，但这并不难理解。我们将重点给出真实世界的例子，**，然后我们将看到如果你不小心的话会有多危险。**让我们投入其中吧！

# 括号符号是什么样子的？

考虑这个数组:

```
const array = ["lol", "lmao", "GG", "GLHF"]
```

您可以从该数组中检索一个项，该项的索引为:

```
array[3]
// expects: "GLHF"
```

好，现在考虑这个物体:

```
const obj1 = {prop1: "Trololo", foo: "bar"}
```

你已经知道可以访问像 ***obj1.foo*** 这样的对象属性，对吗？这就是我们所说的点符号。但是如果我告诉你，你也可以用一个字符串访问一个属性，就像你访问一个数组的索引号一样。这里有一个例子:

```
obj1["foo"]// expects: "bar"// it’s the same asobj.foo// expects: "bar"
```

等等，不要太激动。如果我告诉你，就像数组项一样，你可以进行任何类型的计算，甚至可以调用括号内的函数，如下例所示:

```
obj1["prop" + "1"] // doing some computation// expects: "Trololo"function foo() {return "foo"}obj1[foo()] // calling an object// expects: "bar"
```

如果你曾经在 Ruby 中使用过 python 或 hashes，你应该已经熟悉了这种语法，并且知道它为我们 JS 开发人员打开了无限的潜力。**它使我们能够动态地访问属性和调用功能。不管怎样，现在你可以尽情放松了。**

# 括号符号有什么用？

## 动态访问属性

考虑一个随机 CMS 的 API，它将为您提供一个 JSON，其中包含用多种语言编写的一篇文章的标题和摘要，如下所示:

在比利时，我们说 3 种语言，这是我在现实生活中经常遇到的事情

好了，现在考虑这个响应已经被转换成一个对象并存储在一个名为 ***respond*** 的变量中。如何用指定的语言创建一个可以返回指定属性的函数？

如果您以前不熟悉括号符号，您的第一反应将是嵌套的 if-else 语句。这可能会产生很好的代码，但是如果缩进正确的话，也需要大约 25 行以上的代码。你也有 switch/case 语句选项，但是它也需要嵌套它们，而嵌套的 switch/case 就是一团乱麻。用括号符号，我可以这样做:

```
// respond variable was given before
function displayContent(attrName, lang) {
  return respond.data[attrName][lang]
}
```

是的，只要三行代码，你就可以做到！现在，您可以用法语显示标题，如下所示:

```
displayContent("title", "fr")
// expects: "Combien de hot-dogs peux-tu manger?"
```

很神奇吧？但是，尽管这段代码看起来很神奇，但它仍然太单薄、不安全、易受攻击。让我们把它带到体育馆去放松一下，感到安全！

这是升级/安全版本的样子:

11 行缩进良好、安全且非常优雅的代码。

*稍后再谈安全问题。你会明白为什么这个代码的第一个版本是超级脆弱的。*

## 动态调用方法

一个简单的典型例子:假设你需要实现 [OAuth 2.0](https://oauth.net/2/) ，为多个社交网络提供登录选项，比如脸书、谷歌、Instagram。你可以这样写:

相当简单，但如此强大！一旦你知道了括号符号，你就会一直用到它。

但是要小心，年轻的学徒，即使它是强大而简单的，但在未经训练的开发人员手中可能是非常危险的。最坏的情况是，如果你不学会驯服它的阴暗面，它会破坏代码库。

![](img/fb939403f0a332dd8f8e898f2bb01cbf.png)

现在我们来看看如何成为一个负责任的开发者。我们来谈谈弱点。

# 你应该知道的危险的东西！

## 胁迫警报！

当您使用括号符号时，括号**中的任何内容都应该是或者返回一个原始值**(字符串或数字)。如果您的目的是访问一个现有的属性，请始终确保返回的原语与一个现有属性的名称相对应，以便它能够正常工作。

如果你不返回一个原语，你放在那里的任何东西都将被强制转换为 1。当这种情况发生时，您的代码变得非常不可预测并且很难调试。[检查此 mdn 示例](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Property_Accessors#Property_names):

```
var foo = {unique_prop: 1}, bar = {unique_prop: 2}, object = {};
object[foo] = 'value';
console.log(object[bar]);
```

令人惊讶的是，这段代码的控制台输出不是*。给自己一分钟时间猜猜会是什么…*

*它将 ***【值】*** 成为*c*ause booth***foo***和 ***bar*** 变量是两个不同的对象。即使不同，也要挟成同一个字符串值:***【Object Object】"***
所以，两者都会访问同一个属性，在第二行代码中创建，像这样:
***Object["[Object Object]"]。****

*![](img/06596c21e8dcc09148be5957113f60c1.png)*

*我给你的建议是:别乱来！JavaScript 中的有意强制已经很难处理了，**无意强制是真正的噩梦**。我敢打赌，至少 70%的 JavaScript 迷因，就像上面这个，都是关于它处理强制的愚蠢方式。就是这么糟糕！*

*括号内的符号**总是**显式返回一个原语！[这里有更多关于强制的内容。](https://www.freecodecamp.org/news/js-type-coercion-explained-27ba3d9a2839/)*

## *让用户访问您的属性和方法*

*使用括号符号，您可以创建一个应用程序，在其中向您的用户提供不需要的访问属性。更糟糕的是，他们可能会调用不应该调用的方法。*

*让我们来看看我能想到的最愚蠢的例子:
想象一个网络应用程序，你可以在那里问一堆关于某人的问题。这段 HTML 带有一个输入字段、一个按钮和一个元素，请求的数据应该显示为:*

```
*<div>
Ask something about John: <input type="text" id="dataAsker">
<br>
<button id="dataLeaker"> answers! </button>
<p id="answer_el"></p>
</div>*
```

*现在，考虑附加在它上面的这个脚本:*

```
*data: {
  first_name: "John",
  last_name: "Doe",
  age: 34,
  profession: "Proud developer",
  credit_card_number: "12345678", // Should be private
  dirty_secret: "bets in illegal hot-eating contest" // private
  methods: {
    full_name(){return(this.first_name + " " + this.last_name)},
    dangerous(){// imagine some code the breaks the whole INTERNET},
    leak_everything() {// image it leaks the all of googles data}
  }
}answerRequest() {
  const input = document.getElementById("dataAsker").value
  const answer_el = document.getElementById("answer_el") if (data[input] !== undefined) { // access properties
    answer_el.innerHTML = data[input]
  } else if(data.methods[input] !== undefined) {
    // invoke if its a method
    answer_el.innerHTML = data.methods[input]()
  }
}document.getElementById("#dataLeaker").addEventListener("click", answerRequest);*
```

*在这个应用程序中，用户可以访问所有的数据，并可以调用 ***数据*** 对象所拥有的方法。例如，如果他在输入字段中填写**“first _ name”**并请求一个答案，他将在答案元素中返回**“John”**。但是，他也可以用同样的方式访问***crediet _ card _ number***和 ***dirty secret*** 属性。由于这段代码给了用户访问所有方法的权限，他可以调用无害的 ***full_name*** 方法以及非常有害的 ***dangerous*** 和***leak _ everything***方法，这些方法显然会让整个互联网着火。所以要非常注意你的用户可以用括号符号访问什么。*

## *解决方案:验证*

*正如你从下面的例子中看到的，我总是使用某种验证模式来确保我不会盲目地执行参数中给出的任何东西:*

*基本上，我设置了一个有效原始值的数组。之后，我检查括号符号中使用的给定参数是否是有效值之一。*

*使用这种模式，我可以同时解决这两个问题，并检查参数是否等于有效的原始值之一。因此，不存在非故意胁迫的风险。最重要的是，只有经过阵列验证的内容才可以访问。这使得用户没有访问他不应该访问的内容的风险。*

*您不需要使用与我在这里使用的完全相同的验证模式，但是您需要以某种方式验证这些东西。*

# *我们到此为止*

*你觉得这篇文章怎么样？所有的例子都清楚了吗？你知道为什么要小心括号符号吗？这篇文章还有什么没有回答的问题吗？请在下面的评论中告诉我。*

*本系列的下一篇文章:使用逻辑运算符返回内容*

*[点击此处查看完整系列](https://medium.com/@patick.cyiza/7-js-patterns-junior-developers-should-know-well-1a0ad075338f)*