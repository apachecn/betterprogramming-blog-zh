# 如何用 React 处理表单

> 原文：<https://betterprogramming.pub/handling-forms-with-react-26cae6c21427>

## 通过编写受控组件来处理输入更改

![](img/63d946c4f2ba3879c6dc53b62704ac02.png)

朱利安·莫罗在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我们将看看如何用 React 处理表单。

# 表单处理

处理表单输入值更改的标准方法是用 React 处理它们。这是一种叫做*受控组件*的技术。

我们可以用`input`、`textarea`和`select`元素创建受控组件。它们维护自己的状态，并根据用户输入进行更新。

和其他事情一样，组件状态用`setState()`更新。这意味着我们必须用输入元素的值来调用它，以便用输入的值来更新状态。

要用输入的值更新状态，我们可以编写以下代码:

在上面的代码中，我们有一个`input`元素，它有一个设置为`this.handleChange`的`onChange`处理程序。`handleChange`方法具有用最新输入值更新`this.state.name`的代码。`event.target.value`有最新值。

然后，我们在表单上附加了一个`onSubmit`处理程序，它被设置为`this.handleSubmit`。

我们调用`event.preventDefault`来阻止默认的提交动作，因此我们可以在处理程序中运行 JavaScript 代码来显示带有最新输入值的警告框。

处理程序方法使得处理输入验证变得容易。我们也可以在那里操纵它。例如，我们可以在更新状态之前更改输入值，如下所示:

```
handleChange(event) {
  this.setState({name: event.target.value.toLowerCase()});
}
```

在将输入设置为`this.state.name`之前，我们不必创建额外的函数来将输入转换为小写。

我们使用`value`属性将值设置为最新状态，这样我们就可以看到我们输入的值。

# textarea 标签

像处理`input`元素一样，我们可以用同样的方式处理`textarea`元素的值变化。

我们可以编写以下代码，将状态设置为最新输入的值:

这与处理来自`input`元素的输入值的代码非常相似，但是我们用一个`textarea`元素来代替。

# 选择标签

元素创建了一个下拉菜单，我们可以从中选择更多的值。

我们可以编写以下代码从下拉列表中获取选择，并将其设置为状态:

正如我们所看到的，它几乎和其他例子一样，除了我们有一个包含`option`元素的`select`元素。

`select`元素也有助于获得多重选择。我们可以通过获取可用的选项，获取选中的选项，并将它们放入一个数组来更新所有选择的状态，如下所示:

在代码中，我们有:

在函数的第一行，我们有:

```
const options = event.target.options;
```

它拥有来自`select`元素的所有选项。

然后，我们可以循环遍历`options`并检查每个的`selected`属性，如果`selected`为`true`，则将它们推送到数组中。然后我们用`fruits`调用`setState`，用最新选择的值更新状态。

在`handleSubmit`中，我们可以用逗号对`this.state.fruits`和`join`进行调用，将其转换成逗号分隔的字符串。

另外，`select`元素的`value`属性采用数组而不是字符串。React 足够智能，可以解析数组并呈现所选的值。

![](img/34e8badef9aef252a49cca9208ce6405.png)

照片由[布鲁克·拉克](https://unsplash.com/@brookelark?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 处理多个输入

当我们需要处理多个输入时，我们不想为每个输入创建一个新的`onChange`处理函数。所以，我们想做一个可以设置所有值的函数。

我们可以这样做:

在上面的代码中，我们有下面的`handleChange`方法:

我们获取`event.target`对象，并将其设置为`target`。`target`具有`name`和`value`属性，它们分别具有输入的 name 和 value 属性值。

因此，我们可以利用 ES6 的动态对象属性特性，调用`setState`，用`name`作为属性名，用`value`作为值来更新状态。

# 结论

我们应该通过编写受控组件来处理输入变化。为此，我们为`onChange`事件附加了一个事件处理函数。

为了处理表单提交，我们将一个`onSubmit`事件处理程序附加到表单上，然后从参数中获取`event`对象并在其中调用`event.preventDefault`，这样我们就可以在处理程序中运行 JavaScript 代码。

为了用一个`onChange`处理程序处理多个输入变化，我们从处理程序的参数中从`event.target`获取`name`和`value`属性，并用从 ES6 开始可用的动态属性名特性更新它。