# React 中使用 Apollo 客户端获取 GraphQL 数据

> 原文：<https://betterprogramming.pub/fetching-data-from-a-graphql-api-in-react-ad003edc4e7b>

## 旅程

![](img/d273ce18d0be0b191b164235daca8a84.png)

卢克·切瑟在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我们将通过一些例子来说明如何使用 [Apollo 客户端](https://www.apollographql.com/docs/react/)状态管理库从 React 项目中的 [GraphQL API](https://graphql.org/) 获取和显示数据。

# 设置

在我们开始全面了解 Apollo 客户端之前，让我们先创建一个 React 项目，或者您可以使用下一个基础 React 项目，因为我们下面要探索的代码就是基于它的。

[](https://github.com/drac94/react-ts-sb-sc-boilerplate) [## drac 94/react-ts-s b-sc-样板文件

### 这个项目是用 Create React App 引导的。在项目目录中，您可以运行:在…中运行应用程序

github.com](https://github.com/drac94/react-ts-sb-sc-boilerplate) 

如果您想了解关于这个基础项目的更多信息，请不要忘记查看我解释了包括哪些内容以及它如何帮助您简化项目的文章。

[](https://medium.com/better-programming/7-things-every-react-project-should-start-with-21f7570a7a4b) [## 每个 React 项目应该从 7 件事开始

### 从一开始就让您的项目变得简单

medium.com](https://medium.com/better-programming/7-things-every-react-project-should-start-with-21f7570a7a4b) 

让我们用下面的命令克隆它:

```
git clone [https://github.com/drac94/react-ts-sb-sc-boilerplate.git](https://github.com/drac94/react-ts-sb-sc-boilerplate.git) react-graphql
```

正如开始时提到的，为了能够连接到 GraphQL API，我们将利用 Apollo 客户端库，它为我们提供了许多好处，如内存缓存、本地状态管理等。

要安装这个库，我们需要在终端中运行下一个命令:

```
cd react-graphql
yarn add [@apollo/client](http://twitter.com/apollo/client) graphql
```

安装完成后，我们就可以开始配置 React 项目了。

对于我们的例子，我们将使用 [Countries GraphQL API](https://github.com/trevorblades/countries) ，它为我们提供了关于国家、大洲和语言的信息

# 客户

我们的第一步是使用以下代码创建一个连接到 API 的客户端:

我们做了什么？

*   **第 1 行**:我们导入了设置应用程序所需的东西，包括客户端、缓存机制和提供者
*   **第 3-5 行**:我们创建了一个新的`ApolloClient`，将 API 的 URL 设置为`uri`参数和`cache`对象
*   **第 10 行**:我们用`ApolloProvider`包装了我们的应用程序，我们将刚刚创建的`client`设置为初始值

这样，我们的 React 项目就可以开始获取数据了。

# 这些疑问

下一步是定义我们将用来获取所需数据的查询。为此，让我们创建一个新文件，我们将在其中定义特定视图所需的所有查询。

这个查询将获取一个国家列表，列表中的每个国家对象都有一个`name`属性。

# 景色

最后，我们使用刚刚定义的查询获取视图中的数据。

这里发生了什么？

*   **线 3** :我们进口了`[useQuery](https://www.apollographql.com/docs/react/data/queries/#executing-a-query)`挂钩
*   **第 6 行**:我们导入了在`App.query.ts`中定义的查询
*   **第 13 行**:触发了`useQuery`钩子，查询通过

`useQuery`钩子返回一个包含三个主要属性的对象——`loading`、`error`和`data`——我们将根据查询的状态使用它们来呈现不同的 UI 元素

*   **第 15 行**:如果`loading`为`true`，我们渲染一条`loading…`消息
*   **第 16 行**:如果`loading`是`false`并且`error`不是未定义的，我们显示一条错误信息
*   **第 17-28 行**:最后，如果`loading`为假，`error`未定义，并且`data`不为空或未定义，我们循环显示结果——在本例中是国家的名称

我们现在应该在浏览器中看到一个国家列表。

# 变量

如果我们需要向查询传递变量呢？假设我们想要获取某个特定国家的信息。

我们从定义查询开始。

如果我们分析这个查询，我们可以看到现在我们正在传递一个名为`code`的变量。它的类型是`ID!`，这是一个强制`!`。

现在我们称之为在我们看来。

这里的关键部分在第 16 行，我们通过发送一个`variables`对象作为`useQuery`钩子的第二个参数来定义`code`变量的值。

# 懒惰

前面的两个查询都是在视图呈现时执行的，但是有时我们会希望在用户做出动作时调用我们的 API，比如单击按钮。这在 Apollo 客户端中也是可能的——我们只需要使用`[useLazyQuery](https://www.apollographql.com/docs/react/api/react/hooks/#uselazyquery)`钩子而不是`useQuery`。

让我们再看看第 16 行，这里我们使用了`useLazyQuery`钩子，它返回一个数组，数组的第一项是一个函数，我们可以在需要的时候调用它来获取数据。

这一次我们获取数据，直到用户点击按钮，而不是在最初的渲染。

# 结论

我们看到，借助 Apollo Client，只需很少的代码，我们就可以配置 React 项目，通过 GraphQL API 获取数据，但这只是冰山一角——我们还可以做更多的事情。我们还可以使用它进行突变，我们可以创建和使用本地查询和突变，我们可以跟踪网络状态或修改本地状态等。

最终，我们有了一个完整的状态管理库，允许我们使用 GraphQL，因此选项可能是无穷无尽的。感谢阅读。