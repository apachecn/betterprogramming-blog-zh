# 用 Ruby: Send 和 Public Send 方法进行元编程

> 原文：<https://betterprogramming.pub/metaprogramming-with-ruby-send-and-public-send-methods-f85aa8f91366>

## 开始写动态代码

![](img/49d57546dbeb429cde154ab7943bc9cb.png)

来自 [Pexels](https://www.pexels.com/photo/woman-sitting-on-the-floor-using-a-laptop-4050299/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的 [Vlada Karpovich](https://www.pexels.com/@vlada-karpovich?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的照片。

编写动态代码有时会很困难。幸运的是，Ruby 支持元编程，它允许你编写代码来为你动态地编写更多的代码。

在创建类或方法并动态调用它们时，元编程是一种强大的技术，这也使您的代码变得枯燥。使用元编程的缺点是，它比编写普通代码要昂贵得多，并且在我看来，只有在没有其他方法来解决问题或完成您想要的事情时，您才应该使用元编程。

在本文中，我们将讨论如何在 Ruby 中使用`send`和`public_send`方法。

首先，我们来了解一下这两种方法是做什么的。

# 发送

此方法将字符串或符号作为参数，并将该消息发送给对象。`send`方法的第二个参数代表该方法接收的任何参数。

让我们看一个例子:

```
full_name = "Mia Smith Jr."
full_name.send("count", "i") # 2
full_name.send(:upcase) # "MIA SMITH JR."
```

在这个例子中，我们声明了一个变量并指定了字符串`“Mia Smith Jr.”`。然后我们使用`send`方法来执行字符串中的`count`和`upcase`方法。由于`count`方法将我们想要计数的字符作为参数，我们可以将它作为第二个参数传递给`send`方法。

如果你想知道这是如何发生的，答案很简单。`send`方法接受我们传递的第一个参数，并在对象上执行该方法。如果对象响应该方法，那么输出将是成功的。否则，会产生错误。

我们可以使用`respond_to?`方法检查一个对象是否响应了一条消息。这是预先检查一个对象是否能处理特定消息的好方法。

例如，我们可以确认一个字符串是否像这样响应`split`方法:

```
full_name.respond_to?(:split) # true
```

正如我之前提到的，我们可以向`send`和`public_send`方法传递一个字符串或一个符号。但是只要有可能，最好使用符号，因为它们是字符串的优化版本。

# 公共发送

这个方法做的事情和`send`方法完全一样。然而，微小的区别是它只调用公共方法——不调用任何私有或受保护的方法。

让我们看一个如何使用`public_send`方法的例子:

在这个例子中，我们有两个类:一个`device`类和一个`device reporter`类。我们初始化一个新的`device`，并将该实例传递给`device reporter`。然后，reporter 类使用`public send`方法向`device`对象发送消息，在本例中是`tech_details`方法。因为`device`实例确实响应了这个消息，所以它不会抛出错误。它将只返回方法中定义的散列。

所以正如你所看到的，我们在`device`实例上调用了一个公共方法。如果我们使用`public_send`方法调用一个受保护的或者私有的方法(例如`total_price`方法)，它将会给我们一个错误。原因是创建`public send`方法是为了确保我们没有违反另一个对象的内部细节(也就是说，我们没有调用任何我们不应该调用的方法)。

这是一个很好的实践，因为我们希望我们的对象保持它们的细节是私有的，并封装所有不应该向其他对象公开的内容。

# 为什么你应该使用这些方法

这就是`send`和`public_send`方法的主要区别。但是用这两种方法有什么好处呢？答案很简单:它允许我们动态地向对象发送消息。

让我们看看如何使用`send`方法来实现这一点:

在上面的例子中，我们有一个`book service`类，它在构造函数中接收一些参数，并有一个公共方法。在这种情况下，我们使用的是`send`方法，而不是`public_send`方法，因为我们想要向`self`发送消息。这意味着我们想要执行一个属于同一个类的方法，所以我们不必使用`public_send`方法，因为我们可以调用`self`的任何私有或受保护的方法。

在 call 方法中，我们根据在构造函数中收到的过滤器动态地向`self`发送消息。这样，我们可以避免使用`if`语句，代码看起来非常干净。

现在，让我们看另一个使用`public_send`的例子:

在这个例子中，我们有一个`notifier service`类，它接收构造函数中的`notifier_type`、`message`和`user`作为参数。然后它有一个`notify`公共方法，首先调用`user_fields`私有方法并将结果赋给`fields`全局变量。

在`user_fields`方法中，我们迭代一个包含一组用户属性的常量。对于每个属性，我们使用`public_send`方法将消息动态发送给用户对象，并将该属性分配给散列。这将产生用户属性的散列。

然后，`notify`方法中的下一行再次使用`send`来根据我们收到的`notifier_type`参数执行正确的方法。

在这之前，一切看起来都很好。现在来看看`User`班。

用户类在构造函数中取`name`、`last_name`和`age`。它有一个公共方法和一个私有方法。这个类的重要部分是它只有针对`name`和`last_name`的访问器，而不是针对`age`。`Age`是一个私有的 reader 属性，这意味着它应该只对该类是可访问的，而不对其他对象公开。

如果我们照原样运行这段代码，它将产生一个错误，因为在`notifier service`中定义的常量包含了来自`user`对象的私有属性。所以，当迭代这个数组并试图使用`public_send`方法时，它会抛出一个错误，说这是一个私有方法，因此我们不能调用它。

这就是我发现`public_send`方法真正有用的地方。如果你因为某种原因忘记了`user`对象有某些属性或者方法被定义为 private，这个方法会告诉你，你在试图侵犯另一个对象的内部细节。

现在，要修复这个错误，我们可以使用我前面提到的`respond_to?`方法:

```
def user_fields
  USER_FIELDS.each_with_object({}) do |field, memo|
    memo["#{field}"] = user.public_send(field) if user.respond_to?(field)
  end
end
```

有了这一改变，即使我们向`user fields`常量添加私有方法和属性，它也不会抛出错误，因为我们在尝试对其使用`public_send`方法之前，会检查用户是否响应了该消息。

# 关键要点

*   当您想要在同一个类(`self`)中动态调用方法和属性时，请使用`send`方法。
*   当您想要动态调用其他对象的方法和属性时，请使用`public_send`方法。这确保您没有调用任何私有方法或属性。
*   当你想知道一个对象在实际发送它之前是否响应一个特定的消息时，使用`respond_to?`方法。

感谢阅读！我希望你已经发现这是有用的。

你可能会觉得这很有趣:

[](https://levelup.gitconnected.com/understanding-the-object-id-method-in-ruby-f61bb3888113) [## 理解 Ruby 中的 Object_id 方法

### Ruby 和许多编程语言一样，可以创建对象来存储属性、方法和执行…

levelup.gitconnected.com](https://levelup.gitconnected.com/understanding-the-object-id-method-in-ruby-f61bb3888113) [](https://medium.com/codestory/what-is-the-power-of-inversion-of-control-fd4953630444) [## 控制反转的力量是什么？

### Ruby 编程语言示例

medium.com](https://medium.com/codestory/what-is-the-power-of-inversion-of-control-fd4953630444) [](https://levelup.gitconnected.com/postgresql-views-with-ruby-on-rails-78260cd6f021) [## 使用 Ruby on Rails 的 PostgreSQL 视图

### 利用视图的力量封装复杂的查询。

levelup.gitconnected.com](https://levelup.gitconnected.com/postgresql-views-with-ruby-on-rails-78260cd6f021)