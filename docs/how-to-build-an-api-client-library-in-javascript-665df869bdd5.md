# 如何用 JavaScript 构建 API 客户端库

> 原文：<https://betterprogramming.pub/how-to-build-an-api-client-library-in-javascript-665df869bdd5>

## 为 API 创建自己的 JavaScript 包装器

![](img/d1f608515ea4a0f4208455a06e4c4f2b.png)

j 萨莫拉在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

每当我发现自己连接到一个没有客户端库的第三方 API 时，我都必须围绕它创建许多样板文件:

*   手动获取身份验证凭据并将其传递给数据提取层。如果您想要持久化 API 键或执行 OAuth 过程，该怎么办？
*   定义数据模型和规范化。您需要确保从 API 接收的数据以结构化的方式保存。
*   为 API 创建一个控制器，将所有方法保存在一个地方，这样您就可以轻松地修改和查找 API 在整个代码库中的所有用法。
*   保持更新。API 往往会有很大的变化。新方法来了，旧方法过时了。

就在这几天，我决定连接到 [DEV API](https://docs.dev.to/api/) 来自动收集我发表的文章的统计数据。没有任何可供使用的 API 客户端，所以我最终创建了所有的包装逻辑。如果它已经存在，那么为什么不与他人分享我的学习成果呢？我们将经历五个步骤，并就如何实现您自己的客户端提出一个清晰的计划。

# 1.语言和目标平台

在进入编码部分之前，让我们讨论一下我们将使用哪种语言。还记得我关于定义数据模型的观点吗？对于 API 客户端，向用户提供更多关于将要返回的数据的信息是非常重要的，这样我们就不必经常在文档和 IDE 之间切换上下文。这也有助于在编写代码时避免错误和打字错误。我们最后都有考试，不是吗？记住所有这些，目前最好的选择是使用 TypeScript。

现在让我们考虑一下我们的图书馆将在哪里运行。基本上，它可以是浏览器或 Node.js 服务器。为什么不开发一个两者都适用的库呢？它还将使它与服务器端呈现一起使用变得更容易，在服务器端呈现中，代码首先在服务器上执行，然后在客户端执行。

# 2.集束

任何库的两个主要区别在于它的规模和对新旧客户端用户的支持。首先，我们希望我们的规模尽可能小 API 客户端不应该给项目增加太多的重量。其次，该库应该通过为那些不支持现代版本的用户提供 CommonJS 包，同时为拥有较新版本的客户提供一个现代包，来提供像样的浏览器支持。

web 项目的默认选择是 [Webpack](https://webpack.js.org/) ，但是我们的库相当小而且简单，所以我选择了 [Rollup](https://rollupjs.org/) 和`[microbundle](https://github.com/developit/microbundle)`作为基础捆绑器，以便于设置。

```
npm i -D microbundle
```

现在用构建任务和入口点的路径更新`package.js`:

```
{
  "source": "src/foo.js",          // Your source file (same as 1st arg to microbundle)
  "main": "dist/foo.js",           // Output path for CommonJS/Node
  "module": "dist/foo.module.js",  // Output path for JS Modules
  "unpkg": "dist/foo.umd.js",      // Optional, for unpkg.com
  "scripts": {
    "build": "microbundle",        // Uses "source" and "main" as input and output paths by default
    "dev": "microbundle watch"
  }
}
```

# 3.图书馆的结构

因为我们打算支持许多 API 端点，所以如果 API 扩展，我们希望我们的代码具有良好的伸缩性。最好的方法之一是将文件夹与资源名称对齐。对于 API 的[开发，它看起来像这样:](https://docs.dev.to/api/)

```
/src
	/articles
		index.ts // Everything that's related to articles
		...
	/comments
		index.ts
		...
	/users
		...
	index.ts // Imports and joins all resources together
```

将资源保存为单独的类也很有用，这样你就不需要在每次添加新方法时改变根`index.ts`。然后，您需要使用 [TypeScript Mixins](https://www.typescriptlang.org/docs/handbook/mixins.html) 将它们合并在一起:

# 4.获取库

我们想做一个既能在浏览器中工作又能在 Node.js 中工作的库。我们如何让我们的请求也通用呢？`fetch`在浏览器中有，但在 Node.js 中没有，在那里你应该使用`http`模块。

[同构-解锁](https://github.com/developit/unfetch/tree/master/packages/isomorphic-unfetch)将帮助我们在任何地方使用`fetch`函数，并在浏览器和 Node.js 版本之间自动切换。

现在让我们来看看包装`fetch`并附加认证头的`request`函数:

我们总是返回`Promise`,这样客户端可以将请求链接在一起或者等待结果。

# 5.测试

API 客户端中的大部分代码都与第三方端点集成在一起。为了使测试更容易，我们可以利用 [Nock](https://github.com/nock/nock) 库，它可以方便地模拟一个 HTTP 服务器:

在上面的例子中，我们设置了模拟，然后发出请求，最后检查模拟是否已经执行。

# 结论

我们构建了一个 API 客户端，它很小，可伸缩，支持开箱即用的 TypeScript，可以在浏览器和 Node.js 中工作。

如果你想看最终的代码，请随意查看[库](https://github.com/ilyamkin/dev-to-js)。不要犹豫问我细节或建议改进。