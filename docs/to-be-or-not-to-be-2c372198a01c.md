# 生存还是毁灭

> 原文：<https://betterprogramming.pub/to-be-or-not-to-be-2c372198a01c>

## 在 React 中选择受控和非受控组件

![](img/70960cd3f47a2c0aa3397259ccb8fe2d.png)

奥利弗·施文德纳在 [Unsplash](https://unsplash.com/s/photos/control?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

[受控](https://reactjs.org/docs/forms.html#controlled-components)和[非受控](https://reactjs.org/docs/uncontrolled-components.html)组件是 React 中的两个重要概念。了解它们之间的区别以及哪种情况最适合它们是至关重要的。

React 中的受控和非受控组件是什么？

*   受控组件管理其子组件的数据。
*   不受控制的组件让其子组件管理自己的数据。

上面定义中提到的孩子是典型的 HTML 表单元素，比如`<input>`、`<textarea>`、`<select>`等。在本文中，我们将以`<input type=”text”>`为例。

子也可以是反应组分。

不受控制的组件使用自然的 HTML 方式，让 DOM 保存和更新它们的值。下面的`App`是一个不受控制的组件，其中的输入值由 HTML 元素`<input>`维护。

受控组件被称为 React 方式，由 React 团队推荐。

受控输入通过一个属性接受它的值，并提供一个回调来更新这个值。该值通常由组件的状态保存。用法很简单。

让我们用一些例子来看看受控组件和非受控组件的区别。

# 示例 1:将每个输入按键转换为大写

我们如何管理不受控制的组件？React 提供事件处理程序来检测更改，然后使用回调来响应更改。

在下面的例子中，`onChange`处理器将不受控制的输入值转换成大写。这发生在每次击键时。

这可以通过使用如下受控部件来实现:

# 示例 2:用初始值将每个输入键转换为大写

我们稍微修改一下例子一，让它有一个初始值。下面是不受控制的组件如何处理初始值。

下面是受控组件处理初始值的方式:

# 示例 3:单击按钮后将输入转换为大写

在这个例子中，我们想要演示外部事件是如何管理 input 元素的。大写转换被延迟，直到点击一个按钮。

我们面临一个问题。从按钮的事件处理程序中，我们无法访问这个不受控制的输入字段。

React 提供了解决这类问题的参考。ref 是访问 DOM 元素或 React 元素的一种方式。

在下面的代码中，`React.useRef()`生成一个 ref，`textInput`。这个引用被传入输入元素，并在安装时使`textInput.current`指向这个输入元素。

然后，我们能够使用`textInput.current`来管理这个不受控制的输入值。

以下是匹配的受控组件示例:

# **示例 4:将有效输入转换为大写**

我们在这个例子中添加了验证。在这里，只有字母是有效的输入。当键入无效的键时，它会被忽略，并显示一条红色错误消息。

对于不受控制的组件，通用的`onChange`事件处理程序被替换为`onKeyPress`，因为我们可能需要在`onChange`发生之前停止默认行为。

对于受控组件，它不需要更改事件处理程序，也不需要阻止输入元素的默认行为。

# 示例 5:表单提交用例

下面是一个表单提交用例:

*   有两个输入字段:姓名和电子邮件。姓名是必填字段，电子邮件需要通过正则表达式测试。
*   如果存在验证错误，第一个错误将以红色显示。
*   只有当所有输入值都有效时，提交按钮才会启用。
*   提交表单后，会立即显示发送的信息。该消息会一直显示，直到用户开始输入新值。

下面的代码使用了一个不受控制的组件。未受控制的特定行被突出显示，其中`handleChange`执行验证并启用/禁用按钮。

```
const App = () => {
  const [error, setError] = React.useState('');
  const [submission, setSubmission] = React.useState('');
  **const form = React.useRef();**
  const handleChange = () => {
    **const { name, email, submit } = form.current;**
    **if (!name.value) {
      setError('Name is required');
      submit.disabled = true;
    } else if (!/^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/.test(email.value)) {
      setError('Invalid email');
      submit.disabled = true;
    } else {
      setError('');
      submit.disabled = false;
    }** setSubmission(''); **};**
  const handleSubmit = e => {
    e.preventDefault();
 **const { name, email, submit } = form.current;
    if (!error) {
      setSubmission(`Sent info - name: ${name.value}, email: ${email.value}`)
      submit.disabled = true;
      e.target.reset();
    }**
  };
  return (
    <form **ref={form}** onSubmit={handleSubmit}>
      <input name="name" onChange={handleChange}/>
      <input name="email" onChange={handleChange}/>
      <div>{submission}</div>
      <button name="submit" **disabled** type="submit">Submit</button>
    </form>
  );
}
```

下面是等效的受控组件代码。受控制的特定行被突出显示，其中`handleChange`更新子数据，而`React.useEffect`执行验证并启用/禁用按钮。

```
const App = () => {
  **const [name, setName] = React.useState('');
  const [email, setEmail] = React.useState('');**
  **const [isDisabled, setIsDisabled] = React.useState(true);**
  const [error, setError] = React.useState('');
  const [submission, setSubmission] = React.useState('');
  **const handleChange = e => {
    if (e.target.name === 'name') {
      setName(e.target.value);
    } else if (e.target.name === 'email') {
      setEmail(e.target.value);
    }** setSubmission(''); **};**
  const handleSubmit = e => {
    e.preventDefault();
 **if (!error) {
      setName('');
      setEmail('');
      setSubmission(`Sent info - name: ${name.value}, email: ${email.value}`)
      setIsDisabled(true);
    }**
  };
 **React.useEffect(() => {
    if (!submission) {
      if (!name) {
        setError('Name is required');
        setIsDisabled(true);
      } else if (!/^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/.test(email)) {
        setError('Invalid email');
        setIsDisabled(true);
      } else {
        setError('');
        setIsDisabled(false);
      }
    }
  }, [submission, name, email]);** 
return (
    <form onSubmit={handleSubmit}>
      <input name="name" **value={name}** onChange={handleChange}/>
      <input name="email" **value={email}** onChange={handleChange}/>
      <div style={{color: "red"}}>{error}</div>
      <div>{submission}</div>
      <button name="submit" **disabled={isDisabled}** type="submit">Submit</button>
    </form>
  )
}
```

# 示例 6:具有反应子组件

我们在开始时已经提到，识别受控和非受控组件的方法可以应用于 React 组件的子组件。

在下面的示例中，`App`是一个非受控组件，尽管它的子组件`ShowUpperCase`是一个受控组件。

同样的行为可以用受控的方式编码。接下来的`App`是一个受控组件，管理`ShowUpperCase`的数据。`ShowUpperCase` 也是受控成分。

# 结论

您已经看到了一些受控和非受控组件的例子。两种方式都可行。你可以选择一种方式来编码你的用例。

然而，对于特定的组件，坚持一种方式。不要将同一组件的受控和非受控方式混合使用。此外，不要在组件的生存期内切换类型。

有时，您可能会在调试器控制台中遇到此警告:

```
A component is changing an uncontrolled input of type checkbox to be controlled. Input elements should not switch from uncontrolled to controlled (or vice versa).
```

这可能是由于您意外地将状态设置为`null`或`undefined`，这在 React 中被视为不受控制。当您取消设置一个值时，记得将其设置为`‘’`或`false`。

现在是时候考验你的知识了！

在下面的代码中，`App`是受控组件还是非受控组件？

答案是不受控制。

`App`写为受控元件，但`undefined`值将输入元件切换为不受控！

感谢阅读。我希望这有所帮助。如果你有任何问题，请随时回复。