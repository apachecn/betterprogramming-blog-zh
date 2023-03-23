# 应用程序二进制接口——API 的底层相关接口

> 原文：<https://betterprogramming.pub/application-binary-interface-apis-low-level-relative-91108559dd52>

## 在所有 API 背后都有一个低级的表亲，一种不同的怪兽:APIs 应用程序二进制接口

![](img/7d4a67ed1a8aea9891d8900cfef8ebd3.png)

[翁贝托](https://unsplash.com/@umby?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/chip?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# 你为什么要在乎？

嗯，我没有一个很好的答案，除了我相信了解我们专业的来龙去脉会让我们成为更好的开发人员。

它也往往会唤醒我们的好奇心，这总是一件好事。

# ABI 是什么？

为了理解 ABIs，让我们先回忆一下什么是 API。

API 本质上是一个契约，它使您能够在不同的应用程序之间发送和接收数据。

合同有条款和条件——只要你遵守规则，你就会得到你想要的。

> *让您的程序访问我的程序的数据—使用此格式和 URL 来调用我—* `*GET* [*https://www.somesite.com*](https://www.somesite.com)*?someQueryParm=42*`
> 
> *作为回报，我将以下面的格式给你回复* `*{ “result”: “Some imaginary computer says this is the meaning of life”}*`

要使用 API，您不需要使用与 API 创建者相同的源代码。API 可以用 GO 编写，而我用 JS，完全没问题——只要我们遵守约定。

ABI 非常相似——但是在二进制水平上。契约是两段二进制代码之间的。为了合作，他们需要遵守相同的规则。每个处理二进制表示的工具都需要遵循这些规则。这意味着编译器、连接器和汇编器。

甚至库代码的实现也会受到目标 ABI 的影响。

ABI 公约取决于两个主要因素:

*   指令集架构(ISA) —主要用于调用约定
*   正在使用的操作系统——系统调用、运行时库等。

这种双重依赖性就是为什么为 Windows 编译的代码不能在 OS X 机器上运行，即使它们可能使用相同的 CPU 和相同的 ISA。

# ABIs 包括什么？

它们包括相当多的东西，但它们的主要目标是定义如何访问数据结构，以及如何用机器代码调用计算例程(函数)。

ABIs 还定义了以下内容:

*   处理器指令集——处理器的寄存器和堆栈如何组织，内存访问等。
*   可以使用的数据类型— unsigned int 32 等。
*   如何表示一个库函数的名字？在 API 中，这非常简单，即使使用了函数重载。然而，在二进制中，我们需要一个唯一的表示。因此 ABI 需要定义如何解析相同的函数名——例如，通过[名称混淆](https://en.wikipedia.org/wiki/Name_mangling)。
*   应用程序如何通过直接/间接系统调用调用操作系统，堆栈如何增长，[字节序](https://en.wikipedia.org/wiki/Endianness)等。

## 一些现实生活中的例子

但是首先，一个有趣的不太真实的用例——让我们看看下面的比萨饼制作程序:

API 级别。API 有一个接受一个参数的`PreparePizza`函数:`pizzaSize`——让我们假设它是一个从`1`到`3`的整数。

ABI 水平。ABI 更深入地定义了参数是在调用堆栈上传递还是通过寄存器传递。

所以我们调用了一个 API—`preparePizza(3) → max size`。我们真的饿了。
代码被转换成二进制代码，机器消耗它(看到我在那里做了什么？)，而`Pizza`已经在路上了。

现在，没有一些`toppings`的`Pizza`是什么？

于是，API v2 又多了一个说法——`toppings`。出于某种原因(显然是为了让我更容易理解这个例子)，它也是一个整数。这次从`1`到`6`。每个数字代表不同的配料——额外的奶酪、橄榄、菠萝(哦，天哪)、蘑菇...你明白了。

所以我们进行了另一个 API 调用— `preparePizza(3,1) → max size`。我们还是很饿，我们想要更多的奶酪。

只有一个问题。我们使用了不同的编译器。它成功地编译了我们的代码，但是这个编译器使用了不同的 ABI——与之前的编译器相比，它以相反的顺序将参数传递给调用堆栈。

因此，在幕后，会生成不同的二进制代码。

比萨饼送到了我们的门口。上面有菠萝的小比萨饼。

(3，1)变成了(1，3) →多了奶酪的大披萨变成了上面有菠萝的小披萨。

现在是一些现实生活中的例子:

*   使用不同的语言——例如，Pascal 以与 C 相反的顺序在堆栈上传递参数——所以默认情况下，它们不遵循相同的 ABI。
*   为同一种语言使用不同的编译器——不同的 C++编译器有不同的命名方式(仅举一个例子)。所以他们的二进制文件不能链接成一个可执行文件。
*   Linux 内核因没有保持稳定的 ABI 而臭名昭著——每次都必须重新编译(尽管有变通办法——DKMS)
*   引入对库的更改，而引用库的主可执行文件保持不变——参见示例[这里的](https://stackoverflow.com/questions/2171177/what-is-an-application-binary-interface-abi/54967743#54967743)。

# 底线

您可能永远不会使用 ABI，但是熟悉幕后工作的来龙去脉是有好处的。

ABI 是两段二进制代码之间的契约。每个处理二进制表示的工具都需要遵循这个契约。这意味着编译器、连接器和汇编器。甚至库代码实现。

这使得二进制代码是可移植的，因此支持相同 ABI 的不同平台可以执行它/动态地将其链接到可执行文件中。

如果您想了解更多信息，请查看以下资源:

*   ABIs 上[维基百科](https://en.wikipedia.org/wiki/Application_binary_interface)
*   关于 [Stackoverflow](https://stackoverflow.com/questions/2171177/what-is-an-application-binary-interface-abi) 的精彩讨论