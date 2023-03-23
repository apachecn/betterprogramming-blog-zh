# 如何有条件地呈现 React 组件

> 原文：<https://betterprogramming.pub/conditional-rendering-in-react-components-bea51e34f3a1>

## 仅在以下情况下显示此组件…

![](img/3327086724b77ecc0864c5ab0739276d.png)

弗朗切斯科·德·托马索在 [Unsplash](https://unsplash.com/s/photos/render?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

[React](https://reactjs.org/) 是一个用于创建前端视图的库。它有一个庞大的图书馆生态系统与之合作。此外，我们可以用它来增强现有的应用程序。

在本文中，我们将研究如何有条件地呈现项目。

# 条件渲染

我们可以通过使用常规的`if`或`switch`语句有条件地呈现 React 组件中的项目，或者我们可以对有两种情况的条件语句使用三元运算符。

例如，我们可以使用三元运算符，如下所示:

```
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = { on: false };
  } handleClick(e) {
    e.preventDefault();
    this.setState({
      on: !this.state.on
    });
  } render() {
    return (
      <div>
        <button onClick={this.handleClick.bind(this)}>Toggle</button>
        <p>{this.state.on ? "ON" : "OFF"}</p>
      </div>
    );
  }
}
```

在上面的代码中，我们有一个按钮元素，它附带了`handleClick` click-handler 方法。

然后，我们有了`p`元素，其中有三进制表达式来显示`ON`或`OFF`，这取决于`this.state.on`的值。如果是`true`，我们得到`ON`。否则，我们得到`OFF`。

我们可以将三元表达式重写为一个`if`语句，如下所示:

```
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = { on: false };
  } handleClick(e) {
    e.preventDefault();
    this.setState({
      on: !this.state.on
    });
  } render() {
    let status;
    if (this.state.on) {
      status = <p>ON</p>;
    } else {
      status = <p>OFF</p>;
    }
    return (
      <div>
        <button onClick=  {this.handleClick.bind(this)}>Toggle</button>
        {status}
      </div>
    );
  }
}
```

正如我们所见，JSX 和普通的 JavaScript 代码可以无缝地结合在一起。

为了使用一个`switch`语句，我们可以这样写:

```
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = { on: false };
  } handleClick(e) {
    e.preventDefault();
    this.setState({
      on: !this.state.on
    });
  } render() {
    let status;
    switch (this.state.on) {
      case true:
        status = <p>ON</p>;
        break;
      case false:
        status = <p>OFF</p>;
        break;
      default:
        status = null;
        break;
    }
    return (
      <div>
        <button onClick={this.handleClick.bind(this)}>Toggle</button>
        {status}
      </div>
    );
  }
}
```

这三个例子都做了同样的事情。当我们点击按钮时，`ON`和`OFF`文本将会切换，因为我们调用了`this.setState`并将`on`设置为`this.state.on`的相反值。

因此，我们得到了切换效应。

# 使用逻辑&运算符的内嵌 If

如果我们只有一个`if`块，我们可以将条件表达式与一个`&&`组合，然后我们要添加的表达式如下:

```
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = { val: 0 };
  } handleClick(e) {
    e.preventDefault();
    this.setState({
      val: Math.random()
    });
  } render() {
    return (
      <div>
        <button onClick={this.handleClick.bind(this)}>Click Me</button>
        <p>{this.state.val > 0.5 && "You win"}</p>
      </div>
    );
  }
}
```

在下面的`render`方法中，我们有以下元素:

```
<p>{this.state.val > 0.5 && "You win"}</p>
```

只有当`this.state.val`大于`0.5`时，我们才会显示`You win`。

它之所以有效，是因为如果条件`this.state.val > 0.5`为`true`，React 将继续运行`&&`之后的代码，即`You win`。

这与以下内容相同:

```
render() {
  let status;
  if (this.state.val > 0.5) {
    status = "You win";
  }
  return (
    <div>
      <button onClick={this.handleClick.bind(this)}>Click Me</button>
      <p>{status}</p>
    </div>
  );
}
```

正如我们所看到的，第一个例子要短得多。

# 阻止组件呈现

如果我们想阻止一个元素渲染，我们可以把它设置为`null`或者`undefined`。

例如，在上面的例子中，我们有:

```
let status;
if (this.state.val > 0.5) {
  status = "You win";
}
```

这意味着`status`最初被设置为`undefined`。

只有当`this.state.val`大于`0.5`时，我们才设置`status`到`You win`。

那么，这意味着`status`有时是`undefined`，它不会显示任何内容，或者是`You win`，它会显示`You win`。

# 结论

我们可以用多种方式有条件地显示项目。首先，我们可以使用`if`语句。

如果我们有很多案例要检查，我们也可以使用`switch`语句。

如果我们想要显示某个东西是否为 `true`而另一个东西不是，我们可以使用三元表达式。

如果我们只想显示的东西是`true`，我们可以加入条件表达式和代码来显示带有`&&`的东西。

为了防止某些东西出现，我们可以将它设置为`null`或`undefined`。