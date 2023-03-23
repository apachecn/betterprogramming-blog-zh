# 为什么我从 Next.js 迁移到 React App

> 原文：<https://betterprogramming.pub/why-i-migrated-from-next-js-to-create-react-app-7e74834e8431>

## 我没有从 CRA 迁移到 Next——原因如下

![](img/215f89736ce714b935a430e53be3259b.png)

照片由[克莱门特·H](https://unsplash.com/@clemhlrdt?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/javascript?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

让我先说我是 [Next.js](https://nextjs.org/) 的超级粉丝。对于构建 React 应用程序来说，这是一个令人惊叹的框架，尤其是当您需要服务器端呈现(SSR)时。总有一天，我会写一篇文章介绍这个框架的所有优点，但幸运的是，很多人已经在这么做了。

相反，我现在将解释为什么我从 Next.js 迁移了我的许多辅助项目之一来创建 React App。

注意:如果你不关心背景故事，向下滚动，直接进入结论。

# 免费开始

当你有很多副业项目(这些项目不会让你赚到钱)时，如果你能免费主持这些项目就好了。我有一个托管在 Heroku 的自定义域上的 Ruby on Rails 项目，这个项目已经花费了我每月 30 多美元，这很好——如果你只有一个项目或者这个项目让你赚了一些钱…

使用 Firebase 和 Firestore 构建 React 应用程序的一个很酷的地方是，您可以构建一个全栈应用程序，并且不需要任何成本(除了自定义域名)。我用类似于 [FlowReads](https://www.flowreads.com/) 和 [MakerMove](https://www.makermove.com/) 这样的附带项目来做。我还制作了 [React Milkshake](https://www.reactmilkshake.com/) ，这是用这个堆栈创建应用程序的样板文件。对于一个新的副业项目，我打算用同样的方法，但是用 Nextjs，虽然它工作得很好，但是我遇到了一个问题。

# 在 Vercel 上部署

部署 Nextjs 应用程序的最佳地方是 Vercel(以前的 [Zeit](https://zeit.co/) )。虽然你可以免费开始，但如果我想保持我目前的项目，我必须升级到团队计划(每月 20 美元起)。

使用 Next.js，您可以选择是静态生成页面还是服务器呈现页面。如果您的页面是服务器呈现的，这意味着在构建过程中会为您的页面创建一个无服务器函数。Zeit 的团队刚刚决定将无服务器功能的数量限制在 12 个——而我的应用程序有 14 页。这意味着我不能再部署这个应用程序——由于这个限制，它会在构建过程中失败。我可以理解限制免费层上无服务器功能数量的决定，但最令人沮丧的是我的站点已经部署了。在 Zeit 增加了这个限制后，如果不升级，我就无法部署任何更改！

你可能会想“但是你为什么要服务器渲染每一个页面呢？你就不能至少静态地提供几页吗？”这也是 Zeit 推荐的，也正是我想要的。唯一的问题是，以我目前的实现(使用 Redux)，我不能。让我解释一下。

# SSR vs SSG

使用 Next.js，你可以在你的页面组件中使用`getIntialProps`(或者从 9.3 版本开始使用`getServerSideProps`和`getStaticProps`)，它决定一个页面是应该由服务器渲染还是静态生成(在这里阅读更多关于[的内容](https://nextjs.org/docs/basic-features/data-fetching))。如果你的页面中不需要任何数据，它会被静态生成，但是当你在`_app.js` *中使用`getInitialProps`时，你所有的页面都会被服务器渲染。*在构建您的应用程序时，您会收到这条警告消息:“由于 pages/_app 中的 getInitialProps，您已经退出了自动静态优化。”

现在，当您使用 Redux 作为您的状态管理库时，您可能会使用`next-redux-wrapper`，因此您不必实现任何复杂的东西来使您的商店客户端状态与服务器同步等。至少，我是这么做的，问题来了:这个库在你的`_app.js`文件上调用`getInitialProps`，所以你所有的页面都会实现服务器端渲染。

老实说，我的应用程序不需要服务器渲染，因为一切都在登录屏幕后面。我基本上只用了 Next.js，因为我想用它做实验。我可能有更多的选择，而不仅仅是将我的项目重构到 CRA，但我认为服务器端渲染(即使是 Next.js)可能带来的麻烦不值得为一个不会从中受益太多的项目花费时间。我也喜欢免费托管东西，只要我还在开发阶段，而对于 Next.js，我不确定我能不能做到。

# **结论**

如果您在 Nextjs 应用程序中使用 redux(使用 [next-redux-wrapper](https://github.com/kirill-konshin/next-redux-wrapper) )并且有超过 12 条路线，如果您在 Zeit 上托管项目，您将需要升级到团队计划。

如果你不需要 SSR，正在用 redux，还想免费托管，那你大概就不要用 Next.js 了，还有，如果你想用 Next.js 的 SSG 选项，就不要用 [Redux](https://redux.js.org/) 了。

否则，我可以推荐你尝试一下 [Next.js](https://nextjs.org/) ，因为这是一个非常好的框架！