# 使用@Input()测试角度组件

> 原文：<https://betterprogramming.pub/testing-angular-components-with-input-3bd6c07cfaf6>

## 出于免费的故事？在 https://aiko.dev/angular_test_input_decorator/[免费获得这篇文章](https://aiko.dev/angular_test_input_decorator/)

本文涵盖了测试通过@Input()装饰器绑定输入的组件的多种方法

![](img/6745f4a4308ef5907ce570bc0394d8e8.png)

(这篇文章的更新版本可以在[这里](https://aiko.dev/angular_test_input_decorator/)找到。)

当开发一个接受输入的 Angular(阅读 Angular 2 或 Angular 4，或者当您阅读本文时的任何当前版本)组件时，您可能会决定对整个组件进行单元测试。至少我希望你有！

例如，我们有一个组件`ComponentUnderTest`，在其中我们想要显示大写的输入…我知道这是一个多么好的用例，每个企业主都需要它！

所以，假设`ComponentUnderTest`绑定并显示输入。

为了验证`processInput()`正确地大写了我们的输入，我们可以简单地给输入变量分配一个测试值，并断言，在调用该方法之后，显示的输入全部是大写的。

很简单！对吗？

但是现在，我们需要手动调用`processInput()`，直到我们调用它，我们的输入仍然以糟糕的小写显示。

幸运的是，有了 Angular 的`OnInit`生命周期挂钩，我们甚至可以在显示任何东西之前触发我们的`processInput()`。所以我们来实现它，在相应的方法中调用`processInput()`。

让我们进行测试！

哦不！测试失败了！

```
Cannot read property ‘toUpperCase’ of undefined
```

啊，当然，当在我们的`ngOnInit()`中的输入上调用`toUpperCase()`时，我们甚至还没有给它赋值。

当我们实际将组件嵌入到产品代码中时，我们将通过绑定组件标签中的输入来设置`input`的值。

所以让我们将`ComponentUnderTest`嵌入到`TestHostComponent`中。通过实际拥有一个主机或父组件，我们能够准确地传递测试所需的输入。

在我们的测试中，我们可以简单地定义这个`TestHostComponent`，将它包含在测试模块配置中，并在我们的`beforeEach`方法中实例化它。

什么结果是绿色测试，输入在开始时是大写的。

然而，我们真的能确定输入是大写的，并且我们的组件不总是显示“输入文本”吗？让我们编写另一个测试，它支持“不同的测试输入”。

我们可以做的是定义另一个`TestHostComponent`来绑定另一个输入，但是我们可以做得更好！

`setInput()`在我们的主机组件中设置输入。在让 Angular 检测变化之前，在我们的测试中分配输入允许我们的两个测试都通过..

现在，假设我们必须将不止一个而是多个输入绑定到组件。我们现在需要为每个输入编写一个 setter 方法吗？

幸运的是，答案是否定的！

除了设置器，我们还可以从主机组件中获取对组件的引用，并将其传递到我们的测试中。在那里，我们可以完全控制我们的组件，并可以修改我们想要的任何字段。

# 摘要

*   为了测试通过`@Input()`装饰器绑定输入的组件，我们可以在测试中创建一个主机组件来包装我们的测试组件。
*   对于多个测试输入，我们可以向我们的主机组件添加一个 setter。
*   对于一个以上的输入绑定，甚至对于测试中的组件的更多控制，我们可以用`@ViewChild`从主机组件中获取它，并将其直接传递到我们的测试中。

完整的源代码可以在 [GitHub](https://github.com/AikoPath/ComponentInputTest) 上找到。

[![](img/f1553c88c61d1fa0f54648e89f8a9acf.png)](https://ko-fi.com/G2G5BKVAB)

在[https://ko-fi.com/G2G5BKVAB](https://ko-fi.com/G2G5BKVAB)支持我