# 使用 useContext 和 useReducer 管理表单的状态

> 原文：<https://betterprogramming.pub/react-hooks-and-forms-dedb8072763a>

## 在 React 中构建更好的表单

![](img/27b36b813d89c7699284c491a51e3ad5.png)

Efe Kurnaz 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

React 的`[useState](https://reactjs.org/docs/hooks-reference.html#usestate)`和`[useEffect](https://reactjs.org/docs/hooks-reference.html#useeffect)`可能是使用和谈论最多的钩子，涵盖了你 90%的用例，但是鲜为人知的`[useContext](https://reactjs.org/docs/hooks-reference.html#usecontext)`和`[useReducer](https://reactjs.org/docs/hooks-reference.html#usereducer)`在应用到正确的用例上时绝对令人惊叹。

React 根据组件构建 UI 的方法让您可以将 HTML 及其状态视为一个实体。然而，在大多数情况下，你的组件的状态可能是彼此不相关的单独的小片段(例如，显示一个加载器，打开一个模态，等等。)使用单独的状态改变处理程序(使用`useState`，有几种情况下你的状态可能彼此紧密相关(例如一个复杂的用户信息对象)。在这种情况下，管理和更新它会变得非常麻烦。`useReducer`挂钩非常适合这种情况。让我们构建一个简单的表单来更好地理解这一点。

下面的例子使用了 [Immer 的减速器](https://github.com/immerjs/use-immer#useimmerreducer)，它是 React 的`useReducer`顶部的一个小包装器。想了解更多可以看我之前的[文章](https://link.medium.com/XfctSuUuzbb)。此外，为了便于理解，这个例子过于简化，但是如果需要，这种方法可以用于扩展。已经有这么多复杂的解决方案，如 [**Formik**](https://formik.org/) 和[**React Hook Form**](https://react-hook-form.com/)**可供您考虑构建表单。**

# **构建表单**

**我们将构建一个简单的表单来收集用户信息。它被分解成几个组件，每个组件可以根据需要而变得复杂。但是现在，事情很简单:**

**表单的初始状态如下所示:**

## **使用上下文**

**您可能已经注意到，我们没有向不同的表单组件传递任何状态属性。我们将使用 React 的[上下文 API](https://reactjs.org/docs/context.html#reactcreatecontext) 传递我们的状态。在这个小例子中，我们没有道具钻取的问题，但是当表单增长时，当您需要通过道具将状态传递给几个表单组件的子组件时，这个问题很容易出现。**

**因此，让我们创建一个上下文来存储这个状态:**

**该文件导出三样东西:**

*   **`FormContext` —用`null` *初始化的上下文对象。*这将最终用于保持状态。**
*   **`FormProvider` —这是一个 React 组件，每个上下文对象都带有它。该组件接受一个名为`value`的属性，它存储任何给定值。此提供程序的任何子组件都可以访问存储值。**
*   **`useFormContext` —一个定制的挂钩，用于获取存储在我们的上下文中的值。**

## **使用减速器**

**接下来，我们将创建一个包含修改状态的逻辑的 Reducer:**

**`useImmerReducer`钩子接受两个参数:缩减器本身和初始状态值。它返回状态和一个更新该状态的函数(这里，我们称之为`dispatch`)。更简单地说，这个函数可以被认为是传统的`setState`方法。**

**一个缩减器(`FormReducer`)接受两个参数:**

*   **`state` —每次调用缩减器时，它都会收到最新版本的状态。**
*   **`action` —这可以是用于修改状态的任何信息。通常，它作为一个物体看起来是这样的:**

```
{
  **key**: 'aKeyFromStateObject',
  **payload**: {}, // a new value for state[key]
}
```

## **处理事件**

**回到表单，大多数表单都有一个`handleEvent`或`handleChange`函数来处理各种输入/选择/复选框等。事件。我们的函数看起来像这样:**

**这可以与如下输入集成:**

```
<**input**
   **value**={state.name.value}
   **onChange**={(e) => **handleChange**("name", e.target.value)}
/>
```

## **使用提供商**

**到目前为止，我们已经创建了初始状态，创建了缩减器，并将事件处理程序附加到我们的输入中。现在让我们使用`FormProvider`组件向表单组件提供状态值:**

**这里，我们将状态和`handleChange`函数传递给`FormProvider`的值属性。这些可以在任何子表单组件中访问。**

## **使用我们的自定义钩子获取存储在上下文中的值**

**还记得我们创建的用于检索上下文值的`useFormContext`定制钩子吗？让我们看看如何使用它:**

**这里，我们使用自定义钩子来获取`FormContext`值，即状态和`handleChange`方法。**

# **把这一切联系在一起**

**首先，所有这些可能感觉有点奇怪，但是使用这种上下文+ Reducer 模式来维护/修改您的相关状态肯定要干净得多。我鼓励你仔细阅读下面的工作示例并进行尝试。**

**useReducer 和 useContext 挂钩管理 React 中的表单状态**

**代码可以在这个 [CodeSandBox](https://codesandbox.io/s/react-hooks-and-forms-450uv) 和 [GitHub](https://github.com/rkrajat/react-hooks-and-form) 上找到。**

**感谢阅读。**