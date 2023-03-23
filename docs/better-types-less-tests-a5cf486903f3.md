# 更好的类型，更少的测试

> 原文：<https://betterprogramming.pub/better-types-less-tests-a5cf486903f3>

## 为什么您应该关注类型，以及这如何影响自动化测试

![](img/e199da7192f1006b866b4f0ac89a56b6.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=3222894) 的 [Prettysleepy2](https://pixabay.com/users/Prettysleepy2-2973588/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=3222894)

我对编程的热情开始于动态类型语言(PHP)，所以我艰难地认识到一个类型良好的程序和一个检查器/编译器可以帮助我们防止错误。

我也做了很多自动化测试的工作。多亏了令人敬畏的 Ruby 社区，我很早就学会了这项技能，但是这项技能也是动态类型化的。

在本文中，我们将使用我在开发 [CRUI](https://github.com/cruijs/crui) 时遇到的真实案例来展示类型如何帮助我们交付更好的代码，以及它们如何与测试相关联。

作为参考语言，我们将使用 Javascript 和 [Typescript](https://www.typescriptlang.org/) ，但本文适用于任何动态和类型化语言。

# 没有类型

在 CRUI 中，我们有一个绑定流和特定元素属性的函数。最简单的形式是这样的:

```
function h$b(tag, bind)
```

老实说，我不知道该拿这个函数怎么办。

我真傻，问题显然是没有文档！补充一下吧。

```
/**
 * Element with Bind.
 * Create an DOM Node and bind properties to a StreamBox.
 * 
 * [@param](http://twitter.com/param) tag A string with an HTML Tag like 'div'
 * [@param](http://twitter.com/param) bind An object specifying which property to bind to which stream box.
 * [@returns](http://twitter.com/returns) A component
 */
function h$b(tag, bind)
```

好了，现在我准备好使用它了:我将使用`'input'`作为标签，对于第二个，我需要一个带有流盒的对象，等等，文档中提到的这些属性是什么？

经过一番挖掘，我们(再次)发现了这个问题，并决定彻底解决这个问题:

```
/**
 * Element with Bind.
 * Create an DOM Node and bind properties to a StreamBox.
 * 
 * [@param](http://twitter.com/param) tag A string with an HTML Tag like 'div'
 * [@param](http://twitter.com/param) bind An object specifying which property to bind to which stream box. We support 2 properties: `value` and `checked`. 
 *            This parameter should look like: {value: StreamBox}
 * [@returns](http://twitter.com/returns) A component
 */
function h$b(tag, bind)
```

一个有效的例子是:

```
const stream = new StreamBox('')
const comp = h$b('input', {value: stream})
```

现在我们已经理解了这个函数，我们仍然需要验证它是否实现了它的承诺。

# 无类型—测试

我认为测试是软件开发的基本部分。我们主要有两种方法来测试这个功能:

*   **单元测试**在隔离状态下执行，只测试手边的单元。隔离经常需要嘲笑依赖。
*   **集成测试**考虑整体功能而不是单一功能，尽可能保持真实的依赖关系。

对于这样一个简单的函数，单元测试就足够了。

因此，我们开始测试并涵盖以下所有情况:

*   传递除了`value`或`checked`之外的任何属性都会抛出异常
*   传递除了`input`或其他支持`value`和`checked`的有效 HTML 元素标签之外的东西会抛出异常
*   对于`checked`属性，只允许使用`input`标签
*   为绑定到`value`的 StreamBox 设置一个值将对其进行字符串化
*   为绑定到`checked`的 StreamBox 设置一个值会使它成为一个布尔值
*   更改 StreamBox 值也会更改元素值
*   更改元素值也会更改 StreamBox 值

唷！我们写了相当多的测试，但是现在我们完全有信心这个函数可以工作，并且会随着我们代码库的发展而继续工作。

让我们在应用程序中使用它。它看起来会像这样:

```
const stream = new StreamBox('')
hc('div', [
   h$b('input', { valeu: stream })
])
```

我们对`hc`和`h$b`都进行了广泛的测试，所以我们非常有信心这段代码能够工作。我们通过我们最喜欢的捆绑器运行它，并打开浏览器来享受我们不可思议的创造…一个空白的页面。

好吧，出了点问题。我们打开浏览器控制台，发现如下异常:

```
Error: `valeu` property is not supported.
```

哎哟！一个愚蠢的错别字。

# 无类型—经验教训

*   函数签名通常很少告诉我们如何使用它。
*   文档是必要的，但并不总是可靠的；要么是信息不够，要么是过时了。
*   我们需要编写大量的测试和防御代码，以确保我们的函数行为正常，并且很容易找出哪里出了问题。
*   正确性不会复合:在动态和弱类型语言中，单元测试对正确性的保证非常有限。
*   为了确保正确性，我们需要编写大量的集成测试，这些测试通常很难维护，执行和编写都很慢。
*   鉴于 100%的集成测试覆盖率通常不是一件事，我们在代码库中添加的每一行都可能触发一个新的故障，这个故障只会在运行时被捕获。个人认为，这是一场噩梦。

当使用动态/弱类型语言时，确保代码正确的负担几乎完全由开发人员承担。我们必须更加细心和勤奋，才能做好我们的工作。

# 介绍类型

上述函数的类型化版本可能是:

```
function h$b(tag: string, bind: Object): Function
```

这在 Typescript 中是有效的，并且稍微好一点，但是我认为它对我们没有任何帮助。

添加随机类型不会解决任何问题。我们需要正确理解手头的**域**，然后**尽可能通过**类型** **系统**表达出来**。

让我们重温一下我们为 Javascript 编写的文档:

```
/**
 * Element with Bind.
 * Create an DOM Node and bind properties to a StreamBox.
 * 
 * [@param](http://twitter.com/param) tag A string with an HTML Tag like 'div'
 * [@param](http://twitter.com/param) bind An object specifying which property to bind to which stream box. We support 2 properties: `value` and `checked`. 
 *            This parameter should look like: {value: StreamBox}
 * [@returns](http://twitter.com/returns) A component
 */
```

好吧，我们可以把它整理成:

```
type Tag = string
type Bind = { value?: StreamBox, checked?: StreamBox }
type Component = () => Nodefunction h$b(tag: Tag, bind: Bind): Component
```

现在我们正在谈话！零文档，我们已经有了比以前更多的信息:组件实际上是一个不接收任何东西并返回一个节点的函数。*(请注意，CRUI 组件比这个稍微复杂一点)*

类型/代码和文档的一个很大的区别是代码不能不同步。

# 类型—测试

我们有我们的功能，但是现在我们需要用测试来覆盖它。

让我们重温一下。

> 传递除了`value`或`checked`之外的任何属性都会抛出异常。

等等，我们实际上不再有这个问题了，因为我们定义了 Bind 类型系统会强制遵守这个规则。

好了，继续下一个:

> 传递除了`input`或其他有效的 HTML 元素标签之外的东西会抛出一个异常

嗯，我们不涵盖这一点，但我认为类型可以在这里有所帮助:

```
type Tag = 'input'|'select'|'textarea'
function h$b(tag: Tag, bind: Bind): Component
```

不错！如果我们试图使用除了`input`、`select`或`textarea`之外的任何东西，Typescript 编译器会对我们大喊大叫，强迫我们使用有效的标签。

下一个:

> 对于`checked`属性，只允许使用`input`标记

让我们在这里多考虑一点:我们可以对我们刚刚定义的所有标签使用`value`，但是`checked`只能和`input`一起使用。有趣的是，由于[函数重载](https://www.typescriptlang.org/docs/handbook/functions.html)，Typescript 在这里也能有所帮助:

```
type Tag = 'input'|'select'|'textarea'
type BindValue = { value: StreamBox }
type BindChecked = { checked: StreamBox }function h$b(tag: Tag, bind: BindValue): Component
function h$b(tag: 'input', bind: BindChecked): Component
```

这个定义看起来有点吓人，但它只是编码了我们对这个函数的接受标准:`checked`只能绑定到一个`input`元素，而`value`可以用于定义的元素。代码将在两个定义之间共享。

跳过了另一个测试，请进行下一个测试！

> 将一个值设置到绑定到`value`的流盒会将其字符串化

换句话说:`value`只适用于字符串，`checked`只适用于布尔值。

我们肯定可以通过增强 StreamBox 定义来解决这个问题:

```
type BindValue = { value: StreamBox<string> }
type BindChecked = { checked: StreamBox<boolean> }
```

如果您不熟悉泛型，这只是通知编译器 StreamBox 包含一个用于`value`的 string 类型值和一个用于`checked`的 boolean 类型值。

我们可以在`<>`之间添加任何其他类型，这就是为什么它被称为*通用*。

以下是最后两个验收标准:

> 更改 StreamBox 值也会更改 element 值
> 
> 更改元素值也会更改 StreamBox 值

这里没有捷径，我们只需要编写测试。这个*是这个特定功能的实际业务逻辑*；剩下的只是细节。

最后，我们通过使用类型系统覆盖了 7 个验收标准中的 5 个。这是一个令人难以置信的结果，特别是因为我们的函数逻辑和代码现在没有了 Javascript 版本中必须放置的所有防御代码和异常，这使得它更简单，更不容易出错。

# 经验教训

*   只写*一些*类型也无济于事。我们需要写出正确的类型。
*   由于 IDE 的支持，在类型系统中编码相当多的逻辑是可能的，这将避免运行时检查的必要性，简化逻辑，提高性能并防止我们编码时的错误。
*   我们需要编写比以前少得多的测试，同时对整体正确性有更强的保证。
*   正确性包括:类型可以确保每个函数都以预期的和正确的方式使用；因此，单元测试更加相关。
*   集成测试不太相关，但是对于覆盖类型不够的情况仍然很重要，例如:一个函数期望元素以一定的顺序正确地完成它的工作。
*   通过大量的单元测试和一些集成测试，100%的测试覆盖率是可以实现的。就我个人而言，我不认为这是一个要求。

这里最重要的一点是，编译器现在将防止我们(以及我们函数的任何用户)犯任何愚蠢的错误——这将提高生产率。

## 真实案例，比测试更好

在开发 [CRUI](https://github.com/cruijs/crui) 的时候，由于 Streams 的工作方式，我确实遇到了一些错误。我的第一反应是为它写一个测试，但是我很快意识到这一点帮助都没有。测试对于内部行为是有用的——但是对于由于接收错误信息而导致的问题，测试是无效的。