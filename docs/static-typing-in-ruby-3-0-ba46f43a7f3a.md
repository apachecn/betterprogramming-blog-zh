# Ruby 3.0 中的静态类型

> 原文：<https://betterprogramming.pub/static-typing-in-ruby-3-0-ba46f43a7f3a>

## 了解 Ruby 的下一步发展

![](img/70b5897ef047dc10425f446b2bebac7f.png)

Joshua Fuller 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

本周， [Ruby](https://www.ruby-lang.org/en/) 的开发团队发布了他们期待已久的 3.0 版本主要更新的第一个预览版！

在无数很酷的变化和增加中——包括一些奇妙的总体性能提升和一个强大的新并发/并行处理效率模型——一种新的内置语言已经出现，用于静态类型定义: [Ruby (Type) Signature language，或 RBS](https://github.com/ruby/rbs) 。

# 静态打字

Ruby 是一种[动态类型语言](https://en.wikipedia.org/wiki/Type_system#Static_and_dynamic_type_checking_in_practice)，这意味着解释器在运行时试图推断变量和对象属性的数据类型。这通常导致程序更加动态，更容易(更快)编码，解释器/编译器加载代码更快。然而，对类型推断的需求及其相关成本，以及在编译时用动态类型代码检测错误的困难，已经导致现代编程趋势转向静态类型语言。

静态类型使开发体验更加流畅。bug 更容易检测，尤其是在你写的时候用现代的 ide 和它们的林挺。对于大多数静态类型的语言来说，它们更显式的本质导致编译更快的代码(更有效的机器代码)。)

流行的动态类型语言也开始效仿。Python 的可选静态类型定义的[类型库](https://docs.python.org/3/library/typing.html)自从在 Python 3.5 中引入以来，已经获得了巨大的吸引力。[微软的静态类型 JavaScript 超集类型脚本](https://www.typescriptlang.org/)在最近几年里迅速流行起来，基本上已经成为行业标准。

# Ruby 中的类型

在即将到来的 3.0 版本中，Ruby 还计划集成可选的静态类型！

开发人员在新版本预览版中创建了一个名为 RBS 的“[基础类型签名语言](https://developer.squareup.com/blog/the-state-of-ruby-3-typing/)”gem。新的`.rbs`文件类似于[打字稿的](https://www.typescriptlang.org/docs/handbook/declaration-files/templates/module-d-ts.html) `[.d.ts](https://www.typescriptlang.org/docs/handbook/declaration-files/templates/module-d-ts.html)` [类型定义文件](https://www.typescriptlang.org/docs/handbook/declaration-files/templates/module-d-ts.html)。这些文件包含所有不同模块的类型定义。通过将它们保存在单独的文件中，您实际的 Ruby 代码不需要更改，并且您可以快速方便地将静态类型的好处添加到您的 Ruby 程序中！

除了传统的类型定义语法(看起来很像你对 Ruby 版本的期望), RBS 还实现了更安全的“引导式” [Duck Typing、](https://en.wikipedia.org/wiki/Duck_typing)“联合类型”和“方法重载”接口，以应对非统一的 Ruby 类型，以及其他一些很酷的特性。

# 冰糕

RBS 旨在成为 Ruby 中静态类型的标准化基础。[Ruby 中当前打字解决方案的领导者是 Sorbet](https://sorbet.org/) ，Ruby 的新版本仍然支持它。 [RBS 与 Sorbet 一起开发，](https://sorbet.org/blog/2020/07/30/ruby-3-rbs-sorbet)Sorbet 是实施 RBS 文件的高级类型检查器。

# 结论

随着在大型可伸缩应用程序中采用静态类型的行业趋势，Ruby 3.0 中 RBS 的加入是一个令人兴奋的进步。考虑到打字带来的开发体验效率的提高，并将其与 Ruby 3.0 中运行时性能的大幅提升相结合，我认为 Ruby 在软件行业的前景非常光明。

# 资源

[](https://github.com/ruby/rbs) [## ruby/rbs

### RBS 是一种描述 Ruby 程序结构的语言。你可以写下一个类或模块的定义…

github.com](https://github.com/ruby/rbs)  [## Ruby 3.0.0 预览版 1 发布

### 我们很高兴地宣布 Ruby 3.0.0-preview1 的发布。它引入了一个数字…

www.ruby-lang.org](https://www.ruby-lang.org/en/news/2020/09/25/ruby-3-0-0-preview1-released/) [](https://developer.squareup.com/blog/the-state-of-ruby-3-typing/) [## Ruby 3 打字的状态

### 我们很高兴宣布 Ruby 3 的新类型签名语言 RBS。Ruby 3 的一个长期目标是…

developer.squareup.com](https://developer.squareup.com/blog/the-state-of-ruby-3-typing/) [](https://sorbet.org/blog/2020/07/30/ruby-3-rbs-sorbet) [## Ruby 3、RBS 和冰糕中的类型

### 昨天 Square 在他们的博客上发布了一篇文章，介绍 RBS (Ruby Signature)，这是 Ruby 3 的一种类型语法格式…

sorbet.org](https://sorbet.org/blog/2020/07/30/ruby-3-rbs-sorbet)