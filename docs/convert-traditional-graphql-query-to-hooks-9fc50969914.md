# 如何将传统的 GraphQL 查询转换成 React 钩子

> 原文：<https://betterprogramming.pub/convert-traditional-graphql-query-to-hooks-9fc50969914>

## 轻松转换

![](img/5b3478f91518db011b8ee8d964dd4d30.png)

Justin Luebke 在 [Unsplash](https://unsplash.com/s/photos/change?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

如果你不知道 React 钩子或者如何在 GraphQL API 中使用它们，先看看这篇文章。

在本文中，我将告诉您如何轻松地将 GraphQL 查询转换为 React Hooks。

# GraphQL 查询

最简单地说，GraphQL 就是询问对象上的特定字段。让我们从一个非常简单的查询开始。

```
{ 
 hero {
   name
 }
}
```

我们运行时得到的结果。

您可以看到该查询与结果具有完全相同的形状。这对 GraphQL 来说是必不可少的，因为您总能得到您所期望的，并且服务器确切地知道客户端需要哪些字段。

让我们看一个在 React 类组件中进行 GraphQL 查询的实时示例。

```
npm install react-apollo
```

这个包为我们导出了`Query`。

```
npm install graphql-tag
```

将 GraphQL 查询字符串解析成标准 GraphQL AST 的 JavaScript 模板文字标签。

然后，从已安装的包中导入`Query`并使用它，如下例所示。

如果你注意到，除了实际数据之外，我们还有两个状态，`error`和`loading`。

## 装载状态

当这个组件挂载时，在后台发送的 GraphQL 查询可能还没有完成。但是我们需要处理没有数据的临时状态，因此，我们在`loading`状态期间返回一些有用的文本。

在这种加载状态下，通常可以做一些有趣的事情，比如显示加载微调器。

## 错误状态

现在，由于各种原因，查询也可能以`error`状态结束。有时 GraphQL 查询可能是错误的，或者服务器没有响应。

不管是什么原因，面向用户的 UI 应该显示一些信息来传达发生了错误。在这种错误状态下，通常您可以将这些错误消息发送给第三方服务，以跟踪哪里出错了。

现在，如果您要将传统查询更改为 Hooks，您必须执行以下操作:

```
npm install @apollo/react-hooks
```

它将为您提供一个`useQuery`钩子，导入这个钩子，并如下例所示为您的函数式 React 组件使用它。

因此，在我们将 ID 作为变量传递之后，上面的两个例子都对`Project`模型进行 GraphQL 查询，以获取特定项目的`name`。

第一个示例演示了 React 类组件的传统查询，而第二个示例演示了使用钩子的 React 功能组件。

# 结论

钩子是反应的一个强大的附加物。此外，React Hooks 是一个强大的库，可以在 React 应用程序中使用带有 GraphQL 的钩子。我建议现在就使用钩子来接触 GraphQL 查询，因为您已经有了基础。

你今天学到新东西了吗？评论和反馈总能让作者开心。