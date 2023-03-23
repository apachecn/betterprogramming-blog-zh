# 如何像专业人士一样使用 React 挂钩

> 原文：<https://betterprogramming.pub/how-to-use-react-hooks-like-professional-573cc96fb79d>

## React 挂钩的最佳实践

![](img/42e4b2ddff7c4549d55e1ad9f3422860.png)

图德·巴休在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

React 挂钩的引入从根本上改变了我们构建 React 应用程序的方式，将状态和生命周期功能暴露在基于类的组件之外。功能组件现在是 React 的一等公民，构成了 React 应用程序的主体。

尽管 React 钩子非常强大，但也很容易被误用，在代码中引入意外的行为或错误。

让我们来看看使用 React 挂钩的一些最佳实践，以确保您正在编写干净、高效和可维护的代码。

# 使用钩子，因为它们应该被使用

虽然这看起来很明显，但是我已经看到了一些非常不恰当的使用钩子的方法。React 定义了下面的[钩子规则](https://reactjs.org/docs/hooks-rules.html)。

## 只调用顶层的钩子

不要在循环、条件和嵌套函数中调用钩子。让我们来看一个例子:

```
if (setStorage) {
  useEffect(() => {
    localStorage.setItem('item', 'example');
  })
}
```

注意`useEffect`是如何嵌套在`if`语句中的。

这应该被重构，以便我们在根处调用`useEffect`:

```
useEffect(() => {
  if (setStorage) {
    localStorage.setItem('item', 'example');
  }
})
```

这可以防止每次组件渲染时钩子没有以相同的顺序被调用，并让 React 在`useState`和`useEffect`调用中正确地保持状态。

## 仅从功能组件调用挂钩

虽然这似乎是显而易见的，但是不要在普通的 JS 代码中使用 React 钩子——只在组件中使用。这使得跟踪您在哪里修改和维护状态变得更加容易。

# React Hooks 有一个 ESLint 插件——使用它

React 发布了一个很棒的 ESLint 插件，名为[ESLint-plugin-react-Hooks](https://www.npmjs.com/package/eslint-plugin-react-hooks)，帮助开发者在他们的项目中以正确的方式编写钩子。甚至在运行应用程序之前，使用这个 eslint 插件来捕获和修复钩子错误。

定义了两个相对简单的规则:

*   `react-hooks/rules-of-hooks`
*   `react-hooks/exhaustive-deps`

第一条规则只是强制您的代码遵守钩子规则，正如上一节所解释的。第二条规则确保在效果函数中引用的每个值都在`dependencies`数组中。

例如，这段代码将触发`exhaustive-deps`警告，因为它正在访问一个不在`dependencies`数组中的变量`componentId`:

虽然这看起来很烦人，但它将帮助您避免一些与未列出的依赖项相关的奇怪错误。

# useState 也可以用于对象！

许多`useState`示例用多个变量声明多个状态:

```
const [userEmail, setUserEmail] = useState('')
const [userName, setUserName] = useState('')
const [isError, setIsError] = useState(true)
```

虽然这样做是可行的，但是当您开始在本地状态中维护更多的值时，可能会有点冗长。`useState`也可以很好的保存数组和对象，为什么不使用它呢？

```
const [state, setState] = ({
  email: 'test@test.com',
  userName: 'Name',
  isError: false,
})
```

这里唯一需要注意的是，当设置状态来更新对象的单个值时，必须确保将旧状态正确地合并到新状态中:

```
setState((oldState) => ({
  ...oldState,
  userName: 'New Name',
}))
```

这确保了您获得的是最新的状态，而不是某个旧版本。

请记住，建议将状态[分割成多个状态变量，React 基于这些变量一起改变值(主要是为了可读性)。但是对于像表单这样的状态，将状态分组在一起更简单。](https://reactjs.org/docs/hooks-faq.html#should-i-use-one-or-many-state-variables)

# 定制挂钩超赞！

当您构建 React 应用程序时，您会开始注意到应用程序逻辑是跨许多组件共享的。

您可以将组件的逻辑提取到许多可重用的函数中，如[自定义挂钩](https://reactjs.org/docs/hooks-custom.html)，允许您在整个应用程序中创建类似的本地状态，从而使项目更具可重构性和可维护性。

# 不要使用支柱钻孔

属性钻取是 React 应用程序中的一个常见问题，在 React 应用程序中，数据从一个父组件向下传递，直到到达正确的子组件，而一些嵌套组件实际上并不使用它们。

React Context 是一个通过组件树向下传递数据的特性，而不必通过不手动使用它们的子组件传递数据。React 上下文的值由父组件定义，可以在任何子组件中用`useContext`钩子访问。这是维护全局状态的一个非常简单的方法。看一下文档中的`useContext`挂钩[。](https://reactjs.org/docs/hooks-reference.html#usecontext)

# 结论

正确使用的话，React Hook API 会非常强大。如果您遵循最佳实践，您将确保您拥有无 bug 且可维护的代码，这是一种乐趣。

感谢阅读！