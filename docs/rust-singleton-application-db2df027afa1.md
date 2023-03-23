# Rust:单一应用程序

> 原文：<https://betterprogramming.pub/rust-singleton-application-db2df027afa1>

## 借用检查器与设计模式

大家好。先来说说 Rust 的一个让 Rust 开发者抓狂的概念:借货检查器。

![](img/40c5066b6e07cc39892c3095aca6a14b.png)

迈克尔·泽兹奇在的照片

但首先，让我解释一下这是什么，以及如果你想开始在 Rust 中开发，为什么你应该对这个主题感兴趣。

在其他编译语言中，比如 C++，你必须控制内存，也就是分配和释放动态内存。在 Java 等其他语言中，您不会遇到这个问题，因为垃圾收集器正在运行，并在后台监听未使用的内存以释放它。

Rust 让它“更简单”或者看起来更简单，因为当一个作用域终止时，Rust 会释放它。此外，当您将值传递给一个函数/方法(另一个作用域)时，这个作用域会获取变量属性，当它的作用域终止时，这个内存会被再次释放。让我们看一个例子:

因此，如果我们想在这些函数中重用`some_string`，我们必须切换到获取引用，而不是打印函数中的变量值。

或者再次返回值以不释放它。

借用检查器的其他规则可能会引发错误。例如，当传递对 struct 构造函数的引用时，借用检查器将引发错误，因为它不知道作为引用传递的 struct 或变量可以存在多长时间，因此编译器将强制您使用 lifetime。但是，请注意，这是一个陷阱。

> 借用检查器是避免内存泄漏的防护措施

我们可以说借用检查器是避免内存泄漏的卫士，内存泄漏会使您调试几个小时来找出偶尔的 bug。

# 设计模式

有一些设计模式，比如 Singleton，可能会被借用检查器所阻碍。这是因为，在单例策略中，您必须将结构的实例作为静态存储在结构中。

*Singleton 负责存储自身的全局实例，并被整个应用程序重用来存储全局变量，如配置或服务连接。*

理论上，单例是这样工作的。我们创建一个类/结构，它有一个自身的属性和一个创建自身实例并返回它的方法。

剧透:Rust 不让你创建这个有两个原因:

*   不实例化就不能使用结构中的属性。
*   借用检查器不允许您存储其自身的实例，因为它会将其检测为内存泄漏。

这也是 Rust core 开发者为此设计一些工具的原因。

# **引用计数(又名 Rc)结构**

这种结构允许您通过弱引用在不同的域之间共享引用。它是如何工作的？

这使得代码可以编译，因为 Rc 结构创建了一个弱引用作为属性，告诉编译器在编译时不需要检查。问题是我们在创建属性后没有使用它。所以，在下一次调用中，它会再次创建它。

然而，我们可以使用这个工具将数据结构化为动态列表，而不是单一列表。

*这只在本地线程中有效。如果你需要更多的线程，我推荐你使用 Arc。这个特征实现了克隆特征，用 threadsafe 克隆引用。*

**互斥**

它将我们的值存储为可变的，但会阻塞资源，直到有人离开对该值的引用。例如，如果我们有一个类型为`u8`的变量，即使它是一个原始类型，它也会使它可变。

那么，为什么还是得不到实际值呢？嗯，在`get_instance`中，我们每次调用时都实例化单例结构，而不使用我们实际的`instance`属性。所以，我们必须从全球任何一个国家。

有什么问题？静态变量不能是可变的，因为 Rust 认为你可以创建竞争条件，所以它强迫你使用不安全的作用域。

**线程 _ 本地！**

这个宏允许创建静态变量，而不需要非常数值处理。这里有一个例子:

```
#[derive(Default)]
```

这个派生使我们的结构实现了默认的特征。trait 实现 Rust 的主要和非主要默认值。所以，我们现在不用担心违约。

```
thread_local! {
 static SINGLETON_POOL: Arc<Singleton> = Arc::new(Default::default());
}
```

创建我们的 Singleton 的静态默认值。此外，我们将 Rc 更改为 Arc(原子引用计数)，这允许我们克隆引用。

```
pub fn get_instance() -> Arc<Singleton> {
 SINGLETON_POOL.with(|singleton_pool| singleton_pool.clone())
 }
```

`get_instance`方法现在返回单例的全局状态。瞧，*，*我们让我们的单例工作了。

# 结论

像其他语言一样创建单例并不是一个好主意。也许更简单的方法是将变量存储在父状态中，并在需要这种配置的组件之间共享它。例如，Actix web 框架实现了一个将在每个请求中传递的状态。

# **资源**

*   [借入审核人单据](https://doc.rust-lang.org/book/ch04-02-references-and-borrowing.html)
*   [借 checker 博客](https://blog.logrocket.com/introducing-the-rust-borrow-checker/)
*   [Rc 文档](https://doc.rust-lang.org/stable/std/rc/struct.Rc.html)
*   [Arc docs](https://doc.rust-lang.org/std/sync/struct.Arc.html)
*   [互斥文件](https://doc.rust-lang.org/std/sync/struct.Mutex.html)
*   [线程 _ 本地宏文件](https://doc.rust-lang.org/std/macro.thread_local.html)
*   [Actix web app 状态参考](https://docs.rs/actix-web/latest/actix_web/struct.HttpRequest.html#method.app_data)

感谢阅读；希望你喜欢！我很想听听你对设计模式或 Rust 的想法和经验。

在 [LinkedIn](https://www.linkedin.com/in/%C3%A1ngel-berh%C3%B3-grande/) 上留言或联系我。