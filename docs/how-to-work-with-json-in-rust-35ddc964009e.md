# 如何在 Rust 中使用 JSON

> 原文：<https://betterprogramming.pub/how-to-work-with-json-in-rust-35ddc964009e>

## 用 Rust 学习读写无类型或强类型的 JSON

![](img/40164b9fc67f72d5f986077a4ed35403.png)

照片由[沙哈达特·拉赫曼](https://unsplash.com/@hishahadat?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JSON 现在被广泛使用，即使 Rust 可能不是使用 JSON 作为数据的工具和应用程序使用最多的语言，但在 Rust 这样一种快速发展的语言中，知道如何对待这种格式是很重要的。

在这篇小文章中，您将学会:

*   读取无类型的 JSON。
*   将 JSON 作为强类型数据结构读取。
*   写 JSON 字符串。

全部只使用了`serde`和`serde-json` Rust 依赖项。

# 非类型化 JSON

Rust 是一种强类型语言，JSON 是一种不指定其值的类型的数据格式(就其本身而言)。如果我们不想或者不关心接收具有精确结构和类型的 JSON 数据，那么将 JSON 作为递归枚举读取是由`[serde_json](https://docs.serde.rs/serde_json/value/enum.Value.html)`库提供的一种数据结构。这个结构接受布尔值、字符串、数字、数组和对象(以及空值)，因此任何有效的 JSON 都是可以接受的。

让我们看看在通过添加所需的 dep 来设置我们的 cargo 项目(`cargo new handle_json`)之后它是如何工作的:

```
serde_json = "1.0"
serde = { version = "1.0", features = ["derive"] }
```

为了处理非类型化的 JSON 值，我们将使用由`serde_json`依赖项提供的`from_str()`函数。此外，我们将需要使用我之前谈到的 Enum 表示来解析值。

这里的解析是由`read_json`函数完成的，该函数将一个字符串文字作为参数，然后由`serde_json::from_str()`解析并展开(因为它是一个结果)。您可以看到，我们可以像使用大多数其他语言一样访问 JSON 中的字段:`parsed["article"]`。

# 类型化 JSON

对于大量的用例，我们可能希望在程序交互的数据上实施安全类型。Serde 提供了一种很好的将 JSON 数据映射到 rust 结构的方法。这种方法类似于我们刚刚看到的方法，但是我们不是将 Enum 表示指定为 read JSON 的类型，而是指定一个本地 rust 数据结构。

每个实现 serde 的反序列化特性的类型都可以被反序列化，并用于检查接收到的 JSON 数据是否与提供的结构的类型和名称匹配。这做起来容易，说起来难，所以我们来举个例子。

您可能已经注意到的第一个区别是，我们定义了两个实现 serde 的(反)序列化特征的结构。其次，我们已经为读取的 JSON 数据指定了`Article` struct 作为类型。第三，您会注意到我们并没有将解析后的变量作为枚举进行索引(就像我们之前对`parsed[""]`所做的那样)，而是将其作为`struct` : `parsed.paragraph[0].name`进行索引。

正如您从结构中看到的，我们希望我们的结构有非常具体的类型和名称。让我们看看如果提供的 JSON 数据与结构的类型(和名称)不匹配会发生什么，如下所示:

```
{
  "article": "how to work with json in Rust",
  "author": "tdep",
  "paragraph": [
    {
      "name": 1
    },
    {
      "name": "strongly typed"
    },
    {
      "name": "writing json"
    }
  ]
}
```

如果我们使用这个 JSON 数据(注意，第一段的名称的值是一个数字)，程序将会出错:

```
thread 'main' panicked at 'called `Result::unwrap()` on an `Err` value: Error("invalid type: integer `1`, expected a string", line: 8, column: 15)', src/main.rs:44:58
```

现在，让我们试着用键`"name"`改变键`"article"`:

```
{
  "name": "how to work with json in Rust",
  "author": "tdep",
  "paragraph": [
    {
      "name": "untyped"
    },
    {
      "name": "strongly typed"
    },
    {
      "name": "writing json"
    }
  ]
}
```

程序再次出现混乱:

```
thread 'main' panicked at 'called `Result::unwrap()` on an `Err` value: Error("missing field `article`", line: 17, column: 1)', src/main.rs:44:58
```

如您所见，Rust 未能检测到 JSON 中的文章字段。

如果您希望对接收的数据有更多的控制，这是一个很好的方法，尤其是当您使用用户输入的 JSON 时。

现在让我们快速看看如何反过来:从 Rust 数据结构到 JSON 字符串。

# 编写 JSON

我们将使用`serde_json::to_string()`函数将数据结构转换成 JSON 字符串，并使用 serde 的`Serialize`特征使该结构能够被序列化。让我们看一个例子:

我们只是构建文章(根据定义的结构)，然后将其引用传递给`serde_json::to_string()`函数。正如我们所料，这是运行 cargo 项目的结果:

```
the JSON is: {"article":"how to work with json in Rust","author":"tdep","paragraph":[{"name":"untyped"},{"name":"strongly typed"},{"name":"writing json"}]}
```

我们已经看到了如何在 Rust 中快速、安全、高效地处理 JSON。同样值得一提的是，我们用`serde_json::from_string`或`serde_json::to_string`完成的每一个操作，也可以用`[serde_json::to_vec](https://docs.serde.rs/serde_json/ser/fn.to_vec.html)`、`[serde_json::to_](https://docs.serde.rs/serde_json/ser/fn.to_vec.html)writer`完成，结果不同:`to_vec`序列化(或反序列化)为一个向量，`to_writer`为任何可写输出(例如一个文件)。

感谢阅读！