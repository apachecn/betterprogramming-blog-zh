# 为什么您应该在下一个项目中开始采用 JSON 模式表单

> 原文：<https://betterprogramming.pub/why-you-should-start-adopting-json-schema-forms-in-your-next-project-547dbcbc800a>

## 构建声明性 web 窗体

![](img/5f7ca6b29e95aedd3b59debe6a489a60.png)

[凯莉·西克玛](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

# **什么是 JSON 模式形式？**

[JSON 模式](http://json-schema.org/)帮助您以声明方式构建和定制 web 表单。
应用程序中的表单有时会很复杂，你最终不得不将业务逻辑与 UI 组件混合在一起——尤其是如果你是从零开始构建这些组件，并且维护它们会有巨大的开销。

有了[模型驱动的开发原则](https://en.wikipedia.org/wiki/Model-driven_engineering)，现在你可以用纯 JSON 格式抽象所有这些东西。甚至有一个定义了的[规范，所以你可以维护标准。](http://json-schema.org/specification.html)

让我们深入一些核心原则，并探索如何在现实生活中使用这些形式。

# ***JSON 模式***

这是您的模型，您在其中定义表单应该由什么组成，以及基于它们的验证规则定义它们的类型(例如，`username`和`password`被存储为具有长度的类型字符串)。

# ***UI 模式***

对于给定的模型定义，您可以操作该组件的 UI 应该是什么样子。(例如，对于`password`，您可能希望文本输入是一个密码小部件)。

# ***表格数据***

它保存了在`formData.json`中持久化的可用 `onChange`事件的值，这些值在您提交表单时可用。

# 设置您的第一个表单(非常基本的登录表单)

表单架构. json

用户界面架构. json

表单-数据. json

现在让我们使用 React 挂载这个组件。我将使用[材质 UI](https://material-ui.com/) 变体[库](https://react-json-schema.app/)来演示这一点:

这是一个非常基本的例子，可以帮助您开始使用基于 JSON 模式的表单。你可以在 GitHub 上找到一个[现场游乐场](https://react-jsonschema-form-material-ui.github56.now.sh/)来看看你能创建多少复杂的表单和一个详细的例子[。](https://github.com/vip-git/react-jsonschema-form-material-ui/blob/master/src/demo/body/Example.jsx)

大多数库都将您与特定的小部件联系在一起，但是这个库有可能用您现有的组件来扩展它:

[](https://react-json-schema.app/docs/custom-components) [## 定制组件

### 本页描述了如何向 jsonschema 添加定制组件

react-json-schema.app](https://react-json-schema.app/docs/custom-components) 

# 结论

在下一篇文章中，我将通过一个例子更深入地将现有表单转换成基于 JSON 模式的表单。

发挥一下您的想象力，如果您可以基于这些 JSON 表单生成所有代码，会怎么样？将事物抽象到 JSON 的简单性给你留下了无限的可能性。