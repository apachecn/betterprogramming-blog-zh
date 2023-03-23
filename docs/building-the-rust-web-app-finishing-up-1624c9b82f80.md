# 构建 Rust Web 应用程序—完成

> 原文：<https://betterprogramming.pub/building-the-rust-web-app-finishing-up-1624c9b82f80>

## 使用 Rocket 后我学到了什么

![](img/c42bda1e9d5b35224a57db3102f6e303.png)

[费德里科·博托斯](https://unsplash.com/@landscapeplaces)在 [Unsplash](https://unsplash.com/) 上的照片

这是关于编写 web 应用程序的系列文章的第六部分，也是最后一部分。对于这个系列，我们将在 [Rust](https://www.rust-lang.org/learn/get-started) 中编写 web app，我自己来说明如何编写。

但是，如果您不愿意自己编写代码，我在这里[创建了一个包含本系列所有代码的库。在本系列的每一部分的结尾，我都提交了一个存储库。](https://github.com/garrettudstrand/rust-web-app/tree/main)[在前一部分](/building-the-rust-web-app-multiple-users-and-authentication-5ca5988ddfe4)中，我们处理了我们的应用程序不能处理多个用户的事实。在这一部分中，我们清理了前五部分的代码，并讨论了您可以做些什么来改进这个应用程序，以及您可以阅读或做些什么来提高您的 web 开发技能。

# 打扫

我不会为此做什么疯狂的事情，但是我们应该把代码分成多个文件。虽然我们可以为我们的代码分割做一些有趣的事情，或者我们可以使用类似 [Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html) 的东西进行更深入的研究，但是我们的项目并没有复杂到足以保证这一点。我们现在的`main.rs`变得太长了，很难读懂。首先，用下面的代码在`src`中创建一个名为`lib.rs`的文件:

然后，用下面的代码在`src`中制作一个名为`render_routes.rs`的文件:

然后，在`src`中创建一个名为`task_routes.rs`的文件，并包含以下代码:

接下来，用下面的代码在`src`中创建一个名为`user_routes.rs`的文件:

最后，转到`main.rs`，修改成如下图:

同样，如果你难以理解整个`mod`业务，或者 Rust 如何将代码分割成多个文件，我建议从 Rust book 中的[这一章。](https://doc.rust-lang.org/book/ch07-00-managing-growing-projects-with-packages-crates-and-modules.html)

有了它，我们的代码就更简洁了。我们的所有代码不是在一个大文件中，而是被分割在多个文件中，希望这些文件的名称不言自明。如果这将成为一些大型或复杂的项目，可能需要一个不同的组织，但是这里使用的组织对于我们的玩具例子来说是非常好的。

# 关于火箭的最后思考

这是实现一个基本的`todo`应用程序的漫长旅程。如果我坚持不使用 ORM，这可能会更容易。当您有大量的表和数据时，像 Sea ORM 这样的东西是有用的，但是对于像这样小的东西来说就不值得了。

我会更容易坚持使用数据库驱动程序，自己编写查询。过度设计一个解决方案通常会使解决方案更难使用，并让留下来处理您的工作的人非常恼火。

总的来说，Rocket 工作得非常好，尽管我在让 ORM 工作时遇到了一些困难。使用它建立一个服务器真的很简单，而且它的扩展性允许您随心所欲地使用该服务器。

我认为令人恼火的是，他们没有一些基本的组件来使用身份验证，并迫使您写出许多其他框架通常会帮助您避免的身份验证样板文件。像 [Laravel](https://laravel.com/) 这样的公司在减少认证的麻烦和痛苦方面做得更好。

我不确定转发请求守卫是否是正确的举动，如果他们没有被认证，重定向用户登录。如果防护失败，我找不到任何重定向的方法，所以我不得不一遍又一遍地重复相同的小片段，以重定向到每个必须被认证的单个路由的登录。

我想这样做的好处是路由的逻辑是显式的，所以如果我愿意的话，将来我可以把它修改得更好。但是如果我能定义一些默认的重定向，如果一个请求保护失败了，并且写转发覆盖了它，那就更好了，但是我想这可能很难实现。同样，也不清楚哪种设计决策更好。

被迫为每个路由编写重定向意味着任何给定的路由都可以有一个定制的重定向，因此定制、修改或扩展我们当前的重定向逻辑要容易得多。然而，这可以被称为违反[干](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)，如果你给他们这种说法，其他人会告诉你 [YAGNI](https://en.wikipedia.org/wiki/You_aren%27t_gonna_need_it) 。

我会在实际的 web 应用程序中使用它吗？也许吧，但是看起来我最终得到的代码比 Laravel 项目要冗长得多。我觉得我可以在用 Rocket 实现这个基本 CRUD 示例的时间内，用 Laravel 实现十次。

然而，在本教程中使用 Rust 之后，我已经爱上了它。整理错误并写在里面是一件令人兴奋的事情，但如果你试图在合理的时间内开发一个应用程序，我不确定 Rocket 是否是一个好的框架选择。也许我很慢，这是一个很好的选择。我还没有完全决定。

# 从这里去哪里

既然您已经对 web 开发的所有构件有了很好的了解，那么开始开发自己的 web 应用程序可能是个好主意。当你遇到问题时，你可能会用谷歌搜索它们，然后你可以开始填补我留在这里的知识空白。

如果你想更快地学习更多关于 web 开发的知识，请使用另一个 web 框架，如 [Laravel](https://laravel.com/) 或 [Spring](https://spring.io/) 。看到对这些概念和其他有用特性的略微不同的解释，可以让您对 web 开发领域有更多的了解。

然而，也许你只是想提高你的开发技能。在这种情况下，有大量的书籍可以帮助你增强代码编写技能。我推荐[干净代码](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)，[以任务为中心的用户界面设计](http://hcibib.org/tcuid/tcuid.pdf)，[代码完成，第二版](https://www.amazon.com/Code-Complete-Practical-Handbook-Construction/dp/0735619670)，还有[神话人月](https://www.amazon.com/Mythical-Man-Month-Software-Engineering-Anniversary/dp/0201835959)。

最后，如果我没有提到[设计模式:可重用的面向对象软件的元素](https://www.amazon.com/Design-Patterns-Object-Oriented-Addison-Wesley-Professional-ebook/dp/B000SEIBB8)，我会受到惩罚。我不确定设计模式是否真的如他们所吹嘘的那样，但是意识到它们会让你更好地与你的开发伙伴交流。你不应该教条地遵循这些书的建议，你也不需要从头到尾地阅读所有这些书，但是它们可以给你一些有用的想法，告诉你应该如何看待代码，以及“好代码”是什么样子的。它们对初学者尤其有用。

如果你想要提供更多具体信息来帮助你提高的书，我推荐 [Effective Rust](https://www.lurklurk.org/effective-rust/cover.html) 和 [Effective Java](https://www.amazon.com/Effective-Java-Joshua-Bloch/dp/0134685997) 。当然，好的代码是什么样子也不是最终的决定，但是它们给了你很多改进这两种语言代码的想法。

还有很多其他的资料，所以如果你对我在这里提供的资源不感兴趣，可以去谷歌一下，找到你自己认为有前途的技术资源。只要你致力于成为一名更好的开发人员，并真诚地采取措施来提升自己，你很快就会成为一名优秀的开发人员。

嗯，就这些。我希望我在这 21000 字的混乱中教会了你一些东西。信不信由你，web 开发中还有很多概念我在这里都没有涉及到。当谈到构建 web 应用程序时，有大量的知识，但希望我已经给了你足够的信息，这样你至少可以开始构建自己的 web 应用程序。

祝你好运，感谢你的阅读！

# 资源

*   【rust-lang.org】Rust 编程语言(T4)
*   [本系列的 GitHub 库](https://github.com/garrettudstrand/rust-web-app/tree/main)
*   [洁净建筑](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
*   [用包、板条箱和模块管理不断增长的项目 Rust 编程语言(rust-lang.org)](https://doc.rust-lang.org/book/ch07-00-managing-growing-projects-with-packages-crates-and-modules.html)
*   [Laravel——面向 Web 工匠的 PHP 框架](https://laravel.com/)
*   [不要重复自己——维基百科](https://en.wikipedia.org/wiki/Don't_repeat_yourself)
*   [你不会需要它——维基百科](https://en.wikipedia.org/wiki/You_aren't_gonna_need_it)
*   [春|家](https://spring.io/)
*   干净的代码:敏捷软件工艺手册:罗伯特·c·马丁:9780132350884:亚马逊:书籍
*   [以任务为中心的用户界面设计(hcibib.org)](http://hcibib.org/tcuid/tcuid.pdf)
*   [代码完整:软件构造实用手册，第二版:麦康奈尔，史蒂夫:0790145196705:亚马逊网站:书籍](https://www.amazon.com/Code-Complete-Practical-Handbook-Construction/dp/0735619670)
*   [神话人月:软件工程论文集，周年纪念版:Brooks Jr .，Frederick:8580001065793:Amazon . com:Books](https://www.amazon.com/Mythical-Man-Month-Software-Engineering-Anniversary/dp/0201835959)
*   Amazon.com:设计模式:可重用面向对象软件的元素电子书:Erich，Gamma，Helm Richard，Johnson Ralph，Vlissides John，Grady Booch: Kindle Store
*   [封面——有效防锈(lurklurk.org)](https://www.lurklurk.org/effective-rust/cover.html)
*   [有效的 Java: Bloch，Joshua:9780134685991:Amazon . com:Books](https://www.amazon.com/Effective-Java-Joshua-Bloch/dp/0134685997)