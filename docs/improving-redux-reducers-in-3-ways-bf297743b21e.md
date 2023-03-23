# 从 3 个方面改进 Redux 减速器

> 原文：<https://betterprogramming.pub/improving-redux-reducers-in-3-ways-bf297743b21e>

## 对象、类和函数式编程

![](img/8d68ccd6a2eb7cf4366e5ac0f0e2d640.png)

照片由 [Isaac Smith](https://unsplash.com/@isaacmsmith?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在这篇文章中，我假设你知道什么是 Redux 以及 reducers 是做什么的。

我将讲述如何通过提高 Redux Redux reducer 的速度来改进它们，以及如何避免*圈复杂度*警告/错误，当动作数量增加时，您可能会遇到类似 [SonarQube](https://www.sonarqube.org/) 的情况。

下面是一个简单的`switch`语句，你可能在 99%的 Redux/reducers 例子中见过。

我们要改进的方法是使用一个`dictionary`。

# 字典(键值对)

一个`dictionary`只是一个简单的 JavaScript 对象，你可以在其中添加一个字符串值作为键，并给它赋值。

下面是我们将要创建的内容的简化版本。注意，关键是 action `type`名称，赋值是一个函数，它将当前状态和有效负载作为参数来创建一个新状态。

# **JavaScript 函数式方法**

让我们通过创建一个`baseReducer`函数来改进字典示例，该函数将`initialState`作为第一个参数，将字典作为第二个参数。

希望下面的代码易于阅读/理解，但是基本上，我们使用动作类型常量作为函数名。

在上面的代码中，`reducerDictionary`参数是传入的字典。注意这里如何使用`action.type`，`reducer[action.type]`，以访问正确的减速器功能。

# **JavaScript 类方法**

让我们通过为我们的类缩减器创建一个`BaseReducer`类来扩展字典示例。

下面，注意如何`ShowsReducer` `extends` `BaseReducer`。这是*继承*,它将一些逻辑抽象到另一个类中，因此 reducers 只拥有必要的东西。

如果你看上面的`BaseReducer`，你会看到:

*   `Line 2`:是当一个 reducer 类扩展这个`BaseReducer`时将被覆盖的`initialState`。
*   `Line 4`:Redux 将使用的 reducer 方法。
*   `Line 5`:获取与`action.type`匹配的类方法。
*   `Line 7`:如果没有找到方法(`!method`)或者动作是错误的(`action.error`)，则返回当前的`state`。
*   `Line 11`:用`state`和`action`参数调用找到的方法，该方法将返回 Redux 将使用的修改后的`state`。

**为什么更快并避免了圈复杂度？**

`switch`语句类似于`if`语句，其中代码检查多个语句以找到正确的一个。基本上它是检查每一个，看看是否有匹配。使用字典方法，代码可以立即获取正确的代码，因为它知道从哪个槽获取它。

[圈复杂度](https://docs.sonarqube.org/latest/user-guide/metric-definitions/)是基于通过代码的路径数计算的。某些关键字增加了复杂性，`case`就是其中之一。因此，当 reducer 中的动作数量增加时，复杂性也会增加。我正在做一个项目，其中的代码不能合并到`master`中，除非它通过了所有的代码需求，圈复杂度是其中之一。

# 代码示例

如果您想看看这些代码示例的运行情况，请查阅我的另一篇文章，获取示例应用程序以及函数式方法和基于类的方法的源代码。我也有打字稿版本！

[](https://medium.com/better-programming/my-awesome-react-redux-structure-6044e5007e22) [## 我的超赞反应/还原结构

### 了解如何以优雅的方式构建 React/Redux 应用程序

medium.com](https://medium.com/better-programming/my-awesome-react-redux-structure-6044e5007e22) 

如果你喜欢这篇文章，请分享它，关注我，阅读我的其他[文章](https://medium.com/@robertsavian)和/或用我下面的推荐链接注册 Medium。谢谢！

[](https://medium.com/@robertsavian/membership) [## 通过我的推荐链接加入 Medium—Robert S(代码带)

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

medium.com](https://medium.com/@robertsavian/membership)