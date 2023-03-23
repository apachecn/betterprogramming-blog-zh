# 在 C#中实现工厂设计模式的 5 种方法

> 原文：<https://betterprogramming.pub/5-ways-to-implement-factory-design-pattern-in-c-382c0992a3ff>

## 静态、异步、参数化、内部和抽象工厂

![](img/cf0a92e00c38aadebc41d0a92ba1bead.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上用高清的[科普拍照。](https://unsplash.com/@scienceinhd?utm_source=medium&utm_medium=referral)

对象可以通过调用`new`关键字直接实例化其他对象，但是这种方法通常不是最佳选择。

实例化对象是一项独立的责任，应该从客户端代码转移到称为工厂的独立类/方法。

![](img/e9f78c358e560cc70349e6768fd50372.png)

使用工厂设计模式有以下好处:

*   客户端代码少了一项职责，因为它不需要创建对象。这是工厂的责任。
*   工厂封装了创建可被许多客户机重用的对象的逻辑。

工厂模式有许多变体，每一种都解决不同的问题。

# 1.静态工厂方法

构造函数与声明它的类同名。如果类中有多个重载构造函数，它们将具有相同的名称。

需要用多个重载构造函数实例化一个类的客户端代码，如果不检查类的实现细节，可能不知道调用哪个构造函数。

此外，当构造函数需要相同数量的相同类型的参数时，有时它们根本不起作用。

以下代码无法编译:

```
public struct TimeSpan
{
    public TimeSpan(double seconds)
    {
    }        public TimeSpan(double minutes)
    {   
    }
}
```

静态工厂方法是构造函数的一个很好的替代方法，因为它们有一个惟一的名称，可以准确地描述返回实例的用途。

客户端代码可以通过读取工厂方法的名称来判断要调用哪个工厂方法，甚至不需要查看类代码。

# 2.异步工厂方法

构造函数的一个限制是它不能用关键字`async`标记。因此，如果需要，无法在构造函数中等待异步调用。

当您需要执行一些异步操作来创建对象时，异步工厂方法有助于绕过这个构造函数限制。

注意，这个构造函数是私有的，所以创建对象的唯一方法是调用`CreateNewAsync`方法。

# 3.参数化工厂方法

参数化工厂方法基于输入参数创建并返回特定类型的对象。

工厂方法可以使用字典(表驱动方法)、`switch`表达式或简单的`if-else`语句来实现。

# 4.内部工厂

在决定将工厂方法放在哪里时，开发人员有几种选择。

## 在原来的班级里

最直接的方法是将工厂方法直接放入它们创建的类中。

当静态工厂方法简单并且包含几行代码时，通常选择这种方法。

将静态工厂方法放在原始类中的缺点是，它违反了单一责任原则，因为该类除了它的主要责任之外，还知道如何创建自己。

优点是凝聚力高。工厂方法尽可能靠近它们实例化的类。

## 在单独的班级里

静态工厂方法的另一个位置是一个单独的类。

这种方法解决了单一责任的问题，但是出现了另一个更严重的问题。`Term`类的构造函数现在必须是公共的。否则，工厂方法无法创建`Term`实例。

同时拥有公共构造函数和工厂方法使得需要实例化类的客户很难正确使用代码，因为他们不得不在两个可用选项之间做出选择。

## 在内层

内部工厂有助于减少前两种方法的缺点。

内部工厂是一个内部静态类，可以访问外部类的私有构造函数，因此不再需要公共构造函数。

内部工厂模式用于。NET 内置类型。例如，启动新任务的选项之一如下所示:

```
await Task.Factory.StartNew(() =>
{
    //Code
});
```

# 5.抽象工厂

抽象工厂提供了创建依赖或相关对象的接口。

抽象工厂可以使用适当的抽象类或接口来实现。

```
**//Abstract factory**
public abstract class DbProviderFactory
{
    public abstract DbConnection CreateConnection();
    public abstract DbCommand CreateCommand();
}**//Concrete factory**
public class SqlClientFactory : DbProviderFactory
{
    public override DbConnection CreateConnection() 
        => new SqlConnection();

    public override DbCommand CreateCommand() 
        => new SqlCommand();
}**//Concrete factory**
public class PostgreSqlClientFactory : DbProviderFactory
{
    public override DbConnection CreateConnection() 
        => new PostgreSqlConnection(); public override DbCommand CreateCommand() 
        => new PostgreSqlCommand();
}
```

客户端代码应该只依赖于使用以下方法的`DbProviderFactory`抽象工厂:`CreateConnection`、`CreateCommand`等等。

要让客户机代码与不同的数据存储一起工作，您只需要更改 DI 容器中的特定实现。

# 结论

实现工厂设计模式的选项列表可能不完整，因为封装了创建对象的逻辑的每个类都可以称为工厂。

我们通过最常用和最有趣的方式来实现这个模式(从我的角度来看)。欢迎对工厂模式的实现提出任何其他想法。

# 我的其他文章

[](https://levelup.gitconnected.com/how-to-professionally-to-do-a-code-review-of-a-bug-fix-f17de72d42e0) [## 如何专业地对 Bug 修复进行代码审查

### 审查 bug 修复时要问的几个重要问题。

levelup.gitconnected.com](https://levelup.gitconnected.com/how-to-professionally-to-do-a-code-review-of-a-bug-fix-f17de72d42e0) [](https://levelup.gitconnected.com/you-are-simply-injecting-a-dependency-thinking-that-you-are-following-the-dependency-inversion-32632954c208) [## 你只是简单地注入一个依赖，认为你在遵循依赖倒置…

### 澄清差异。

levelup.gitconnected.com](https://levelup.gitconnected.com/you-are-simply-injecting-a-dependency-thinking-that-you-are-following-the-dependency-inversion-32632954c208) [](https://levelup.gitconnected.com/how-to-fix-bugs-and-not-introduce-new-ones-9f35e625673a) [## 如何在不破坏应用程序的情况下修复 Bug

### 更改源代码时更自信的步骤。

levelup.gitconnected.com](https://levelup.gitconnected.com/how-to-fix-bugs-and-not-introduce-new-ones-9f35e625673a)