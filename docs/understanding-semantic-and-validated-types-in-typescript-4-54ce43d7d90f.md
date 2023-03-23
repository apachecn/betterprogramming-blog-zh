# 理解 TypeScript 4 中的语义和验证类型

> 原文：<https://betterprogramming.pub/understanding-semantic-and-validated-types-in-typescript-4-54ce43d7d90f>

## 使用 TypeScript 4.1 中引入的模板文本类型，您可以创建语义和验证类型，使您的代码更加类型安全

![](img/eb2b0791b114c369e85ea4eb99858615.png)

照片由[马库斯·温克勒](https://unsplash.com/@markuswinkler?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

**📕看看我的新书** [**干净代码原则和模式:软件从业者手册**](https://www.amazon.com/Clean-Code-Principles-Patterns-Practitioners-ebook/dp/B0BSDJKYQJ/ref=sr_1_1?crid=8M0CMV4CP6UQ&keywords=clean+code+principles&qid=1674980636&sprefix=clean+code+principle%2Caps%2C175&sr=8-1) ！

# 语义类型

[validated-types](https://github.com/pksilen/validated-types) 是一个使用 TypeScript 创建语义类型和变量的库。使用语义类型，您可以区分同一基本类型的多种类型。例如，如果一个函数接受两个布尔参数，调用函数可能会以错误的顺序给出这两个参数，但您永远不会注意到这一点，因为它不会生成编译错误。

如果你只是像平常一样嘲笑被调用的函数，你也不会在单元测试中发现它。只有你的集成测试覆盖率足够大，你才能在集成测试中抓住它。

使用语义类型，您可以通过给它们两个不同的语义名称来区分这两种布尔类型。那么就不可能以错误的顺序给出这两个论点。语义名称只是描述类型和变量用途的任何字符串。语义名称的类型定义如下:

```
type SemName<N extends string> = N extends `${infer Name}` ? `${Name}` : never;
```

下面的例子使用`validated-types`库的`SemVar`(语义变量)声明两个语义类型和两个不同语义名称的布尔类型变量:`isRecursiveCall`和`isInternalCall` *。*

# 验证的类型

[validated-types](https://github.com/pksilen/validated-types) 也是 TypeScript 的类型值验证库。您可以验证整数、浮点数、字符串和数组的值。使用这四个基本验证器，您可以创建被验证的完整对象。例如，对于数字，您可以验证最小值和最大值。对于字符串，您可以验证最小和最大长度，还可以执行语义验证来验证字符串是否应该是有效的 URL、IP 地址或电子邮件地址。

通过对应用程序函数的参数使用经验证的类型，您可以放心，只有正确的值才会传递给函数。您不必在函数内部做任何验证工作，只需使用已经验证过的参数值。值的验证只在构造验证对象时进行一次，因此根据需要多次使用验证值不会影响性能。

您应该在接收未经验证的输入数据的函数中创建经过验证的整数、浮点、字符串和数组对象，然后将经过验证的值传递给应用程序中的其余函数。

在下列情况下，您的应用程序通常会从外部来源接收未经验证的输入数据:

*   读取命令行参数
*   读取环境变量
*   读取标准输入
*   从文件系统中读取文件
*   从套接字读取数据(网络输入)
*   来自用户界面的最终用户输入

## 验证整数

下面是一个使用`validated-types`库的`VInt`(经验证的整数)创建一个允许值在 1 到 10 之间的经验证的整数的例子:

上例中为`VInt` 给出的泛型类型参数“1，10”与以下类型匹配:

```
type IntValidationSpec<ValidationSpec extends string> =
 ValidationSpec extends `${infer MinValue},${infer MaxValue}`
    ? `${MinValue},${MaxValue}`
    : never;
```

如果你用一个不同的泛型类型参数声明一个`VInt`，例如‘0，100’，那么它就是它自己的不同类型。你不能用一个类型为`VInt<’1,10'>`的变量来代替类型为`VInt<’0,100'>` 的变量，反之亦然，你会得到一个编译错误，这就是为什么你的代码更加类型安全。

## 验证字符串

下面是一个使用`validated-types` 库的*`VString`*(经验证的字符串)创建经验证的字符串的示例，该字符串允许 URL 的最小长度为一个字符，最大长度为 1024 个字符:**

# **资源**

**有关`validated-types` 库和 API 文档的更多示例，请查看 Github 中的以下资源库:**

*   **[https://github.com/pksilen/validated-types](https://github.com/pksilen/validated-types)**