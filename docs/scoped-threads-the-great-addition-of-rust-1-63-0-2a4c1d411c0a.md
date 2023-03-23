# 引入作用域线程——Rust 1 . 63 . 0 的新增功能

> 原文：<https://betterprogramming.pub/scoped-threads-the-great-addition-of-rust-1-63-0-2a4c1d411c0a>

## 一瞥令人敬畏的更新

![](img/8bba813d9e4aaa568cb20f17caa963c2.png)

让蟹线横行

Rust 1.63.0 已经发布，这个版本包含了最近最期待的特性之一。毕竟，[横梁](https://crates.io/crates/crossbeam)的这种能力是用这个机箱而不是用标准库在 Rust 中实现多线程的主要原因之一。让我们看看新的作用域线程允许我们做什么。

# 我们曾经拥有的

下面的代码是一个相当基本的多线程实现。这可能是任何并行编程入门练习的一部分。

我们正在读取数字列表，并在两个不同的线程上计算该列表的两个统计数据。最后，我们等待两个结果并打印结果。

只读并在离开作用域之前加入，这应该是安全的吧？

那么，如果我们尝试运行这段代码会发生什么呢？我们得到如下所示的两个错误，我们生成的每个线程一个:

```
error[E0373]: closure may outlive the current function, but it borrows `numbers`, which is owned by the current function
 --> src/main.rs:7:28
  |
7 | std::thread::spawn(|| numbers.iter().sum::<i32>() as f32...);
  |                    ^^ ------- `numbers` is borrowed here
  |                    |
  |                    may outlive borrowed value `numbers`
  |
note: function requires argument type to outlive `'static`
 --> src/main.rs:7:9
  |
7 | std::thread::spawn(|| numbers.iter().sum::<i32>() as f32...);
  | ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
help: to force the closure to take ownership of `numbers` (and any other referenced variables), use the `move` keyword
  |
7 | std::thread::spawn(move || numbers.iter().sum::<i32>() as ...);
```

它说用线程调用的闭包可以比我们的引用活得长。它给出了使用`move`赋予线程引用的所有权的提示，这样我们就知道它不会因为它而存活。事情是这样的，我们希望在当前范围内继续使用我们的列表，并且我们还希望将它传递给另一个线程。我们可以只使用`Clone`,但是我们知道不应该需要它，那么为什么我们不能继续使用借用列表呢？

我们没有变异任何东西。而且我们知道，当`numbers`还在范围内时，我们正在使用连接。所以，我们知道这是安全的，应该行得通。为什么会这样？因为`std::thread::spawn`不知道范围，它假设我们借用的数字列表可能会在线程完成计算之前被丢弃。这就是为什么它需要一个`move`或`'static`的寿命。然而，现在我们可以用这些知识来生成线程。

# 我们现在能做什么

随着 Rust 版本今天的发布和这个特性的稳定，我们现在可以传递一个闭包给`std::thread::scope`来运行我们的线程。闭包将接收一个`Scope`作为第一个也是唯一的参数。我们可以用它来产生线程，而不是调用`std::thread::spawn`。我们产生的这些线程现在会意识到它们的范围。所以，上面的代码应该是这样的。

这运行得很好。如您所见，我们将线程的生成和连接移到了新的闭包中，并使用`scope`来生成这些线程。现在我们的代码知道线程确实会在这个范围内完成计算，所以它们不会比我们借用的数字活得长，这是一个更大范围的一部分。

# 另一个例子

最后，作为一个额外的我会让你在这里的另一个例子工作代码，这在以前是不可能的。Rust 1.63.0 公告中提供了这个例子，你可以在这里阅读详细的[。](https://blog.rust-lang.org/2022/08/11/Rust-1.63.0.html)

在这个例子中，`a`可以在两个线程中被借用，就像我们对`numbers`所做的那样，不需要编译器询问`'static` 的生存期，`x`也可以被借用，即使是可变的，它们都可以被使用，甚至在`std::thread::scope`之后发生变异。甚至不需要连接。这就是我所说的令人敬畏的更新。

[](https://github.com/kriogenia/medium/tree/main/scoped_threads) [## 中/主克里金处的作用域线程数/中

### 存放我在 Medium 帖子上使用的示例的存储库——Medium/scoped _ threads 位于 main kriogenia/medium

github.com](https://github.com/kriogenia/medium/tree/main/scoped_threads)