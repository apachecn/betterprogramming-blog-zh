# 在自定义模块中组织节点代码

> 原文：<https://betterprogramming.pub/organize-nodejs-code-using-custom-modules-caf5081c691e>

## 使用 NodeJS 模块，一切看起来都很干净

当您开始开发节点应用程序时，一切看起来都很简单、明了且编码良好。直到您开始实现 ExpressJS 路线、一般特性和常见任务，在这一点上，一切都开始变得一团糟。JavaScript 一直受到代码组织和上下文分离问题的困扰，但有了 NodeJS 模块，它看起来很干净，编码也很好。

![](img/ae80df415877f76b80affcf2c6563f36.png)

由[万花筒](https://unsplash.com/@kaleidico?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 节点模块

NodeJS 模块是代码、方法、模型等等的包装器。NodeJS 模块只是逻辑上独立的例程或代码部分的容器。使用模块分割代码的方式严格取决于您希望如何组织代码。NodeJS 模块构成了关注点、上下文分离和代码重用的原则。一旦你能以正确的方式分割代码，你就基本上设计了你能随时简单重用的 NodeJS 模块。通过延迟加载，您可以扩展这种实现和概念，并最大化 NodeJS 模块的好处。

*由于 NodeJS 模块能够将几乎任何行为封装在单独的代码部分中，因此您可以决定是否加载资源等。这取决于你。*

# 神奇的词:模块.导出

NodeJS 模块中的一切本质上都是私有的。每当你声明一个新的变量或者一个新的方法，NodeJS 认为这在模块内部是有用的——没有任何东西暴露给外部。这就是为什么在 NodeJS 中，我们可以通过使用`module.exports`来决定应该向外界公开什么。

一个模块可以导出几乎所有的东西:变量、常量、函数，甚至对象。我们知道 JavaScript 是多才多艺且易于操作的，所以很容易让`module.exports`甚至用其定义中的方法来公开对象。

接近自定义 NodeJS 模块的第一步通常很简单，就像这样:

```
module.exports = “hello from a module”;
```

这使得 NodeJS 模块由一个字符串组成。如果您导入这个模块，您所拥有的只是一个字符串:

```
// main.jsvar message = require(“./hello.js”);
```

您可以通过公开函数而不是字符串来扩展相同的模式:

```
// functions.jsmodule.exports = function(name){ return “Hello “ + name;}
```

在这种情况下，无论何时导入模块，都会得到一个看起来像方法的函数:

```
// main.jsvar hello = require(“./functions.js”);console.log(hello("Ivano"));
```

# 导出对象

您可以导出和导入自定义 NodeJS 模块的方式很容易编码。在一个定制模块中需要公开几个方法和多个变量是很常见的——我们如何做呢？

很简单。通过导出一个对象，而不是一个单独的变量或方法，来扩展相同的概念和相同的语法。通过使用标准的 JSON 符号，您可以动态地创建一个对象，并向外界公开您想要的任何内容:

# 结论:超出一般用途

使用 NodeJS 模块非常有趣而且非常简单。练习一些导出，你很快就会掌握编写 NodeJS 定制模块。

您可能应该做一些“私有”配置，然后只导出模块内部的部分登录。这很常见，这使得 NodeJS 模块更加强大，适应性更强。编写代码的私有和公共部分是编写定制 NodeJS 模块的主要目标。 例如，在 mongose 中，您可以在模块内部定义模式，然后只将 mongose 模块导出到 main。

另一个很好的例子是编写一个 NodeJS 模块来共享 MongoDB 连接的实例。这比通过将连接放入 ExpressJS 请求(这是普遍实现的)来全球化或共享连接要好得多。

对于 ExpressJS 路由，我建议在不同的文件中使用 ExpressJS 路由器实例:只需查看官方文档。

定义许多 ExpressJS 路由对于大中型应用程序来说几乎是强制性的，你不能假装把它们都放在同一个 Javascript 文件中！