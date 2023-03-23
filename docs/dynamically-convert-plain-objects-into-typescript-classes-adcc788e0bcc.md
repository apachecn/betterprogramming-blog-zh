# 将普通对象动态转换为 TypeScript 类

> 原文：<https://betterprogramming.pub/dynamically-convert-plain-objects-into-typescript-classes-adcc788e0bcc>

## 使用 reflect-metadata 将普通对象动态转换为具有自定义数据类型的类型检查类

![](img/14c6ef8deae18c0d4d2cc8b2411d4812.png)

照片由 [Ricardo Gomez Angel](https://unsplash.com/@ripato?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

处理普通对象(或 JSON.parse 的结果)是 web 开发的基本部分。为了让开发体验变得可以忍受，我们经常将普通对象塑造成可预测的东西，包括将它们的值转换成比原始类型更强大的东西。

我们可以采取几种方法。一个是工厂函数，它要求您定义转换函数，外加一个带有类型定义的接口。另一个是类，它们在功能和类型上是独立的。我想为后者提供一个理由，因为如今课堂不太受欢迎。

我们将探索如何将普通对象嵌入到类中，同时允许我们在处理自定义数据类型和提供附加功能(getter/setter/methods)时需要的灵活性。

# 简单的解决方案

首先，我们将定义一个普通类:

我们可以这样使用它:

但是我们想要更多。我们希望能够在运行时根据传入的值动态地构造这些类，而不必通过每个类的构造函数来设置每个属性。

一种替代方法是以编程方式将值分配给有效负载中的每个属性。这种情况的常见模式如下:

这非常简单:我们调用`this.hasOwnProperty`来确保我们只为属于这个类的属性设置值。

这很好，但只有当我们希望我们的类只包含基本类型时，它才会有用。除此之外，我们需要在每个类中重复相同的构造函数。

让我们来看一个更实际的类定义:

这更接近于我们在典型应用中的情况。自定义数据类型，比如我们自己的`Address`类、 [Luxon 的](https://moment.github.io/luxon/docs/class/src/datetime.js~DateTime.html) `[DateTime](https://moment.github.io/luxon/docs/class/src/datetime.js~DateTime.html)`，或者 [decimal.js'](https://mikemcl.github.io/decimal.js/) `[Decimal](https://mikemcl.github.io/decimal.js/)`。

# JavaScript 类型问题

在 JavaScript 中，目前还没有本地的方法来找出什么是类型属性并在运行时实例化它们——原因是类型在 JavaScript 中并不真正存在。TypeScript 类型是开发乐趣的语法糖。

JavaScript 中用于运行时类型检查的工具有:

1.  `typeof`，只对原始类型有效
2.  `instanceof`，这只有在您已经知道要检查的类或构造函数的情况下才有用

# 让位于反射-元数据

反射是 Java 和 C#等传统 OOP 语言以及 Go、Python 和 Lua 等语言的常用工具。简而言之，它是一个提供的 API，允许您在运行时检查一个类或属性，并获取其类型。这允许您从中创建新的实例。

JavaScript 中的 [reflect-metadata](https://github.com/rbuckton/reflect-metadata) 提议还不是 [TC39](https://prop-tc39.now.sh) 的一部分，但它是由负责在 C#中实现反射的人编写的，所以可以肯定地说它最终会实现。

尽管 reflect-metadata 是实验性的，但它已经被 Angular 2 大量使用了很多年。Angular 依赖于它的依赖注入机制，即在运行时需要时创建和传递资源。

它就像一个键值存储，只能驻留在类或类属性中。我们可以获取它，并使用它来自动填充类型元数据或管理我们自己的自定义元数据，以便我们可以实现我们所有的目标和梦想。

# 让我们编写一个更动态的方法

安装后:


并将其导入到我们文件的开头:
`import 'reflect-metadata'`

我们需要确保我们的`tsconfig.json` 包含以下内容:

```
"compilerOptions": {
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true
}
```

这将允许我们使用 decorators 来触发 reflect-metadata，从而在我们的装饰属性中自动填充`design:type`元数据键。

我们还需要一些类型:

**构造函数:**用于表示构造函数或类，我们可以在其上调用`new`
**可索引:**用于定义可索引的类或对象，您可以在其上执行`object[key]` **DeepPartial:** 用于允许我们指定对象或类的部分表示，因为提供的`Partial` TypeScript 实用程序只对一个深度级别有效

现在轮到装修工人了:

**Primed:** 它没有主体，但是将用于触发`Reflect.metadata`被添加到被修饰的属性上，因为 reflect-metadata 只自动填充被修饰的属性的`design:type`元数据。
**Model:** 它将用于覆盖修饰类的构造函数，这样我们就可以隐式地调用我们的自定义初始化方法。

我们将创建一个`Base`类，负责初始化我们的属性，在需要时创建新的实例:

您会注意到我们添加了一个不同的方法来初始化我们的实例，并且我们的构造函数是空的。

这是因为我们想从 B 中初始化 A，这样我们就不必将构造函数复制到每个类中。如果 A 扩展了 B，并且 A 包含具有默认值的属性，则不能从 B 的构造函数中设置 A 的属性，因为它们将被 A 的默认值覆盖:

这就是为什么我们有一个`init`方法。我们要确保在设置属性之前完全初始化。

所以在`init`方法中，我们调用:
`Reflect.getMetadata('design:type', this, key)`来获取为实例中的键自动填充的元数据，它将包含与分配给修饰属性的类型相关联的值。如果它存在，我们用`new`创建一个新的实例，将值传递给它。

使用我们前面的例子，这些类现在看起来像这样:

这个有点小问题。如果尝试执行以下操作，我们会得到一个类型错误:

这是因为我们想要将一个字符串传递到我们的`dob`字段中，将一个数字传递到我们的`netWorth`字段中，而我们的`Person`类分别期待一个`DateTime`和一个`Decimal`。我们所能做的是修改我们的`Base`类来接受一个可选的第二类型，我们可以用它在它和目标类的类型之间创建一个新的联合类型。

这是它看起来的样子:

我们可以这样使用它:

# 处理数组和其他自定义类型

我们就快成功了，但仍有几个问题:

1.  reflect-metadata 没有正确填充数组上的`design:type`。它将它们设置为`Array`而不是预期的类型。
2.  并不是所有的自定义数据类型都会被相同地创建/初始化。对于 Luxon 的`DateTime`，我们想用`DateTime.fromISO`来初始化它。我们也希望其他类型的灵活性。

为了解决这些问题，我们需要能够定制我们在需要时指定什么类型的方式，为此，我们将引入一个新的元数据键。

我们将允许`Primed` decorator 接受一个可选参数，这个参数将是一个类或函数。然后我们用`Reflect.defineMetadata`将它保存到`CONSTRUCTOR_META`键中。

为了处理 Luxon 的`DateTime` 和其他可能以不同方式创建的自定义类型，我们将检查它们并通过一个新的私有函数`parseValue`手动初始化它们。

为了处理数组，我们将检查`design:type`元数据，以了解我们是否需要迭代。

我们将在`CONSTRUCTOR_META`下获取新的元数据，这将优先于`design:type`。

最后，在使地址成为数组之后，这将是我们的类定义和用法:

# 包裹

可以肯定地说，使用工厂函数，您可以省去初始设置，但代价是必须重复设置。您需要创建一个工厂函数和一个带有类型定义的接口。

你可以两者都做，或者(在克服了一些障碍之后)你可以只上一门课。

你可以在这个[代码沙箱](https://codesandbox.io/s/primed-model-v8txy?file=/src/index.ts)里玩代码。

当使用 reflect-metadata 自动填充类型元数据时，有几个注意事项:它不处理自引用或循环引用。

我实际上做了一个简单的包， [primed-model](https://www.npmjs.com/package/primed-model) ，它解决了这些问题，提供了概述的装饰器和基类。它还提供了一个`clone`方法，用于防止传递类实例时的副作用。有兴趣就去看看吧！

如果你想看一个不同的，服用类固醇的人来完成这个任务，你也可以看看[级变形金刚](https://www.npmjs.com/package/class-transformer)。

让我们给班级一些爱！

就这些，感谢阅读。