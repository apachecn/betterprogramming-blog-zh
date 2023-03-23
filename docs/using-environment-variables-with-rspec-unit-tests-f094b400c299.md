# 如何在 RSpec 单元测试中使用环境变量

> 原文：<https://betterprogramming.pub/using-environment-variables-with-rspec-unit-tests-f094b400c299>

## 编写受环境变量影响的 Ruby 单元测试

![](img/88ebbf8615b2c35e8aef573b5ee26851.png)

由[科学高清版](https://unsplash.com/@scienceinhd?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

RSpec 是一个帮助我们使用特定领域语言为 Ruby 应用程序编写单元测试的工具。

软件开发的最佳实践之一——尤其是在开发软件产品的情况下——是使用环境变量来存储应用程序的配置项。使用这些配置有几个原因:

*   功能标志:这些是环境变量，指示应用程序支持的功能是否启用。
*   秘密:在源代码中存储生产秘密，比如数据库密码，是一个非常糟糕的主意(即使它是加密的)。更好的方法是将这些秘密作为环境变量只存储在运行应用程序的主机上。
*   特定于安装/客户/环境的配置项:这些配置项专门用于应用程序的安装或使用应用程序的客户，如用户帐户、定制配置和依赖关系配置。

由于使用环境变量来构建可配置软件，源代码的执行结果变得依赖于这些变量的值。软件应用程序的行为可能因不同的配置值而有所不同，尤其是在软件考虑这些变量的值来启用/禁用功能或选择处理策略时。例如，使用环境变量定义主题国家的税收计算器应用程序可能会根据国家变量为相同的数据计算不同的税额(因为不同的国家有不同的税收百分比)。

为这样的软件应用程序编写单元测试有时可能是一项复杂的任务(因为需要定义测试的用例),并且需要大量的注意力来确保所定义的测试用例是独立的并且不会相互影响。

在本文中，我将介绍一种为受环境变量影响的软件应用程序连接单元测试的方法。下面是我将为之编写测试的 Ruby 应用程序。这个应用程序是一个简单的 Ruby 类，它使用环境变量`MODE`来确定排列数组项目优先级的策略。

测试上面的类的一种方法或想法是在执行 RSpec 测试之前设置所需的环境变量，以便能够模拟使用不同的值运行应用程序。可以通过多种方式实现这一点，尽管有些方式比其他方式更安全。

下面是为上面的类编写 RSpec 单元测试的第一次尝试。下面的测试使用`before`块来设置各自的环境变量，并准备测试的上下文。

上述单元测试高度依赖于测试的执行顺序，因为单元测试正在改变运行流程的环境变量。`ENV`对象是所有测试用例之间的共享对象——即使测试用例在不同的名称空间或文件中定义。例如，在上面的例子中，`default mode`上下文中的环境变量`APPLICATION_MODE`的值将是*降序*，即使它没有在上下文中定义(我假设执行的顺序)。这将导致在运行单元测试时出现下面的错误消息。另一方面，将`default mode`上下文移到文件的顶部会解决这个问题。

```
1) PriorityList#priority_list default mode  sorts ascending
     Failure/Error: expect(subject.priority_list).to eq list.sortexpected: [0, 1, 3, 3, 3, 4, 6, 7, 20]
            got: [20, 7, 6, 4, 3, 3, 3, 1, 0](compared using ==)
```

这意味着我们的测试不是相互隔离的，并且存在一些测试用例配置影响其他测试用例的风险。这个问题可以通过为每个改变环境变量的上下文添加一个`after`块来隔离测试用例的上下文来解决，如下所示:

```
after do
  ENV['APPLICATION_MODE'] = nil
end
```

然而，我并不喜欢这种想法，因为它向测试中引入了更多的代码，而且`after`块可能会变成一个很长的块，这会降低测试的可读性。

相反，我更喜欢使用 RSpec 支持的`[stubs](https://relishapp.com/rspec/rspec-mocks/docs)`和`[allow](https://relishapp.com/rspec/rspec-mocks/docs)`方法来实现相同的单元测试。唯一需要实现的更改是对`before`块的更改。我们需要用下面几行代码替换直接的环境变量赋值来模拟`ENV`对象的行为:

```
allow(ENV).to receive(:fetch).with('APPLICATION_MODE', 'ascending')
                                     .and_return('ascending')
# Or 
allow(ENV).to receive(:fetch).with('APPLICATION_MODE', 'ascending')
                                     .and_return('descending')
```

上面的代码行正在存根化`ENV`对象，以模拟对象在用`APPLICATION_MODE`键接收到一个`fetch`调用的情况下的行为，并设置该调用的预期结果。这种方法的最大优点是，我们不需要在测试执行后重置`after`块中单元用例的上下文。

在本例中，我们在应用程序类中使用了没有方法`fetch`的对象`ENV`，比如`@env = ENV['APPPLICATION_MODE']`。我们可以重写这些台词:

```
allow(ENV).to receive(:[]).with('APPLICATION_MODE')
                                     .and_return('ascending')
# Or
allow(ENV).to receive(:[]).with('APPLICATION_MODE')
                                     .and_return('descending')
```

如上所述，下面是使用`[stubs](https://relishapp.com/rspec/rspec-mocks/docs)`和`[allow](https://relishapp.com/rspec/rspec-mocks/docs)`方法的所展示的应用程序的完整 RSpec 单元测试实现:

# 结论

环境变量在软件开发中非常有用，有助于提高代码质量，降低在版本控制系统(如 GitHub)中暴露安全配置的风险。

测试这样的软件可以通过直接在测试用例中修改环境变量或者使用`stubs`来完成。后一种方法的优点是，它有助于减少编写测试用例所需的代码行数。最重要的是，它有助于保持单独的测试用例相互隔离。