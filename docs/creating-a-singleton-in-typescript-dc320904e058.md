# 在 TypeScript 中创建单例

> 原文：<https://betterprogramming.pub/creating-a-singleton-in-typescript-dc320904e058>

## 众所周知的模式的无类对象方法——从 JavaScript 到 TypeScript

![](img/cdae724f08b3c5ef7f2686a308fcb2b8.png)

由[舒巴姆·达奇](https://unsplash.com/@theshubhamdhage?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/one?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

通常使用 ES6 类声明将设计模式翻译成 JavaScript，这可能有几个原因:

1.  设计模式的流行[定义](https://en.wikipedia.org/wiki/Singleton_pattern#:~:text=the%20singleton%20pattern%20is%20a%20software%20design%20pattern%20that%20restricts%20the%20instantiation%20of%20a%20class%20to%20one%20%22single%22%20instance.)使用*类*关键字
2.  Java 激增，将现有的模式转移到 JavaScript 类是微不足道的(设计模式在 1994 年出现，几年后在 Java 中迅速流行起来)

JavaScript 是多范例的，也可以在同一个代码库中支持类和非类对象(一个[类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes#defining_classes)声明是 JavaScript 中的一个语法糖)。

然而，有一个普遍的共识，由于各种原因，无类方法更好。此外，如果您正在使用 React，并且从 v16.8 开始已经迁移到 Hooks⁴，那么您可能已经停止使用类或者至少不再添加新的类。

# 什么是独生子女

简单地说，Singleton 是一个只能有一个实例的对象。

当需要跨多个模块共享单一版本的数据和/或功能时，这很有用。也许您想声明一次授权凭证，或者用修饰方法创建一个 Axios 实例。在创建日志实例时，它也非常受欢迎。

如果你正在使用 React，你也可以简单地通过使用 [*useContext 钩子*](https://reactjs.org/docs/hooks-reference.html#usecontext) 来创建一个 Singleton。然而，知道如何从第一原理构造单态仍然是有教育意义和有趣的。

# 幼稚的实现

那么，我们如何创造一个呢？

界面很简单。我们只需要一个返回一个对象实例的`getInstance()`函数。对该函数的重复调用(包括跨模块调用)也必须返回相同的实例。

```
const singleton = () => ({ name: "I am not yet a singleton 😿" }) /** Should return _the_ instance of a singleton, not new ones 😕 */const getInstance = () => singleton();
```

但是我们仍然需要能够得到单例。只有一个版本。因此，这不符合实施要求:

*   只能有一个实例(`getInstance()`每次都会创建一个新的实例)
*   全局访问器(只能在脚本中访问)
*   惰性初始化是好的(在调用`getInstance()`时创建)

不过，没关系。上面的例子只是一个开始的试验方法。

# 一个改进的 JavaScript 实现

## 对象闭包

此时，我们可以使用一个[闭包](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)来存储 singleton 的实例。JavaScript 闭包的所有定义似乎都集中在函数的词法范围上，但它也适用于对象！在我们的例子中，singleton 可以存储在外部函数中，并对返回的内部对象可用。代码如下:

JavaScript 中的对象闭包示例

如果您想在一个脚本中创建一个私有但可访问的值，我强调以上方法是一个解决方案，但是对于我们的目的来说，仍然有改进的空间。

## 模块范围

ES6 中的模块是作为脚本文件引入的，只在严格模式下运行，防止函数和变量出现在全局范围内。

有趣的是，模块是单态的；无论导入的频率有多高，它们都只执行一次。

举个简单的例子，这是可行的。一旦模块被导入，立即调用的函数表达式(IIFE)将运行并创建一个实例。如果我们通过`getInstance()`访问它，我们将总是得到相同的单例对象。

但是，您可能已经注意到了一个问题:

*   只能有一个实例(模块生命已经为我们做到了这一点)
*   全局访问器(可通过导出的 getInstance()访问—只需要导入到任何需要访问单例的模块中)
*   惰性初始化不好(模块一导入就被创建)

我们可以利用模块作用域来“隐藏”我们的单例实例并控制对它的访问，我们可以丢弃不再需要的生命:

好了，这解决了我们的懒惰初始化问题，我们现在有一些有用的东西。

*   只能有一个实例
*   全局存取器
*   惰性初始化(在第一次引用时创建)

这里的一个潜在问题是返回的 singleton 可以被客户端修改，所以我们可以通过冻结 singleton 对象来解决这个问题:

但是如果我们想更进一步呢？

我们还没有为 TypeScript 实现引入类型，所以让我们开始吧。此外，如果我们想有选择地用一个值初始化单例怎么办？

# TypSscript 实现

第一步是为 Singleton 的值创建某种类型，并使它可以从外部访问(因为依赖函数可能希望传递它并将其包含在它们的类型中)

```
/**
 * Type of whatever we want to store in the singleton
 * In this case, it is an object with a name attribute
 */export type SingletonValue = {name: string};
```

我所使用的实际类型是任意的，但是除非我们想要一个单例构建器，否则它将特定于单例的使用，但这是另一个讨论的内容。

单例本身可以是泛型的，因此，如果需要的话，可以将它提取到一个单独的库中。现在，为了简单起见，我将把它放在同一个模块中。

应用的泛型

为了保持 TypeScript 的习惯用法，我们为其包含的值定义了一个<t>泛型。</t>

我已经从实现中移除了`Object.freeze()`,以说明编译时类型脚本将如何帮助保护实现。如果您在自己的代码库中使用它，这应该没问题，但是当实现第三方库时，您可能希望冻结它以防万一。

现在，我们可以在模块中为我们的 singleton 包含一个私有“实例”(回想一下，模块总是在严格模式下运行，所以任何值都是不可访问的，除非我们显式导出它们)

```
/**
 * The only instance of our Singleton
 */
let instance: ReturnType<typeof makeSingleton<SingletonValue>>;
```

这使用了一个叫做[返回类型](https://www.typescriptlang.org/docs/handbook/utility-types.html#returntypetype)的“实用类型”,并指定它将存储的单例值的类型(即，当使用值`SingletonValue`时，`makeSingleton`函数的返回类型)

最后一步是处理可选的初始化，并指定我们正在创建的 Singleton 的类型。代码如下:

然而，现在有一个小问题，即每次我们随后调用`getInstance()`时，初始值都可能被传递。这不是所有的问题，因为它只是被忽略，但它可能会导致误解。

最好的方法是，如果已经创建了 Singleton，就抛出一个异常。代码如下:

如果在创建后尝试初始化，则引发异常

所以我们现在已经达成了满足我们要求的最终版本。它看起来是这样的:

单例的最终实现

# 单例的用法

从任何模块中，我们都可以简单地获得`getInstance`函数:

```
import getInstance from "./Singleton";const singleton = getInstance();singleton.setValue({ name: "what is in a name" });const theNameObj = singleton.getValue();
```

获取对单例的多个引用将导致相同的底层对象。代码如下:

```
const singletonRef1 = getInstance();
const singletonRef2 = getInstance();console.log(singletonRef1 === singletonRef2);  *// true*
```

此外，扩展 Singleton 是不可能的，因为 TypeScript 会停止。代码如下:

```
singleton.myExtraFunc = () => { /** some logic */ }*Property 'myExtraFunc' does not exist on type '{ getValue: () => Singleton | undefined; setValue: (value: Singleton) => Singleton; }'.ts(2339)*
```

# 包裹

希望你能看到这篇文章不仅仅是关于在 TypeScript 中实现单例，还涵盖了许多有趣的主题(闭包、模块、生命、泛型等等)。)

你觉得这有趣吗？

最后的实现你怎么看？你会做些什么来改善它？

请在下面的评论中告诉我你的反馈！

# *资源*

[1]:维基百科。*单例模式*。https://en.wikipedia.org/wiki/Singleton_pattern

[2]:维基百科。*设计图案*。[https://en.wikipedia.org/wiki/Design_Patterns](https://en.wikipedia.org/wiki/Design_Patterns)

[3]:道格拉斯·克洛克福特。*JavaScript 中的经典继承*。[https://www.crockford.com/javascript/inheritance.html](https://www.crockford.com/javascript/inheritance.html)

[4]:反应堆。*挂钩常见问题解答*。[https://reactjs.org/docs/hooks-faq.html](https://reactjs.org/docs/hooks-faq.html)