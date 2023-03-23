# React 中 onClick 事件中的呈现组件

> 原文：<https://betterprogramming.pub/rendering-components-in-onclick-events-in-react-bc0d7b54e1cd>

## 单击按钮即可呈现组件

通常，在 React 中，当单击按钮时，您希望呈现一系列组件。虽然拼凑一个解决方案并不难，但我最近发现了一个特别有效的方法。

我们将从 App.js 文件开始。文件的状态如下所示:

在这个实例中，问题是在一个数组中定义的，但是它们很容易来自 ComponentDidMount API 调用。

除了 App.js 文件之外，还有一个 Question.js 文件，这是一个功能组件，如下所示:

为了在单击时有条件地呈现问题，我们向 App.js 添加了一个 on click 方法，该方法为 displayQuestions 的 state 值在 true 和 false 之间切换:

在 App.js return 语句中，该函数被添加到按钮:

```
<button className=”btn” onClick={this.displayQuestion}>View Unanswered Questions</button>
```

接下来，在 App.js 呈现函数中，我们设置:让 questions = null，然后添加一个方法，如果 displayQuestions 为 true，该方法将有条件地设置 questions 变量等于问题组件

最后，questions 变量包含在 render 语句中，当单击按钮时，它将呈现 null 或问题: