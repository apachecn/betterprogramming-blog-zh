# 在 React 中处理多个表单输入

> 原文：<https://betterprogramming.pub/handling-multiple-form-inputs-in-react-c5eb83755d15>

## 快速简单的指南

当一个组件中有多个表单输入需要处理时，我经常看到学生们犯错误。例如，考虑一个基本的登录表单。很有可能会是这样:

自然，我们想要处理这两个输入字段的状态，所以我们可能会在这个组件的*状态*上留出两个字段。

当然，现在我们需要使用一个 *onChange* 监听器来收集这些输入字段中的值。

这就是麻烦开始的地方。我们看到两个输入字段，所以我们的下意识反应可能是创建**两个**更改处理程序。

这将工作，但…恶，对不对？如果我们有一大堆不同的领域呢？我们真的想为每一个写一个新的变更处理程序吗？

编写一个**通用**变更处理程序实际上非常容易——我们只需要两个要素:

1.表单元素的*名称*。
2。使用括号符号来定义我们新的*状态*对象的能力。

让我们关注第二点。特别是，ES6 的一个特性将有助于简化这一过程，所以让我们先了解一下这个特性。

从 ES6 开始，可以使用**括号符号**将键分配给**对象文字**中的对象。

现在，我们知道当对象存储在变量中时，使用括号符号来分配一个键是什么样子。

```
const obj = {};

obj['someKey'] = 1;

console.log(obj.someKey); // 1
```

括号符号的优点是我们可以在括号内计算 JavaScript 表达式。

```
const obj = {};

obj['keyNo' + '2'] = 2;

console.log(obj.keyNo2); // 2
```

然而，到目前为止，我们只能做到这一点，只要我们的对象在一个变量中。现在我们可以在定义对象文字时使用括号，就像这样:

```
const obj = {

  ['keyNo' + 3]: 3, // wowzers!

  regularKey: 4 // a regular key assignment, for comparison
};

console.log(obj.keyNo3); // 3
console.log(obj.regularKey); // 4
```

现在我们在使用括号语法方面有了更多的灵活性，让我们把注意力回到第一个要素:表单元素的名称。

您可能已经注意到，常规的 *<输入>* 元素通常有一个名为*的属性名称*:

```
<input type="text" name="email" />
```

这个 *name* 属性也是 HTML 的一部分——不需要特别反应。

我们可以从事件处理程序接收的*事件*对象中访问这个名称属性:

```
onChange (event) {
  console.log(event.target.name); // the name of the form element
  console.log(event.target.value); // the value of the form element
}
```

现在，让我们回到我们的例子。您可能会注意到一些方便的东西:我们的 *state* 对象上的键名…

```
this.state = { email: '', password: ''}
```

…匹配我们输入的名称:

```
<input type="text" name="email" />
<input type="password" name="password" />
```

这意味着我们可以利用上面介绍的两种成分，编写一个通用的**变更处理程序，如下所示:**

好多了！

如果您发现自己在一个表单中编写了多个变更处理程序，请停下来，考虑如何将其压缩成一个。