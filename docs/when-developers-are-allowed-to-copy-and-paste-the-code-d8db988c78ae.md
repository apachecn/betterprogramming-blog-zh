# 什么时候应该复制粘贴代码？

> 原文：<https://betterprogramming.pub/when-developers-are-allowed-to-copy-and-paste-the-code-d8db988c78ae>

## 澄清何时允许开发者复制粘贴

![](img/9ce6ea940035215ef10feca5b1abc1ae.png)

Photo by [浮萍 闪电](https://unsplash.com/@vsmilelx?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

复制和粘贴代码通常被认为是反模式的，甚至是绝对邪恶的。然而，复制和粘贴只是一种工具，开发人员可以不恰当地使用它，对代码库造成重大损害，或者可以恰当地使用它，在不损害代码质量的情况下加快开发速度。

让我们试着弄清楚什么时候复制和粘贴代码是正当的，什么时候不是。

# 一些现有的代码只是新代码的模板

每个大型项目的代码库可能包含许多重复的代码块，从一行到几行不等。重复代码绝对没有问题，除非更改一个代码块需要对其他相似或相同的代码块进行相同的更改。

假设一个开发人员收到了下面的简单文件，其中有两列(`Name`和`IsDeleted`)用于解析数据并将其加载到内存中:

```
**users.txt**Username, 0
UserName, 0
Username, 1
```

开发人员可以实现类似这样的东西来完成这项任务:

```
public UserFileParser
{
  public IEnumerable<User> Parse(string path)
  {
    StreamReader reader = File.OpenText(path); string line;
    while ((line = reader.ReadLine()) != null)
    {
       string[] values = line.Split(',');
       yield return new User(values[0], Convert.ToInt32(values[1]));
    }
  }
}public class User
{
    public User(string name, bool isDeleted)
    {
        Name = name;
        IsDeleted = isDeleted;
    } public string Name { get; set; }
    public bool IsDeleted { get; set; }
}
```

第二天，开发人员得到了另一个要解析的文件。经过快速调查，开发人员发现两个文件模式是相同的，这意味着解析逻辑将与已经存在的非常相似。

```
**admins.txt**Adminname, 1
Adminname, 0
Adminname, 1
```

开发人员现在有两种方法来完成新任务。第一种是使用泛型、策略或任何其他必要的技术来重用现有的解析逻辑，以最小化冗余行。

第二种方法是复制并粘贴整个`UserFileParser`类，将其重命名为`AdminFileParser,`，再做一些小的调整，就这样。

在这种情况下，复制和粘贴选项不仅比重用现有代码更好，而且是开发人员避免将来出现问题的唯一选择。

碰巧的是，在某些时候，代表不同事物的两个不同文件具有相同的模式。然而，文件的模式很可能会彼此独立地改变——明天一个文件中将有更多的列，或者它的列将被交换，一个新的分隔符将被引入一个文件，等等。

维护单个文件解析器的多功能性将花费开发人员太多的精力。文件解析器中可能会出现条件语句，这会降低代码的可维护性和可测试性。此外，由于其中一个文件中的模式更改而试图更改解析器可能会中断对两个文件的解析。

# 复制和粘贴以避免紧密耦合

当软件开发人员试图完全消除代码重复时，他们会使系统的组件耦合得更紧密。虽然紧密耦合通常不会在整体代码库中导致严重的问题，但是微服务架构或模块化整体应用程序可能会因为紧密耦合而受到严重影响。

让我们先看看下面的例子:

```
public class PdfReportGenerator
{
    public void Generate(string path)
    {
        var file = Parser.Parse(path);
        //Generate Pdf report from file content
    }
}public class HtmlReportGenerator
{
    public void Generate(string path)
    {
        var file = Parser.Parse(path);
        //Generate HTML report from file content
    }
}public class Parser
{
   public static File Parse(string path)
   {
       //TODO: parse the file
   }
}
```

`PdfReportGenerator`和`HtmlReportGenerator`类重用封装在`Parser`类中的相同解析逻辑。上述所有类都是单个解决方案的一部分，更重要的是，它们作为单个部署单元一起部署。

如果开发人员决定重命名`Parse`方法，但忘记在消费者中重命名方法调用，就会出现编译错误。开发人员将立即更新`Parse`方法的所有消费者，并部署工作代码。

然而，在微服务架构中，每个类可能属于每个自己的项目，并且每个项目可以单独部署。

![](img/53c5ed61f02283f136a7420c05f8db27.png)

在这种情况下，更改`Parse`方法的接口而不更新消费者不会导致任何构建错误，因此开发人员可以向存储库提交重大更改。

为了摆脱这种和其他常见依赖给系统带来的问题，开发人员可以将`Parser`实现复制到每个微服务中，并扔掉共享包。

![](img/4b9d085422a473582edaea62dc55d6b6.png)

然而，在微服务中完全摆脱共享包是不可能的。每个微服务不能独立实现所有需要的代码，因为实现几乎每一个新的需求或修复一些问题通常需要在许多微服务中进行相同的更改。

好的，那么如何决定什么时候复制粘贴以避免紧耦合，什么时候复制粘贴是合理的呢？一般建议是:

*   具有数千行代码或领域知识的复杂基础设施代码应该只存在于一个共享包或微服务中。
*   不太可能改变的代码(例如，解析静态文件的代码，这些静态文件的模式多年来都是相同的)或简单的基础设施代码可以在多个微服务中复制和粘贴。

# 因业务压力复制粘贴

也许，在一个由软件开发人员驱动的世界里，将有足够的时间来重构代码，编写所有类型的测试，并遵循所有可能的最佳实践。

然而，在现实中，在业务驱动的世界中，软件开发人员可能需要尽快实现一些功能，这反过来迫使开发人员复制和粘贴现有的逻辑。

由于业务压力，完全避免复制和粘贴代码是不可能的。当这种情况发生时，复制和粘贴代码的开发人员或评审人员至少应该做到以下几点:

*   在问题跟踪系统中登记技术债务票据。
*   描述和评估机票。
*   将科技股纳入最近的 sprint 的范围。

通过遵循这些步骤，不需要的重复代码的生命周期将会很短。

# 结论

*   在您的应用程序中有相似甚至相同的代码块是完全正常的，除非更改一个代码块会导致另一个相似或相同的代码块发生相同的更改。
*   在微服务或模块化单片应用中，开发人员经常需要在代码冗余和紧耦合之间进行平衡。
*   有时，开发人员可能会因为业务压力而复制和粘贴真正应该存在于一个地方的代码。在这种情况下，开发人员需要在问题跟踪系统中注册一个技术债务票据。

感谢阅读。如果你喜欢你所读到的，看看下面这个故事:

[](https://levelup.gitconnected.com/5-ways-to-improve-the-performance-of-c-code-for-free-c89188eba5da) [## 5 种免费提高 C#代码性能的方法

### 慢速代码是可选的。

levelup.gitconnected.com](https://levelup.gitconnected.com/5-ways-to-improve-the-performance-of-c-code-for-free-c89188eba5da)