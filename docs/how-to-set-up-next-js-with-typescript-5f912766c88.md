# 如何用 TypeScript 设置 Next.js

> 原文：<https://betterprogramming.pub/how-to-set-up-next-js-with-typescript-5f912766c88>

## 使用 Next.js 和 TypeScript 启动新的 React 项目

![](img/23f8853bc0635d5bec4a14545fca66b2.png)

在 [Unsplash](/collections/385548/work-and-collaboration?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由 [Tran Mau Tri Tam](https://unsplash.com/@tranmautritam?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍照

如果你开始一个新的 React 项目，你可能要考虑 [Next.js](https://nextjs.org/) 和 [TypeScript](https://www.typescriptlang.org/) 。在本文中，我们将解释为什么这是一个很好的选择，并带您完成使用这些技术建立一个新项目的过程。

# 为什么是 Next.js

当您使用 React 启动一个新项目时，有许多重要的细节需要考虑。你的代码必须用捆绑器捆绑，比如 webpack，然后用编译器转换，比如 Babel。

Create React App 是一个很好的工具，可以帮助您处理这个问题，并为您提供一个良好的开端，但是代码分割、性能预渲染以及 SEO 或服务器端渲染又如何呢？

要构建一个完整的 React 应用程序，你需要的不仅仅是 CRA 提供给你的。通过使用 Next.js，您可以节省一些时间，这是一个 React 框架，为所有这些问题提供了一个解决方案。

# 为什么打字稿？

[TypeScript](https://www.typescriptlang.org/) 是一种开源语言，通过添加静态类型定义构建在 JavaScript 之上。类型提供了一种描述对象形状的方式，提供了更好的文档，并允许 TypeScript 验证您的代码是否正常工作。

TypeScript 似乎正在接管前端开发。几乎 90%的开发人员(他们填写了[JS 状态调查](https://2019.stateofjs.com/javascript-flavors/typescript/))承认他们希望在他们的下一个项目中使用或者学习使用 TypeScript。除此之外，根据 Stack Overflow 的 2019 [开发者调查](https://insights.stackoverflow.com/survey/2019#most-loved-dreaded-and-wanted)，它是最受欢迎的语言之一。

它非常受欢迎，在开发过程中有很多好处。我在“[Stack Choices:JavaScript vs TypeScript](https://codeburst.io/stack-choices-javascript-vs-typescript-c9db39b67bf6)”中详细说明了这些好处

# 我们开始吧

如果您从头开始，打开您的终端，在您的项目文件夹中，运行`yarn create next-app`(在这个例子中使用 Yarn，但是您当然也可以使用 npm)。

您将收到以下消息提示:

```
What is your project named?
```

填写一个名字，然后按回车键。您将看到的下一条消息是:

```
Pick a template
```

选择`Default starter app`，等待你的项目准备好。然后，在您的终端中键入`cd <your-project-name>`,确保您位于可以运行`yarn dev`来启动开发服务器的目录中。

如果一切正常，我们可以设置 TypeScript。首先在项目的根目录下创建一个空的`tsconfig.json`文件:

```
touch tsconfig.json
```

尝试重启开发服务器(`yarn dev`)。您将收到一条警告消息，如下所示:

```
It looks like you're trying to use TypeScript but do not have the required package(s) installed.
```

按照说明安装 TypeScript:

```
yarn add --dev typescript @types/react @types/node
```

现在，尝试再次启动开发服务器。启动服务器后，Next.js 将:

*   为您填充`tsconfig.json`文件。您可以自定义该文件。
*   创建`next-env.d.ts`文件，该文件确保 TypeScript 编译器选择 Next.js 类型。你应该*不要*碰这个文件。

现在让我们通过将`index.js`改为`index.tsx`来尝试一下。如果没有任何问题，这意味着您现在可以将 TypeScript 用于您的 Next.js 应用程序。

就是这样！祝你的项目好运，如果有任何问题，请随时提问。

感谢阅读。