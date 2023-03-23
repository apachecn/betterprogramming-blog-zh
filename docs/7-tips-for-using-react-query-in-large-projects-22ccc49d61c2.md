# 在大型项目中使用 React Query 的 7 个技巧

> 原文：<https://betterprogramming.pub/7-tips-for-using-react-query-in-large-projects-22ccc49d61c2>

## 副作用回调、手动使缓存数据无效等等

![](img/170c317f316dc9c6567c42e479ef7fbc.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上，由[阿姆莉·莫里雄](https://unsplash.com/@amayli?utm_source=medium&utm_medium=referral)拍摄的照片。

在本文中，我们将探索一些在现实世界的代码库中使用 [React Query](https://react-query.tanstack.com/) 的技巧和诀窍，这将帮助您保持项目的可维护性。

# 动机

我的目标是以要点的形式提供一些技巧，这些技巧是我在一年多的时间里经常在企业应用程序上使用 react-query 时学到的。

当开始使用一个新工具时，不缺少内容，和 React Query 也不例外，通常使用一个小应用程序作为参考(例如待办事项列表或计数器)。这很好，不会因为精心设计的领域逻辑而分散观众的注意力。然而，当考虑像企业应用程序或大型代码库这样的真实世界的例子时，需要更多的指导。

# 初步资源

本文假设对 React Query 的工作原理有一些基本的了解。要开始并更熟悉它，请随意查看我以前的文章。另外，看看他们精彩的[文档](https://react-query.tanstack.com/overview)。

如果你对`[useQuery](https://react-query.tanstack.com/reference/useQuery)`和`[useMutation](https://react-query.tanstack.com/reference/useMutation)`钩子的工作原理有一个非常基本的了解，你就可以开始了。

说完了，我们开始吧。

# 1.将 React 查询挂钩包装到您自己的定制挂钩中

不要直接从 React 组件中调用`[useQuery](https://react-query.tanstack.com/reference/useQuery)`和`[useMutation](https://react-query.tanstack.com/reference/useMutation)`，而是将它们包装在您自己定制的钩子中。这将提供一个清晰的关注点分离，拥有一个分离的数据层和表示层。它还将消除将 API 获取函数导入 React 组件的需要。

此外，它将允许您重用包含更多复杂性的钩子，我们将在本文后面讨论。

让我们看一个例子:

我喜欢在一个文件中有几个这样的定制钩子，按域分组。在前面的例子中，我们可以让多个钩子与同一个文件中的`'items'`域交互。

# 2.为了避免正确钻孔，将状态集中在需要的位置附近

[状态托管](https://kentcdodds.com/blog/state-colocation-will-make-your-react-app-faster)是保持应用程序可维护性的最佳策略之一。

我在早期使用 React Query 时犯的一个错误(盲目地遵循[智能和非智能组件模式](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0))是在一个高级组件中导入 React 查询挂钩，并通过 props 将数据传递给低级组件。这在几个级别上运行良好，但是随着我的应用程序的增长，众所周知的[支柱钻孔](https://kentcdodds.com/blog/prop-drilling)成为了一个问题。

事实上，从多个组件调用 React 查询钩子来访问同一块缓存数据几乎没有任何障碍，因此，您可以在那些确实需要数据的组件中调用钩子，而不是通过 props 传递一些数据。在本文的后面，我们将看到如何最小化执行的 API 请求的数量。

# 3.通过设置过时配置属性来防止不必要的 API 请求

React Query 带有一些默认值，可能会让您措手不及。根据他们的[文件](https://react-query.tanstack.com/guides/important-defaults):

> 默认情况下，通过 useQuery 或 useInfiniteQuery 的查询实例将缓存的数据视为陈旧数据。
> 
> 要改变这种行为，您可以使用 staleTime 选项对查询进行全局和逐个配置。指定较长的失效时间意味着查询将不会频繁地重新获取数据
> 
> 在以下情况下，将在后台自动重新获取过时查询:
> 
> 查询装载的新实例
> 
> 窗口被重新聚焦
> 
> 网络重新连接。
> 
> 该查询可选地配置有重新获取间隔。"

使用默认配置，如果您不止一次调用查询函数，那么您将获得与您调用它的次数一样多的 API 请求。

根据您的需要，您可以使用`staleTime` config prop 来节省一些请求。我在当前项目中放置了 30 秒的过时时间，这意味着缓存数据将在 30 秒内有效，因此只要我不显式地使缓存无效，在这个时间范围内就不会执行新的请求。

为了防止请求超过您需要的数量，在初始化 React 查询时，将`staleTime`配置属性设置到`QueryClient`对象中。然后，只要缓存仍然新鲜，您就可以多次调用同一个挂钩，而不会触发多个 API 请求:

*注意:这里提出的建议是基于我当前项目的需求，可能与你的一致，也可能不一致。根据数据的特殊性，您可以给自己或多或少的陈旧时间。*

# 4.对副作用使用 onSuccess 和 onError 回调

利用提供的回调来执行副作用——特别是但不限于`onSuccess`和`onError`。让我们考虑一个例子，每次`'items'`变异时都需要呈现一条 toast 消息，但是您可以在变异或查询之前或之后放置任何您想要的代码片段作为副作用:

请注意我是如何将包含`onSuccess`和`onError`回调的对象作为第二个参数传递给`useMutation`钩子的。

这种方法通过利用钩子来做重复的或特定于域逻辑的副作用，从组件中去除了大量的复杂性。

除了`onSuccess`和`onError`还有其他有用的回调，虽然那些是我用的最多的。在[正式文件](https://react-query.tanstack.com/reference/useMutation#_top)中查看。

# 5.手动使过期数据的缓存失效

当您的缓存过期时，使用`invalidateQueries`函数使其无效并自动重新获取。

我们再想一个例子。您已经从服务器获取了一个项目列表，并且已经使用 React Query 对它们进行了缓存。然后你添加一个新的项目。这使得最初从服务器获取的数据无效，因为项目列表已经更改。

与上一篇技巧文章中介绍的场景类似，让我们利用 React Query 的回调来使过时的缓存失效，这是变异的副作用:

请注意，我们正在调用`useQueryClient`钩子来访问缓存，并调用`invalidateQueries`函数来使与`ServerStateKeysEnum.Items`查询关键字对应的缓存无效。这将导致 API 在使用`useGetItems`钩子需要对应于那个键的缓存时重新获取。

这节省了大量的工作，因为您不再担心每次发生变化时手动重新获取 API 数据。相反，您可以让 React Query 来处理它

# 6.通过启用/禁用查询控制对数据的访问

有时候你想呈现一个包含一个`useQuery`钩子的组件，但是你不想让它获取数据。

让我们想一个例子。我们希望用户只有在对系统拥有有效订阅的情况下才能获取*项*。当然，这也需要在服务器端进行验证，但是在客户端进行快速检查可以防止不必要的请求。

React Query 提供了一个名为`[enabled](https://react-query.tanstack.com/guides/disabling-queries)`的选项，它接收一个布尔值。如果布尔值为`false`，则不会执行查询功能:

请注意我是如何修改`useGetItems`来接收一个`options`对象作为参数的。通过这样做，您可以直接从组件中提供选项，例如，启用或禁用查询。您也可以提供自己的`onSuccess`或`onError`回调。

# 7.避免混合用户界面和服务器缓存

Kent C. Dodds 对 UI 状态和服务器状态有一个很好的定义:

> “服务器缓存—实际存储在服务器上的状态，我们存储在客户端以供快速访问(如用户数据)。
> 
> UI 状态——仅在 UI 中用于控制应用程序交互部分的状态(如模态 isOpen 状态)。"

不要将服务器缓存与 UI 状态混淆。随着应用程序的增长，UI 状态也会增长。在适当的情况下，您可以依靠 React 上下文或第三方库来实现自己的状态管理解决方案。无论您采用哪种方法，都不在本文的讨论范围之内。然而，一个重要的建议是尽量避免将 React Query 管理的服务器缓存与您选择的武器处理的 UI 状态混淆。

服务器缓存本质上具有不同的行为，并带来不同的挑战。虽然 UI 状态通常具有同步行为，但是状态缓存是异步的。

混合服务器缓存和 UI 状态会产生可维护性问题，随着应用程序的增长，这个问题会变得更加明显。我的建议是选择适合您处理 UI 状态需求的任何方法，但是让 React Query 自己为来自服务器的数据施展魔法。

# 结论

React Query 仍然是一个频繁更新的新工具，因此人们不断地创建模式并发现使用该库的有效方法。

我希望这篇文章对你有用。敬请关注未来更多内容！