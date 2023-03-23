# 《铁锈》中的变量和不变性

> 原文：<https://betterprogramming.pub/quick-look-into-variables-and-immutability-in-rust-writing-safe-code-from-the-start-b31635478779>

## 从一开始就编写安全代码

![](img/d9161f75de75c677d89f6f6a02c9b0dd.png)

Rust 原则——不变性和可预测行为

本文讨论了变量和 Rust 语言中与可变性相关的一些概念。默认情况下，Rust 中的所有变量都是不可变的。我们将看看可变性，以及这些约定如何让我们这些程序员受益。我们还将看到编译器如何帮助我们确保程序的正确性。

考虑下面的例子

```
#[test]
**pub fn** test_create_new_event_in_different_scope() {
    **let** event_name = **"user_logged_in"**.to_string();
    println!(**"Original event name is {} "** , event_name)
}
```

在这个例子中，我们考虑一个字符串。字符串很特殊，因为它们不是基本类型，需要堆空间来存储。在现实世界中，当处理内存问题或共享状态时，人们会更频繁地处理结构或复杂对象。

在 Rust 中，任何改变变量名的尝试都会导致编译器错误。试试下面的例子

```
#[test]
**pub fn** test_create_new_event_change_name() {
    **let** event_name = **"user_logged_in"**.to_string();
    event_name = **"Hello_world"**.to_string(); 
    println!(**"Original event name is {} "** , event_name)
}
```

编译这个程序会抛出一个错误。

```
 |
41 |         let event_name = "user_logged_in".to_string();
   |             ----------
   |             |
   |             first assignment to `event_name`
   |             help: consider making this binding mutable: `mut event_name`
42 |         event_name = "Hello_world".to_string();
   |         ^^^^^^^^^^ cannot assign twice to immutable variable
```

在任何情况下，错误消息都是极好的。在上面的例子中，修改一个字符串变量需要程序重新分配内存和移动一些引用。如果我们想改变它，我们必须声明它是可变的。这不仅限于字符串和对象。甚至像`u32`、`i64`、`floats`这样的原始类型也有同样的限制。

让我们再看几个例子，我们想变异一个变量..

# 可变变量

```
#[derive(Deserialize, Debug)]
**pub struct** Event {
    **pub client_id**: ClientIdentifier,
    **pub name**: String,
    **pub data**: HashMap<String, Value>,
}#[test]
**pub fn** test_create_new_event_update_data() { **let** event_name = **"user_logged_in"**.to_string();
    **let** client_id = **"test_client"**.to_string();
    **let** new_event = Event { **name**: event_name, **client_id**: client_id, **data**: HashMap::*new*() };
    new_event.**data**.insert(**"device_model"**.to_string(), Value::*String*(**"iPhone"**.to_string()));
    println!(**"Event name is {} "**, &new_event.**name**);
}
```

考虑这个例子。对于任何接触过 Java、Python 或其他高级编程语言的人来说，这是一个非常简单的操作。一旦我们运行测试，我们期望用新的键/值对更新`HashMap`。

在 Rust 的情况下，这个程序编译失败。这是我们将会看到的错误信息。

```
error[E0596]: cannot borrow `new_event.data` as mutable, as `new_event` is not declared as mutable
  --> analytix/core/src/models.rs:40:9
   |
39 |         let new_event = Event { name: event_name, client_id: client_id, data: HashMap::new() };
   |             --------- help: consider changing this to be mutable: `mut new_event`
40 |         new_event.data.insert("device_model".to_string(), Value::String("iPhone".to_string()));
   |         ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ cannot borrow as mutable
```

**插入** `**HashMap**` **是一个更新操作**。**数据被修改并插入其内部结构**。当这种情况发生时，我们必须明确告诉 Rust 我们打算在测试中更新`HashMap`。为了解决这个问题，我们使用一个`mut`关键字重新声明我们的`HashMap`。

```
**let mut** new_event = Event { **name**: event_name, **client_id**: client_id, **data**: HashMap::*new*() };
```

关键字告诉编译器一个变量将会有一个可变的操作。无论我们处理哪种数据类型，`mut`关键字适用于一切。

# 可变函数

在上面的例子中，我们将变量标记为可变的。当我们更新一个`HashMap.`时，我们知道不仅仅是我们的变量被更新了，而且`HashMap`本身也发生了变异。

当插入数据时，内部结构需要计算散列，检测冲突，并将值插入正确的桶中。这需要函数告诉开发人员可变操作也存在于内部。看看下面 HashMap 的函数签名:

```
#[inline]
#[stable(feature = **"rust1"**, since = **"1.0.0"**)]
**pub fn** insert(&**mut self**, k: K, v: V) -> Option<V> {
    **self**.**base**.insert(k, v)
}
```

这个定义是标准 Rust 库的一部分。它清楚地说明，任何试图在 HashMap 中插入键的调用者都必须传入一个对`HashMap`本身的可变引用来更新它。像变量声明一样，Rust 函数/方法声明也希望开发人员这样做。否则，当我们试图编码我们的程序时，我们会看到很多编译错误。

# 有什么帮助？

通过在代码的每个部分定义可变性限制，我们从 Rust 获得的一个巨大好处是它的编译器能够检测我们代码中的数据竞争、内存泄漏和漏洞。上面的例子非常简单。但是在更复杂的应用程序中，多线程可能会改变数据，我们真的不希望在处理这些时出现意外的行为。铁锈阻止我们犯这样的错误和行为。

就个人而言，它帮助我们以一些额外的代码为代价编写可预测的程序。但是这个方面可能会节省我们在检测和修复代码问题上的时间，甚至是几天。和不变性通常是一种好的实践！

我不建议每个用例都用 Rust。在决定框架/语言之前，必须选择并仔细分析开发和系统的所有方面。但是理解 Rust 的原则会让每个开发人员想到正确的方法和原则来编写干净、可预测和高性能的代码。