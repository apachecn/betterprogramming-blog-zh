# JavaScript 中可理解的依赖注入

> 原文：<https://betterprogramming.pub/understandable-dependency-injection-in-javascript-fab97062c34c>

## 深入了解依赖注入容器，并知道如何移除它

![](img/62f3ccddaf622defec81268667649439.png)

布莱克·康纳利在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我演示了一个易于理解和使用的依赖注入设置，它在执行浏览器或服务器应用程序以及运行测试时都有效。

您可以在 [GitHub](https://github.com/tmj-code/understandable-dependency-injection-in-javascript) 上找到一个非常简单的例子(基本上是本文中的代码)以及一个扩展的例子。

# 简介:什么是依赖注入

依赖注入(或简称为 DI)是一种构建应用程序的方法，这样您就不会在本地创建依赖项，而是从应用程序的其他地方获得这些依赖项。

在下面的代码中,`PostController`依赖于`PostService`从一些 API 获取帖子:

```
/* --- ./post-controller.js --- */
export default class PostController {
    _postService;
    _posts = [];

    constructor(postService) {
      this._postService = postService;
    }

    get posts() { return [..._posts]; } loadPost() {
      this._postService.getList()
          .then(posts => {
              this._posts.length = 0;
              this._posts.push(...posts);
          });
    }
}
```

重要的是`PostController`并没有创造出一个`PostService`；相反，它是通过构造函数提供的。这意味着`PostController`唯一需要知道的就是如何使用`PostService`。

DI 的一个结果是测试变得容易多了，因为我们可以在测试期间简单地在构造函数中注入一个模拟的`PostService`。

软件不容易。一个开发人员同时要记在脑子里的东西越多，留给他做出好的解决方案的脑力就越少。DI 通过以集中和可预测的方式提供服务，消除了一些心理压力。

# 依赖注入容器

想象一下，在一个 web 应用程序中，我们有一个显示帖子列表的视图。它使用后置控制器来提供逻辑，本身只处理显示。我们不想担心如何创建控制器，所以我们要求有人提供给我们。

我们称之为依赖注入容器(以下简称:容器),因为它包含了提供服务实例的逻辑。

在不对您可能使用的任何框架进行假设的情况下，显示帖子的视图可能如下所示:

```
/* --- ./post-view.js --- */
import container from './di-container';export default function PostView(rootElement) {
    const controller = container.postController(); const list = document.createElement('ul');
    rootElement.appendChild(list); controller.loadPosts()
        .then(() => {
            while (list.lastChild) {
                list.removeChild(list.lastChild);
            }

            controller.posts.forEach(post => {
                const item = document.createElement('li');
                item.innerText = post.title;
                list.appendChild(item);
            }); rootElement.appendChild(list);
        });
}
```

本例中的视图只是呈现了一个带有文章标题的项目符号列表。

在这个简短的例子中，我们看到导入的容器如何拥有一个返回`PostController` *:* `const controller = container.postController();`实例的方法

现在让我们看看导入的文件`di-container.js`中发生了什么:

```
/* --- ./di-container.js --- */
const postServiceFactory = () => 
  new PostService(/* possibly some arguments */);
const postControllerFactory = () => 
  new PostController(postServiceFactory());const container = {
  postController: postControllerFactory
};Object.freeze(container);export default container;
```

我们定义`twofunctions`每一个都交付一个依赖项。然后我们将它们公开为一个简单的对象(`const container = {...};`)，只包含视图需要的服务:`PostController`工厂函数。

我们也可以公开这个函数来创建一个`PostService`，但是我们不要这样做，除非我们需要在某个视图中显式地使用它。

你可能注意到了上面的`Object.freeze(container);`线。这是一个内置的 JavaScript 特性:被冻结的对象不能再被修改；您不能更改其属性，也不能添加新的属性。

虽然 DI 并不要求这样，但这是好的，因为我们可能会在许多不同的地方导入容器，我们不希望允许这些地方中的任何一个地方改变容器，从而破坏所有的地方。

上面代码的唯一问题是，任何导入容器的人都会得到相同的依赖设置，无论是运行应用程序还是进行测试。如果我们要测试`PostView`，我们将得到真正的`PostController`，里面有一个真正的`PostService`，从而在测试中调用真正的 API。

我们不希望这样，所以让我们看看如何解决这个问题。

# 从容器中移除依赖项创建

我们将从容器中移除创建代码，并将其放在其他地方。这使得容器非常简单:

```
/* --- ./di-container.js --- */
export default { };
```

只是一个物体，仅此而已。如果您使用的是 TypeScript，您确实需要更多，因为我们希望我们的容器公开类型化的方法:

```
/* --- ./di-container.ts --- */
export default { } as any as {
  postController: () => PostController
};
```

这里我们对 TypeScript 撒谎，声称容器有一个方法返回一个`PostController`(在 [GitHub](https://github.com/tmj-code/understandable-dependency-injection-in-javascript) 上的扩展示例中有一个稍微好一点的解决方案)。

缺少的方法将使我们的视图失败，就像在 JavaScript 版本中一样，除非我们采取措施来确保该方法确实存在。那么我们在哪里做呢？

当在浏览器或服务器上运行我们的应用程序时，我们通常会有一个入口点:例如，在前端，一个 HTML 页面会加载`main.js`，而一个服务器进程会在后端加载`index.js`。测试时，每个测试都是自己的入口点。

如果我们将创建代码放在这个入口点，我们就能够在现实生活中使用一个设置，在测试中使用另一个设置(或多个设置)。它可能看起来像这样:

```
/* --- ./main.js or ./index.js --- */
import container from './di-container';
import PostService from './post-service';
import PostController from './post-controller';const postServiceFactory = () => 
  new PostService(/* possibly some arguments */);
const postControllerFactory = () => 
  new PostController(postServiceFactory());container.postController = postControllerFactory;Object.freeze(container);/* ... more application initialization code ... */
```

这看起来和以前几乎一样，除了现在是应用程序入口点定义如何设置服务。我们可以在测试设置中有类似的代码，只是带有模拟的服务。

事实上，在测试中，我们只需要模拟那些与每个测试相关的服务，例如省略`PostService`设置。

```
/* --- ./post-view-test.js */
import PostControllerMock from './post-controller-mock';const postControllerFactory = () =>
  new PostControllerMock(/* some test data */);
container.postController = postControllerFactory;Object.freeze(container);/* ... test code ... */
```

# 添加更多服务

添加更多服务时，您需要执行以下操作:

*   在`index.js`(或者无论你的入口点是什么)中，你创建服务工厂，并在`Object.freeze`之前将其设置为容器上的一个属性。

对于 TypeScript(还有一步):

*   在`di-container.ts`中，您将方法定义添加到容器导出中，就像我们对上面的`PostController`所做的一样。

如果您有许多服务(这可能是典型的情况)，您应该考虑将容器设置转移到一个单独的文件中，以保持您的`index.js`较小。请参见扩展示例。

# 摘要

依赖注入看起来很神奇，但是这里我展示了一种既容易理解又容易使用的方法。它对 JavaScript 和 TypeScript 都有效，并且在运行测试时也有效。

好处:

*   易于理解，因为没有魔法(第三方包)或高级语言功能涉及。
*   集中化，因此您的应用程序只需要在一个地方进行配置。
*   随着应用程序的增长，易于扩展新的服务。
*   支持测试，因为您使用模拟服务为每个测试单独设置了容器。

此外，对于 Typescript:

*   类型安全，因为类型是直接从容器中推断出来的。
*   可跟踪，因为您可以跟踪每个服务，直到它被创建的地方。

你以前没有使用过依赖注入吗？那就考虑试试吧。你会很快发现它使应用程序更简单，更容易理解，更容易维护和更新。