# C#开发人员扩展方法的力量

> 原文：<https://betterprogramming.pub/the-power-of-extension-methods-b3b1962475c4>

## 以及为什么你不应该害怕使用它

![](img/9cbbc604fb31f1312a75a46775821a90.png)

由 [Austin Distel](https://unsplash.com/@austindistel?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/software?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

你有没有感觉到你的软件开发伙伴害怕使用任何静态的东西？据说，抽象层的缺失会降低代码库的可测试性。虽然在某种程度上这是真的，但是当正确实现时，扩展方法(或者任何静态方法)可以极大地改进您的代码库！

为了解释扩展方法的好处或能力是什么，让我们首先深入到扩展方法本身；什么是扩展方法，如何使用它们，有哪些用例。

# 解释扩展方法

顾名思义，扩展方法是扩展特定类型的方法。通常，扩展方法用于向类型“添加”一个方法或一部分功能，而无需对类型本身进行任何修改或创建新的派生类型。扩展方法总是静态方法，但是调用它们时就好像它们是扩展类型的实例方法一样。

扩展方法可以通过两种方式识别:

*   方法是`static`
*   该方法的第一个参数前面是关键字`this`

# 基本扩展方法

假设我们有一个简单的存储库接口，它定义了您可以对一个实体进行的所有最基本的修改的契约；CRUD 操作:

*   *创建—* 在底层数据层创建一个实体
*   *Read* —查找与指定模式匹配的所有实体
*   *更新* —根据指定实体的标识符更新单个实体
*   *删除* —删除所有匹配指定模式的实体

这个接口可能看起来像这样——其中`IEntity`只定义了一个名为`Identifier`的属性(它有一个`long`数据类型):

```
public interface IRepository<TEntity>
    where TEntity : class, IEntity
{
    void Create(TEntity entity);

    IEnumerable<TEntity> Read(Func<TEntity, bool> match);

    void Update(TEntity entity);

    void Delete(Func<TEntity, bool> match);
}
```

当然，在许多情况下，这些简单的 CRUD 操作是不够的。

当您只期望一个结果而不是多个实体时，能够找到单个实体是非常有用的。

例如，当基于唯一的属性值(如标识符)查找实体时。

这就是扩展方法派上用场的地方:

```
public static TEntity GetSingle<TEntity>(
        this IRepository<TEntity> repository,
        Func<TEntity, bool> match)
        where TEntity : class, IEntity
{
    return repository
        .Read(match)
        .Single();
}
```

这样，您可以在您的存储库上调用`GetSingle`扩展方法，而不会污染您的存储库本身:

```
repository.GetSingle(e => e.Identifier == entityIdentifier);
```

或者，更进一步，您可以定义一个扩展方法，专门过滤特定的属性，比如标识符:

```
public static TEntity GetSingle<TEntity>(
    this IRepository<TEntity> repository,
    long identifier)
    where TEntity : class, IEntity
{
    return repository
        .Read(e => e.Identifier == identifier)
        .Single();
}
```

您可以使用相同的原则来定义一个`TryGetSingle`扩展方法，当没有找到实体时，它不会抛出异常。

相反，它返回一个默认值:

```
public static TEntity TryGetSingle<TEntity>(
    this IRepository<TEntity> repository,
    long identifier)
    where TEntity : class, IEntity
{
    return repository
        .Read(e => e.Identifier == identifier)
        .SingleOrDefault();
}
```

我们对这些扩展方法所做的就是利用我们简单的`IRepository`接口，并扩展它，这样所有实现该接口的类都不需要单独定义它们自己的`GetSingle`和`TryGetSingle`方法。

这使得我们的接口及其实现类更加干净。我们还为扩展方法定义了一个单点定义:它们都在一个类中定义:`RepositoryExtensions`。

您可以只在一种特定类型上定义无限数量的扩展方法(当然，不建议这样做)。

# 链接方法

扩展系列的下一站是方法链接。当涉及到链接方法(例如大多数 LINQ 方法)时，扩展方法可能是一个非常强大的工具。

链接方法通常在扩展时返回相同的类型，这使得它可以链接多个扩展方法——这就是术语“链接方法”的由来。

下面是一个可能的例子。我们可以定义一个`Employee`类型，带有几个属性(名、姓、雇员号和出生日期)。

我们还在类型上定义了几个简单的扩展方法——用于设置`Employee`类型的特定属性:

```
public static Employee WithFirstName(
    this Employee employee,
    string firstName)
{
    employee.FirstName = firstName;

    return employee;
}
```

想象一下该类型的每个属性都有一个扩展方法。我们现在可以这样实例化一个新雇员:

```
var employee = new Employee();

employee
    .WithFirstName("John")
    .WithLastName("Doe")
    .WithEmployeeNumber("MLG420")
    .WithBirthdate(birthdate);
```

在这种情况下，不仅扩展方法非常有用，您的`Employee`类中的静态`Create`方法可以使它更具可读性:

```
public static Employee Create()
{
    return new Employee();
}
```

`Employee`实例化现在看起来像这样:

```
var employee = Employee.Create()
    .WithFirstName("John")
    .WithLastName("Doe")
    .WithEmployeeNumber("MLG420")
    .WithBirthdate(birthdate);
```

虽然扩展链在这样简单的情况下可能不会增加额外的价值，但在更复杂的情况下，它肯定会增加价值，在这种情况下，您不仅需要在类型上设置简单的属性值。想象一下可能性！

# 高级链接方法

我曾经为运行集成测试创建了自己的测试环境构建平台，只是为了好玩。

通常，集成测试需要某种上下文，但是您仍然希望它们是幂等的——这意味着，无论您运行测试多少次，测试都不应该依赖于现有的上下文，也不应该依赖于任何现有的数据。

这意味着应用链接方法来构建特定的测试环境是非常有用的。为了演示这一点，我创建了一个`TestContext`类，它包含一个需要出现在我们的测试用例中的`Employees`列表。

```
public static (TestContext, Employee) AddEmployee(
    this TestContext context)
{
    var employee = Employee.Create();

    return (context, employee);
}

public static (TestContext, Employee) WithName(
    this (TestContext, Employee) input,
    string firstName,
    string lastName)
{
    var (context, employee) = input;

    employee.FirstName = firstName;
    employee.LastName = lastName;

    return (context, employee);
}public static TestContext Save(
    this (TestContext, Employee) input)
{
    var (context, employee) = input;

    context.Employees.Add(employee);

    return context;
}
```

注意，我们没有返回扩展类型的实例，而是返回一个值元组，其中包含我们的`TestContext`实例以及我们将要添加的*雇员*。

`Save`方法最后将实例化和修改后的`Employee`添加到实际的测试上下文中，返回`TestContext`本身以使其可用于进一步的链接。

这种用法可能如下所示:

```
TestContext.Create()
    .AddEmployee()
    .WithName("John", "Doe")
    .WithBirthdate(birthdate)
    .WithEmployeeNumber("MLG420")
    .Save();
```

或者更进一步说:

```
TestContext.Create()
    .AddEmployee()
    .WithName("John", "Doe")
    .WithBirthdate(birthdate)
    .WithEmployeeNumber("MLG420")
    .Save()
    .AddEmployee()
    .WithName("Jane", "Doe")
    .WithEmployeeNumber("MLG024")
    .Save();
```

像这样使用链接方法的一个好处是，特别是当涉及到测试用例时，您不需要总是设置 employee 类型的每个属性。

例如，当您的测试用例中不需要出生日期时，就不要调用`WithBirthdate`扩展方法。您只需显式地设置测试用例所需的所有属性。不管你想在你的类型上设置什么属性，也不管你想实例化多少类型，你都可以在一行可读的代码中完成！

完成`TestContext`的设置后，您可以轻松地访问`Employees`的列表——以及您在上下文中定义的任何其他列表——这些列表是您在设置中添加的，例如使用它们来模仿`EmployeeRepository`。

# 用扩展方法测试

如前所述，扩展方法可能非常强大，但是大多数开发人员都避免使用它们，因为扩展方法没有提高代码库的可测试性——因为缺少抽象层。

尽管如此——抽象层不存在——扩展方法本身非常容易测试。这是因为它们通常只变异一个或两个指定类型的实例。

可以定义一个简单的输入输出单元测试来测试一个扩展方法做什么！这使得扩展方法更加可靠！这也意味着，因为它们是如此的不可思议的可靠，假设扩展方法做了它们应该做的事情，你可以测试你的代码库的其余部分。你甚至不再需要*来‘抽象’它们了！*

# 扩展方法的好处

尽管本文中的例子在现实生活中可能不那么有用，但是使用扩展方法的原则是相同的。如果实施得当，它们可以:

*   保持你的类型定义非常干净；一个存储库类只需要包含最基本的修改操作。
*   提高代码库的可读性；单独设置许多属性比一系列命名良好的扩展方法可读性差；
*   **有单一的责任；**像编辑指定类型的属性值；
*   **是幂等的；**因为它们是静态的，有一个责任，所以对于指定的输入，输出总是相同的；
*   是小的、非常好测试的代码片段；这使得他们非常可靠；
*   **帮助编写符合开放/封闭原则的代码；扩展类型防止它们在不应该被修改的时候被修改，从而防止类变得臃肿；**

由于它们的单一责任、小尺寸、可靠性和等幂性，当测试你的代码库的其余部分时，扩展方法不需要被抽象。你可以依靠他们(和他们的单元测试)在相同的环境下总是做完全相同的事情。

# 权力越大，责任越大

像这样使用扩展方法伴随着巨大的责任，并不一定是所有开发人员的首选。不言而喻，扩展方法也不总是可行的。

然而，*如果你使用它们，你需要保持你扩展的类型尽可能的干净，并且遵守单一责任模式——对于你的类型定义和扩展方法本身。*

使用扩展方法不仅*需要*这样做，而且*也刺激*这样做，因为扩展方法遵循开放/封闭原则(对扩展开放，对修改关闭)。

# 结论

所以，现在你知道了——如果你还不知道的话——扩展方法的威力是什么。

如前所述，它们并不适合每个人、你正在做的每个项目或你所处的每种情况。

它们不是你必须使用的圣杯，但是请问问你自己它们是否以及如何能帮助你或者你正在做的项目。

感谢阅读。