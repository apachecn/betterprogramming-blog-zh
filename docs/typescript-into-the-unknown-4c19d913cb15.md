# 打字稿——进入未知领域

> 原文：<https://betterprogramming.pub/typescript-into-the-unknown-4c19d913cb15>

## 未知类型介绍:更好的任何

![](img/d289f1bbec5c922958b15c42a31cda38.png)

尼古拉斯·洛沃斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

世界各地的 TypeScript 代码到处都是`any`类型，这让我简直要疯了。没有(有效的)理由在应用程序代码中使用它，并且您选择了静态类型系统的安全性。

许多新手(或者对 TypeScript 不太陌生的)开发人员认为，当你不知道你将得到什么类型时，你应该用`any`来键入它。 [TypeScript 的官方文档](https://www.typescriptlang.org/docs/handbook/declaration-files/do-s-and-don-ts.html)也告诉你事实并非如此:

> “除非您正在将 JavaScript 项目迁移到 TypeScript，否则不要使用`any`作为类型。编译器有效地将`any`视为“请关闭这个东西的类型检查”。这类似于在变量的每个用法周围加上一个`@ts-ignore`注释。当您第一次将 JavaScript 项目迁移到 TypeScript 时，这非常有用，因为您可以将尚未迁移的内容的类型设置为`any`，但是在完整的 TypeScript 项目中，您将对程序中使用它的任何部分禁用类型检查。”

除非您正在向开源 JavaScript 库添加类型定义，*不要使用*。如果你正在维护一个开源的 JS 库，那么我很感激你添加类型定义，并且理解你的时间限制。如果到处添加一个`any`允许你在 90%的 API 中添加类型，那么我认为这是一个有效的用例。希望有一天时间允许的时候都重构到`unknown`。理想情况下，TypeScript 社区的成员可以制作 PRs 并帮助您重构它们。

如果我想接受任何东西或者不知道我会得到什么类型的东西怎么办？再次， [TypeScript 的文档](https://www.typescriptlang.org/docs/handbook/declaration-files/do-s-and-don-ts.html#any)提供了帮助:

> “在你不知道你想要接受什么类型的情况下，或者当你想要接受任何东西，因为你将盲目地通过它而不与它交互时，你可以使用`[unknown](https://www.typescriptlang.org/play/#example/unknown-and-never)`。”

# 未知类型

在 TypeScript 3.0 中，我们得到了`unknown`类型，这让我们发出信号“这可以是任何值，所以在使用它之前，您*必须*执行一些类型检查。”与`any`不同，您不能访问类型为`unknown`的值的任何属性，也不能调用/构造它们。`unknown`迫使我们安全地反思返回值。

## 简单的例子

您可以看到一个`any`完全忽略类型检查器的例子:

相反，如果我们将它输入为`unknown`，我们确实会得到一个错误——尽管这个错误会让很多人感到困惑:

## 对象属于未知类型

这个错误迷惑了很多人。与许多有用的 TypeScript 消息不同，这条消息并没有真正解释问题是什么(除非你理解`unknown`类型)。早些时候，我提到过`unknown`的一个更微妙的特性，在浏览文档时很容易被忽略:您不能访问类型为`unknown`的值的任何属性。

TypeScript 使我们将宽类型(`unknown`)的范围缩小到更窄的类型。通常，您会希望做如下事情:

不幸的是，虽然这段代码非常安全，但 TypeScript(目前)不能用`foo`扩展`unknownFoo.prop2`的类型，尽管它是检查属性是否存在的有效保护子句。

很多人被困在这里，放弃了。就个人而言，我很乐意花很多时间来解决这个问题。如果我们不能在大多数情况下使用它，那么这种新类型还有什么意义呢？我们需要创建一个小助手函数来获得正确的类型。以下要点显示了我们需要的代码:

如果我们使用上面的帮助器，我们将获得完全类型安全:

等等……什么？怎么会？

我们的小实用函数利用了 TypeScript 中的[泛型](https://www.typescriptlang.org/docs/handbook/generics.html)和[类型谓词](https://www.typescriptlang.org/docs/handbook/advanced-types.html#using-type-predicates)的能力。

泛型是这部分:`<X extends {}, Y extends PropertyKey>`。我们希望确保第一个参数扩展了对象类型`{}`，第二个参数扩展了`PropertyKey`类型。`PropertyKey`是`Union`类型的字符串、数字和符号的内置别名。基本上，一个 JS 映射中有效的键类型:`type PropertyKey = string | number | symbol`。

类型谓词在 TypeScript 中非常强大，可以有条件地缩小类型范围。基本思想是，如果函数谓词返回`true`，就将参数重新键入一个新类型。

在我们的例子中，谓词是`Object.hasOwnProperty.call(obj, prop)`。谓词只是布尔条件的一个花哨名称。基本上，如果我们的对象有这个属性，我们返回`true`，它告诉类型谓词重新键入我们的值。

这里的最后一块拼图是`obj is X & Record<Y, unknown>`。如果谓词返回`true`，这是重新类型化我们的参数的部分。它说`obj`(我们的第一个参数是`hasOwnProperty` ) *是*(强制类型重铸)`X & Record<Y, unknown>`(我们的新类型)。

`X & Record<Y, unknown>`只是一个[交集类型](https://www.typescriptlang.org/docs/handbook/2/objects.html#intersection-types)。它说它的`X`与键`Y` 和值`unknown`的[记录](https://www.typescriptlang.org/docs/handbook/utility-types.html#recordkeystype)合并在一起。记录类型是 TypeScript 中的实用工具类型。

# 野外不为人知

`unknown`一般发生在 API 边界的野外。我实在想不出有哪种情况是你不会在内部知道数据类型的。出于这个原因，我不太喜欢你看到的很多基本例子，比如这个:

虽然我认为上面的例子很好地强调了我们需要如何缩小类型范围来访问一个`unknown`类型的属性，但是很难想象在现实生活中你会在哪里看到这种情况。

我使用`unknown`的大多数情况是在 API 边界，它通常是在`[Record](https://www.typescriptlang.org/docs/handbook/utility-types.html#recordkeystype)`实用程序类型中，看起来像这样:

```
*const* parameterBag: *Record*<*PropertyKey*, *unknown*> = {
  param1: 1,
  param2: { foo: 'bar ' },
  param3: 'Hello, World!',
};
```

# 包扎

关于添加`unknown`类型的最大抱怨之一是它使代码冗长且难以编写。对此，我唯一的回答是“这才是重点！”它的全部意义在于强迫你处理一个`unknown` 类型的每一种可能的情况。这自然会是一个冗长的过程。如果你的数据类型不是真正的`unknown`，那么就不要这样输入。

为了避免`any`的危险，我建议使用 eslint 规则来纠正显式`any`的使用错误，同时将`tsconfig`改为隐式`any`类型的错误。

```
//eslintrc.json
"@typescript-eslint/no-explicit-any": ["error", {}],// tsconfig.json
"noImplicitAny": true,
```