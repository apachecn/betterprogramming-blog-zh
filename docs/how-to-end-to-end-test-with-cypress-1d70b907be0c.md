# 如何用 Cypress 进行端到端测试

> 原文：<https://betterprogramming.pub/how-to-end-to-end-test-with-cypress-1d70b907be0c>

## 端到端测试入门

![](img/d1204247a21fe538e690676407e5576d.png)

阿尔瓦罗·雷耶斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

什么是端到端测试？我们在一个 Web 应用程序中有两个终端:前端和后端。使用单元测试，我们测试前端或后端的代码。我们不测试应用程序在浏览器中的行为，也不测试两者如何协同工作。

在我看来，编写单元测试是好的！

但是测试前端和后端如何协同工作也同样重要。

我们通过端到端测试来测试前端和后端是否配合良好。因此，自动填写表单、点击按钮、浏览页面应该通过端到端测试来测试。

运行端到端测试将验证您的前端是否正确处理从后端接收的数据。

# 柏树是什么？

有很多端到端测试工具包，但是最流行和最快的工具包之一是 [Cypress](https://www.cypress.io/) 。

Cypress 提供了一种用 JavaScript 和测试运行器编写端到端测试的方法。除此之外，他们还提供了一种在测试失败时保存截图和视频的方法。大多数组织喜欢开源，这很好。

# 我们如何使用 Cypress 进行端到端测试？

Cypress 文档中有一个很棒的教程[可以帮助你开始编写端到端的测试。](https://docs.cypress.io/guides/getting-started/writing-your-first-test#Write-your-first-test)

## 配置

在项目的根目录下，有一个`cypress.json`，您可以在这里更改一些默认配置。在我们的项目中，它看起来像这样:

```
{
    "testFiles": "**/*.e2e.test.js",
    "chromeWebSecurity": false
}
```

在`testFiles`属性中，我们告诉 Cypress 查找名称中包含`e2e.test.js`的文件。你可以用 TypeScript 配置 Cypress，但是在这种情况下，我认为使用它没有任何附加值。它需要花费更长时间的额外的蒸发步骤。

## 测试文件存储在哪里？

对于单元测试，将测试存储在组件文件夹中是非常常见的。在这种情况下，Cypress 以`cypress/integration`文件夹为根来查找文件。所以我们有一个基于应用程序本身的结构，在其中我们存储端到端的测试文件。

# 端到端测试的最低要求

每个测试在至少一个`it()`函数中至少有一个`describe()`函数。这类似于编写单元测试。

这个例子看起来像一个单元测试。所以我们需要利用柏树图书馆。你可以通过使用`cy`来使用它，就像下面我们访问`[https://example.cypress.io](https://example.cypress.io.)` [的例子。](https://example.cypress.io.)

你可以通过运行`npm run e2e:open`看到真正的浏览器执行这个动作。

我们在`package.json`中的脚本看起来像这样。

Cypress 将启动它的应用程序，在那里您可以选择想要运行的端到端测试。默认情况下，它会打开 Chrome 浏览器，但也有一个在 Firefox 和 Edge 中运行它的选项。

Cypress 提供了一个完整的库，你可以查看文档[了解它们都包含了什么。](https://docs.cypress.io/api/table-of-contents)

# 在端到端测试中应该测试什么？

我们将主要用端到端的测试来测试浏览器中出现或变化的东西。比如一个简单的场景:我们访问页面 x，检查`h1`是否包含文本“这是一个很棒的标题”。

在这个例子中，你从`cy`库中调用的每一个方法都是测试的一部分。Like `cy.visit()`将执行动作，但是如果在那个 URL 上没有有效的页面，它将使您的测试失败。所以你做的每一个动作都是在断言。

> 在 Cypress 中，有两种不同类型的断言。首先是“隐性主语”，在这种情况下你用`should()`或`and()`。二、“显性主语”，这种情况下，你用`expect`。在赛普拉斯文档中可以读到更多关于这个的内容。

当调用`cy.get('h1')`时，它将搜索`<h1>`标签。当它找到该元素时，您的测试将继续。如果在几毫秒内找不到元素，测试就会失败。在`get`方法之后，你可以链接很多其他的方法，比如:

*   `.contains()`期望包含内容的元素最终存在于 DOM 中。
*   `.find()`也期望元素最终存在于 DOM 中。
*   `.type()`期望元素最终处于可类型化状态。
*   `.click()`期望元素最终处于可操作状态。`.its()`期望最终找到当前主题的属性。

那么我们在端到端测试中应该测试什么呢？

1.  如果一个元素有某个类`cy.get('element.selector').should('have.class', 'ng-valid');`或者没有某个类`cy.get('element.selector').should('not.have.class', 'ng-valid');`。
2.  如果列表有 3 个子元素`cy.get('ul > li').should('have.length', 3);`。
3.  检查输入字段或文本区是否有特定值`cy.get('input[name="firstName"]').type('Santa Claus').should('have.value', 'Santa Claus');`。

> 检查[这个例子列表](https://docs.cypress.io/guides/references/assertions#Adding-New-Assertions)你能在`should`方法中使用什么。

**额外示例:**

添加父元素的上下文来执行断言，这对表单来说很方便。

> 在 [Cypress 文档](https://docs.cypress.io/api/commands/within#Examples)中查看更多`*within()*`方法的例子。

# 可重用功能

在你反复执行可重用操作的情况下，你可以写一些简单的函数放在一个 lib 文件夹中。但是 Cypress 提供了一种更友好的方式。这些被称为“自定义命令”；你可以在文档中找到很多例子[。](https://docs.cypress.io/api/cypress-api/custom-commands#Log-in-command-using-UI)

但是最好的例子之一是编写登录命令。您需要将代码添加到`cypress/support/commands.js`文件中。

[**通过电子邮件获取我的文章点击这里**](https://blog.byrayray.dev/subscribe) **|** [**购买 5 美元中等会员**](https://blog.byrayray.dev/membership)

# 结论

现在由您来用 Cypress 编写伟大的端到端测试！完美的测试是不存在的，但是如果你每次都对它们进行一点优化，它们就会成为你的真理之源。对于开发人员来说，在构建新功能或更改现有功能后运行端到端测试非常好，因为不可能将所有的测试场景都记在脑子里。

祝你好运，并享受编写有价值的端到端测试的乐趣。

# 谢谢！

![](img/2bd571bb1ce9f466ae279fa98111b086.png)

读完这个故事后，我希望你学到了一些新的东西，或者受到启发去创造一些新的东西！🤗

如果我给你留下了问题或一些要说的话作为回应，向下滚动并给我键入一条消息。如果你想保密，请在 Twitter @DevByRayRay 上给我发一条 [DM。我的 DM 永远是开放的😁](https://twitter.com/@devbyrayray)

[**通过电子邮件获取我的文章点击这里**](https://byrayray.medium.com/subscribe) **|** [**购买 5 美元中等会员资格**](https://byrayray.medium.com/membership)

# 阅读更多

![RayRay](img/992af170033696163d6cc0269218aedd.png)

[雷雷](https://byrayray.medium.com/?source=post_page-----1d70b907be0c--------------------------------)

## 最新的 JavaScript 和 TypeScript 故事

[View list](https://byrayray.medium.com/list/latest-javascript-typescript-stories-0358ad941491?source=post_page-----1d70b907be0c--------------------------------)14 stories![](img/c93ca03b33796c40dcc47873de2697c2.png)![](img/86f37efa11855f6f0f0f62984c37f696.png)![](img/ddbaa6d0bea676316247e82043d60b63.png)