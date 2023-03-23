# 现代 Javascript 技术

> 原文：<https://betterprogramming.pub/modern-javascript-techniques-cf2084236af4>

## 追求纯净的干净和可扩展的语法

![](img/350be117ec75a9e8dbafe0a99518a365.png)

格里特·维穆伦在 [Unsplash](https://unsplash.com/photos/hLmIbKb7PCA) 上拍摄的照片

作为一门非常复杂且适应性很强的语言， [JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript) 拥有许多优势，而且每年都在增长。难怪该语言和社区如此受欢迎，因为它在将用户界面交互性和响应性 web 设计引入互联网方面发挥了巨大作用。虽然有时很复杂，但这种语言被证明很容易掌握，并且通过在客户端执行可以获得更快的用户体验。

很长一段时间，JavaScript 被视为有问题和有缺陷的。这从来都不是语言的问题，而是运行语言的平台:浏览器。这个生态系统是有缺陷的，因为有如此多的分支派别——最明显的是微软的介入，把一切都搞砸了。

在这一时期，Mozilla 一直是理性的声音，但直到 Chrome 获得了足够的市场份额，人们才开始考虑引擎应该是什么样子以及如何构建的标准。

用 [V8](https://v8.dev/) 设定标准就是 [Node.js](https://nodejs.org/en/) 随后的构建方式。作为一种具有服务器端执行支持的完整编程语言，JavaScript 现在为现代 web 应用程序提供支持，并在整个技术堆栈中扩展。

# 方法

作为智能法律合同开源项目 [Accord Project](https://www.accordproject.org/) 的维护者和全栈工程师，我的经历向我展示了 JavaScript 可以实现的强大应用。

此外，我变得非常热衷于学习和采用更好、更有效的语言实践。我将分享这一点，作为对他人的有益参考，也作为我自己未来的历史参考。我希望在后续更深入的文章中扩展这个话题。

![](img/d41861a7fc5b856757535e1e85817e53.png)

我们在 Accord 项目中的大部分工作都是用 JavaScript 编写的，还混合了一些特定领域的语言。为了构建一个可靠的技术栈，允许智能合同的稳定性和效率，Accord 项目依赖于 JavaScript，以及 [OCaml](https://ocaml.org/) 和 [Ergo](https://www.accordproject.org/projects/ergo) 。JavaScript 提供了最好的工具集，可以在各种各样的用例及环境中处理这个问题。我们选择 JavaScript 是因为它的适用性、库的多样性和易用性。这种语言的语法既有表现力又很简单。

Accord 项目核心代码库包含超过 25 万行代码。连同我们的模板库和 UI 组件，有将近一百万个。

## 大纲:

→ [方法](https://medium.com/@jolene.langlinais/modern-javascript-techniques-cf2084236af4#db39)
→ [基础](https://medium.com/@jolene.langlinais/modern-javascript-techniques-cf2084236af4#1519)
→ [工作流程](https://medium.com/@jolene.langlinais/modern-javascript-techniques-cf2084236af4#205c)
→ [操作](https://medium.com/@jolene.langlinais/modern-javascript-techniques-cf2084236af4#7389)
→ [功能](https://medium.com/@jolene.langlinais/modern-javascript-techniques-cf2084236af4#3f3d)
→ [异步](https://medium.com/@jolene.langlinais/modern-javascript-techniques-cf2084236af4#eff3)
→ [功能编程](https://medium.com/@jolene.langlinais/modern-javascript-techniques-cf2084236af4#dc1e)
→ [结论](https://medium.com/@jolene.langlinais/modern-javascript-techniques-cf2084236af4#63d0)
→ [资源](https://medium.com/@jolene.langlinais/modern-javascript-techniques-cf2084236af4#fff0)

# 基本原则

## 可理解的

文档代码。可读性对于编程来说是至关重要的，因为是人类需要解释代码来进行协作。与通过用单个字母命名变量来节省一些额外的字符相比，足够冗长以便日后或对其他人来说清晰易读是一种更好的做法。此外，注释和文档——如 [JSDocs](https://devdocs.io/jsdoc/about-getting-started) 格式——对于构建可与团队或其他人共享的可访问代码非常有用。

乍一看，这似乎是多余的，但是尽可能地对代码进行注释将允许您在几个月后回到某个项目或与同事合作时，轻松地刷新内置文档。

## 全局

避免全局范围内的变量。避免在全局范围内使用变量有多种原因。由于函数执行会导致 JavaScript 从 in 到 out 搜索范围变化，直到找到全局对象，因此性能会降低。此外，这也存在安全缺陷，因为在全局空间中定义函数时，可以通过浏览器调用这些函数。这一点将在[功能编程章节](https://medium.com/@jolene.langlinais/modern-javascript-techniques-cf2084236af4#dc1e)中再次出现。

## 变量

停止使用`var`。范围行为是不一致和混乱的，这可能会导致错误。ES6 带来了`const`和`let`。严格以使用`const`为目标，只有在不可能的情况下才使用`let`。有更多的限制，而且`const`是不可重新分配的，但也不是完全不可变的。该变量将具有对同一对象或原始值的不变引用，但是该变量持有的值不是不可变的。尽管如此，这仍将是向前发展的最佳实践。

## 命名

有点跑题了，但是程序员可以在命名约定上花费 10 倍的精力，却努力让自己的语言具有包容性。

花些时间来描述和适当的易读性和全面的可读性将会在代码的未来创造奇迹。

这对那些希望教育他人的人来说尤其重要；变量名应该有助于解释和给出代码中发生的事情的上下文。刚接触这段代码的人应该能够对正在发生的事情有一个大致的了解。使用动词。布尔变量的例子可以从`is…`开始，函数的例子可以是动作动词。

这里可以找到很好的参考资料: [*一个基于语法的命名约定*](https://dev.to/somedood/a-grammar-based-naming-convention-13jf)

## 工作流程

可维护性的一个主要关键是将逻辑保持在正确的位置，而不是杂乱无章。项目或代码库的构建方式会对理解和遵循的难易程度产生很大影响。

## 进口订单

从粒度级别开始，导入不同模块的顺序可以通过一个可预测的模式来减少混乱。你所使用的特定结构不如存在某种结构重要:

## 模块化

要记住的一个目标是保持包、模块、函数和范围较小。在实践中，可重用性和链接变得更加容易。相似的功能，或者有许多步骤的功能，可以归入一个模块或类。尽量保持功能简单，逐步执行复杂的过程。

一旦一个文件超过 300-400 行代码，就很有可能变得过于混乱和不可维护。在这一点上，通过创建新的模块和文件夹来分解流程可以获得很多好处。将项目想象成一棵有许多分支的树，而不是堆积如山的代码。

ESLint 是一个很好的工具。目标是让文件的深度少于四到五个凹痕。这使代码保持专门化，并鼓励清理死代码。做一个小程序的几个函数比做几件事的一个函数更有用。大函数只能以这种方式使用，而小函数可以在一个项目的多个过程中使用。公开这些较小的辅助函数会在项目中创建一个健壮的 API 库。

伟大的代码可以在不重写一切的情况下得到改进。

## 隔离代码

一个功能应该有一个目的，而不是做多个动作。这个目的应该不是副作用，但是我们会在[函数式编程部分](https://medium.com/@jolene.langlinais/modern-javascript-techniques-cf2084236af4#dc1e)回到这个话题。

一个人为的例子是封装条件:

## 保护条款

构造具有导致错误或空结果的边缘情况的函数的一个好方法是尽早检查这些无效结果。如果这个条件没有被满足或者有一个无效的用例，那么大部分的计算就被阻止了，因为我们已经知道了结果。这被称为[弹跳模式](http://rikschennink.nl/thoughts/the-bouncer-pattern/)或 g [uard 子句](https://dev.to/lanecwagner/guard-clauses-how-to-clean-up-conditionals-2fdm):

这不仅会优化代码，还会鼓励以一种考虑处理边缘情况的方式来思考函数和过程。

## 漂亮和林挺

我的文章的一个主题是代码应该易于阅读和理解。随之而来的是一致的风格和结构。棉绒——任何棉绒——都会非常有用。ESLint 是一个 linter，可以识别代码正确性的问题，比如使用`var`的警告。[更漂亮的](https://prettier.io/)是一个格式化程序，例如，它将识别一致性和一致性问题，并自动对齐括号。鼓励两者结合使用。

如果你需要一个好的起点，StandardJS 和 ESLint 的[预定义配置](https://github.com/feross/eslint-config-standard)是林挺规则的好来源。

# 操作

## 解构

通过保持变量简短并在早期从对象中取出，析构可以帮助节省大量的输入和代码行。ECMAScript 6 引入了这一功能，允许从任何对象或模块访问特定字段，并立即将其赋给变量。

对象:

数组(跳过由`, ,`组成的元素):

功能(类似于对象):

## 默认值

当进行析构时，可以为参数分配默认值。这也可以向用户表明可以传入什么值或者需要什么值。

如果在值没有被传递时不应该抛出错误，那么默认值可能是有用的。

## 第三的

该运算符类似于[逻辑运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators)和`if…else`语句，有三个部分:

1.布尔条件
2。真实情况下的返回值
3。falsy 情况下的返回值

尽量避开消极条件句——检查某事是否存在，而不是它是否不存在。

## 传播

另一种形式的对象析构，spread 操作符允许从数据中提取值，而不必显式地迭代数据。这在 [Redux](https://redux.js.org/) 和函数式编程中很常见，因为这是一种在不改变对象的情况下添加对象的捷径——通过扩展旧对象并向其添加新值来复制旧对象。

## 模板文字

这个特性允许将动态内容嵌入到字符串中，并编写跨多行的字符串。这些用反引号(````)和模板文字片段(`${}`)指定。

# 功能

## 限制范围

函数应该做一件事。一旦他们开始执行多个动作，就很难测试和推理。目标是在函数中不要超过一个抽象层次——如果必要的话，将函数拆分。

## 箭

这种新的函数语法为符号提供了简明清晰的流程。通过从定义函数的作用域中继承`this`,这些函数还有更实际的作用域行为。

以前，函数可以写成:
`function someFunction(input) { /* ...code */ }`

现在我们把同一个东西定义为:
`const someFunction = input => { /* ...code */ }`

如果函数只是返回一些简单的东西，我们可以用一个隐式的`return`语句将它写成一行:
`const add = (a, b) => a + b;`

`const createObject = (a, b) => ({ a, b });`

## 因素

旨在限制传递给函数的参数数量，以提高可测试性。理想情况下，这将低于 3。通常，如果有三个或更多的参数，该函数可能试图自己做很多事情，应该拆分和合并。

## 链接

当前令人沮丧的一个原因是无法方便地访问对象中的嵌套值。目前可能会使用类似这样的内容:

可怕的。

这样做的原因是，如果您直接进入最后一行，您可能会遇到这种错误:
`TypeError: Cannot read property ‘fourthProp’ of undefined`

TC39(决定哪些特性成为 JavaScript 标准的一部分的技术委员会)已经将[可选链接提议](https://github.com/tc39/proposal-optional-chaining)移到了接受的后期阶段。

我真的很期待，因为它会让上面的代码看起来像这样:

如果任何属性不存在，挖掘退出并返回`undefined`。

另一个当前的解决方案是 [Ramda](https://ramdajs.com/) ，它使用一个名为`path`的函数在运行时安全地执行代码，并且不会在控制台中遇到`undefined`错误。

# 异步的

我以前写过“[与 Redux Sagas](https://medium.com/swlh/asynchronous-with-redux-sagas-b43c9630f218) 的异步”，但我将更多地关注`async` / `await`并对此做出承诺。

异步仅仅意味着事情的发生独立于主程序流；电脑就是这样设计的。处理器不会暂停以等待副作用发生来恢复操作。JavaScript 默认是同步的，单线程的；代码不能并行运行。

然而，JavaScript 是为响应用户动作而设计的，本质上是异步的。JavaScript 所在的浏览器提供了一组处理该功能的 API。而且， [Node.js](https://nodejs.org/en/) 引入了非阻塞 I/O 环境，将这个概念扩展到文件、网络调用等。

![](img/6d2b487f01cbe13152399e253d41e951.png)

当这个 side 函数被交给一个单独的线程时，比如一个 API 调用，它作为一个回调函数返回，这个回调函数是作为一个参数传递给另一个函数的函数。然后在外部函数中调用它来完成一个动作。

## 异步和等待

以前，JavaScript 依赖于异步代码的承诺和回调。这很容易导致[回调地狱](https://blog.hellojs.org/asynchronous-javascript-from-callback-hell-to-async-and-await-9b9ceb63c8e8)。这种建立在承诺之上的语法糖提供了一种更加平滑的处理异步代码的方式，但是它不能用于普通回调或节点回调。现在异步代码可以写得更像同步代码。类似于承诺，这些是非阻塞的。

使用这个的函数需要在它前面有`async`关键字，`await`只能在有这个关键字的函数中使用。这个`async`函数隐式返回一个承诺，该承诺将解析为函数内部返回的值。

**好处:**
+清晰:代码更少，可读性更强。
+错误处理:`try` / `catch`可以处理同步和异步代码
+条件:更直接地处理动态结果
+调试:错误堆栈跟踪更容易跟踪
+等待任何东西

# 函数式编程

谈到编程，有两种主要的范例:命令式和声明式。编写函数的强制性方法是解释过程的每一步，而声明性方法表达了计算逻辑，而没有描述具体的流程。

**命令式**:如何做某事
*例句*:指示某人烤一个蛋糕，按部就班
**陈述式**:做什么
*例句*:通过描述一个蛋糕来告诉某人烤一个蛋糕

函数式编程是声明性的。作为一种令人生畏的强大编程范式，函数式编程将计算视为数学函数的评估，并避免改变[状态](https://en.wikipedia.org/wiki/Program_state)和[可变](https://en.wikipedia.org/wiki/Immutable_object)数据。函数是 JavaScript 中的一级实体，这意味着它们被视为值，可以用作数据。函数可以从常量和变量中引用，作为参数传递给其他函数，并作为函数的结果返回。

## 纯函数

在函数式代码中，输出值只取决于传入的参数，对于相同的输入，输出值总是相同的。相反，面向对象的程序通常依赖于状态，并且在不同的时间使用相同的参数会产生不同的结果。

纯函数是遵循函数式编程的一些准则的函数；也就是说，给定相同的参数([幂等](https://en.wikipedia.org/wiki/Idempotence))，它返回相同的结果，并且不会引起可观察到的副作用。这使得它在引用上是透明的，这样做的好处是代码更容易测试。有了这个概念，我们就能够[记住](https://en.wikipedia.org/wiki/Memoization)这些功能。

## 副作用

在函数式编程中避免可变性，例如修改全局对象或全局范围内的值。函数式编程的目的不是改变数据，而是通过增加或减少来创建新的数据副本，而不是改变原始数据。

主要的一点是避免陷阱，比如在对象之间共享状态，或者使用可以被任何东西写入的可变数据。一个不纯粹的动作，比如写文件，应该被限制在一个服务范围内——尽量减少不纯粹的功能。

在 JavaScript 中，原始数据类型通过值传递，而对象通过引用传递。因此，如果一个函数对一个数组进行了更改，那么引用该数组的任何其他函数都会受到影响。这是函数式编程试图避免的巨大危险；如果两个独立且不相关的函数接受相同的输入，但其中一个函数改变了该输入，那么另一个函数现在就有缺陷。一直克隆大型对象会对性能造成很大的负担，但是有一些非常好的库，比如 [Ramda](https://ramdajs.com/) 。

## [拉姆达](https://ramdajs.com/)

Ramda 是一个优秀的库，它为 JavaScript 中的函数式编程提供了额外的工具，使创建代码管道变得更加容易。

![](img/1a8ad848457722c427404a4045d80294.png)

所有的函数都是自动生成的，这使得这个库非常有用。他们的 wiki 有一个帮助你的有用部分，叫做“[我应该使用什么功能](https://github.com/ramda/ramda/wiki/What-Function-Should-I-Use)？”

[Currying](http://www.sitepoint.com/currying-in-functional-javascript/) 给了我们使用高阶函数(将函数作为输入和返回函数的函数)和闭包的能力。curried 函数不是一个有多个参数的函数，而是一个只有一个参数并返回一个只有一个参数的函数。这些串在一起形成一个管道。

## 平静的

虽然 [Ramda](https://ramdajs.com/) 非常适合在一个管道中组合函数，但是 JavaScript 是一种不断发展的语言，很快就会有这种特性。TC39 目前有一个 p [ipeline 操作符](https://github.com/tc39/proposal-pipeline-operator/wiki)的提案。同时，检查一下 [Ramda](https://ramdajs.com/) ，找到一些真正强大的工具。

# 结论

许多圈子对 JavaScript 的批评已经失去了价值。我想这需要 10 倍的时间来消除他们的疑虑。这种语言具有很高的效率，适用于许多环境和应用程序。整个技术领域有许多激动人心的使用案例，能够触及整个堆栈。

抛开这一领域的把关和毒性不谈，进入如此多不同部门的能力为社区提供了一个更具协作性和更有经验的群体。这种语言有如此强大的力量。跨平台桌面应用可以用电子版的 JavaScript 构建，移动应用可以用 React Native 构建，服务器端解决方案可以用 Node.js 构建。

虽然这种语言在不断发展，但并不是每周都有新的框架。进步是好的，这种语言背后的社区是相当进步和创新的。

如有任何问题或反馈，请随时联系我。

# 资源

## 社区

[DEV # Javascript](https://dev.to/t/javascript)
[Javascript 周刊](https://javascriptweekly.com/)

## 教育

[FreeCodeCamp](https://learn.freecodecamp.org/)
[可汗学院计算机编程](https://www.khanacademy.org/computing/computer-programming)
[JavaScript 再入门](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript)
[现代 JavaScript 教程](https://javascript.info/)

## 书

[*你不知道的 JavaScript*](https://github.com/getify/You-Dont-Know-JS)[*雄辩的 Javascript*](https://eloquentjavascript.net/)

## *博客*

[埃里克艾略特](https://medium.com/@_ericelliott)

## 播客

[Javascript Jabber](https://pca.st/m5IV)
[JS Part](https://pca.st/ijqf)y
[syntax . FM](https://pca.st/fmx9)
[全栈电台](https://pca.st/fullstack)
[瓢虫播客](https://pca.st/ZD17)
[Javascript to 榆树](https://pca.st/tr6K)
[榆树镇](https://pca.st/i2d2)

## 多方面的

[JavaScript:理解怪异部分](https://www.youtube.com/watch?v=Bv_5Zv5c-Ts)
[30 天 JavaScript 挑战配相应视频 Wes Bos](https://javascript30.com/)
[Fun Fun 函数](https://www.youtube.com/channel/UCO1cgjhGzsSYb1rsB4bFe4Q/featured)
Switch Case vs Object Literal:
→[Switch Case、 if else or a loup map by May Shavin](https://medium.com/front-end-weekly/switch-case-if-else-or-a-lookup-map-a-study-case-de1c801d944)
→[重写 Javascript:用克里斯布尔金](https://medium.com/chrisburgin/rewriting-javascript-replacing-the-switch-statement-cfff707cf045)
替换 Switch 语句静态键入
→[TypeScript](https://en.wikipedia.org/wiki/Microsoft_TypeScript)
→[2019 年开始使用 TypeScript](https://dev.to/robertcoopercode/get-started-with-typescript-in-2019-6hd)
→[TypeScript](https://scrimba.com/g/gintrototypescript)
→[了解 TypeScript 的类型符号](http://2ality.com/2018/04/type-notation-typescript.html)
功能前端