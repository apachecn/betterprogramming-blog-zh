# 解释 Rust 的模块

> 原文：<https://betterprogramming.pub/explaining-rusts-modules-420d38eed6c5>

## 一劳永逸地回答“我们应该把这些文件放在哪里？”

![](img/310fd50d8a9dd769f54b8bdb8e44de4a.png)

照片由[兰迪·法特](https://unsplash.com/@randyfath?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/different?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 这是不同的

当从 [Rust](https://www.rust-lang.org/) 开始并需要扩展到项目子文件夹中的其他文件时，人们会很快被 Rust 的模块概念绊倒。

无论我们使用何种技术或编程语言，我们都倾向于将我们的想法和概念组织在目录和文件中，以便在项目加速和扩展时保持有价值的概述，封装知识，并创建强大的一致性。

在 Rust 项目中创建这些文件夹，它很快就击中了要害:这里的情况非常不同。一个基于 C 语言的程序员倾向于导入文件，以在自己的范围内获得相应的类型或名称空间。通过 IDE 的上下文帮助或搜索互联网，我们了解到 Rust 是在模块中思考，而不是在文件中。

Rust 与模块一起工作，作为模块树的一部分。没有文件导入这回事。

# 生锈的模块

Rust 模块系统中的重要概念是*包*、*箱*、*模块、*和*路径*。本文主要讨论在多个文件中定义的模块和路径，以及如何将分割的部分组合在一起。

## 创建基础项目

在一个人最喜欢的 IDE(或终端编辑器)中从一个模板创建一个新项目通常是进入新项目的第一步，Rust 项目可能看起来像这样:

![](img/f3afbb4b8512155bd23b28cfdd94fec6.png)

我们的项目包含 src 和(构建生成的)目标文件夹，以及 git 和 Cargo 文件。图片由[https://doc . rust-lang . org/book/ch07-00-managing-growing-projects-with packages-crates-and-modules . html # managing-growing-projects-with packages-crates-and-modules](https://medium.com/u/e87e74fe43b2#managing-growing-projects-with-packages-crates-and-modules)

[https://doc . rust-lang . org/book/ch07-02-defining-modules-to-control-scope-and-privacy . html](https://doc.rust-lang.org/book/ch07-02-defining-modules-to-control-scope-and-privacy.html)

【https://doc.rust-lang.org/std/keyword.pub.html 

⁴[https://doc . rust-lang . org/book/ch07-03-paths-for-reference-to-an-item-in-the-module-tree . html](https://doc.rust-lang.org/book/ch07-03-paths-for-referring-to-an-item-in-the-module-tree.html)

https://doc.rust-lang.org/std/keyword.mod.html[⁵](https://doc.rust-lang.org/std/keyword.mod.html)

https://doc.rust-lang.org/std/keyword.use.html⁶