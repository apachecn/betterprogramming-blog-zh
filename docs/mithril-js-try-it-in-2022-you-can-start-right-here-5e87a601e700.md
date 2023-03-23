# 试试 Mithril.js:一个现代的 JavaScript 前端框架

> 原文：<https://betterprogramming.pub/mithril-js-try-it-in-2022-you-can-start-right-here-5e87a601e700>

## 通过构建 CRUD 应用程序，在 2022 年开始使用 Mithril.js

![](img/8d70e88560323edf8eecab2fda8a00a4.png)

新年伊始

这个世界上有很多前端的 JavaScript 框架，任何一个都不能掉以轻心。但是有一个您可能还没有尝试过，但是您应该尝试一下:Mithril.js。

我在这里不打算谈论 Mithril.js 为什么如此伟大。相反，我将向您展示如何开始。

在本文中，我们将构建一个简单的应用程序，显示来自[https://jsonplaceholder.typicode.com/posts](https://jsonplaceholder.typicode.com/posts)的帖子列表，并通过 6 个简单的步骤提供 CRUD 操作:

1.  初始化项目。
2.  设置应用程序组件。
3.  设置页面之间的传送方式。
4.  实现列表。
5.  实施创建/更新表单。
6.  添加删除按钮。

如果你想看最终结果，你可以在 [GitHub](https://github.com/tmj-code/mithril-simple-demo-with-posts) 或 [CodePen](https://codepen.io/tmj-code/pen/XWeZdmw) 上找到。在这里，我还添加了一些本文中没有提到的样式(只是为了证明我不是设计师)。

# 1.初始化项目

虽然你可以在公共 CDN 上通过引用[核心 Mithril.js 文件轻松构建，但是我们将使用一个 bundler，特别是](https://unpkg.com/mithril@latest/mithril.min.js) [Vite.js](https://vitejs.dev) 。我们还将使用 [TypeScript](https://www.typescriptlang.org/) ，尽管用 JavaScript 也同样容易。

首先，我们创建基本的应用程序并安装依赖项。我们将这个项目命名为`mithril-simple-demo-with-posts`，并使用 Vite 模板`vanilla-ts` (或者`vanilla-js`，如果你是用普通的 JavaScript 做的话):

```
npm init vite@latest mithril-simple-demo-with-posts — — template vanilla-ts
cd m-demo
npm install
npm install mithril — save
npm install [@types/mithril](http://twitter.com/types/mithril) — save-dev
```

如果你愿意，你也可以用`yarn` 或`pnpm` 来做这件事。

# 2.设置应用程序组件

现在我们创建应用程序组件。它将呈现我们应用程序的根元素(`<div class="app">...</div>`)，并将是我们应用程序中所有其他组件的容器。将`src > main.ts`的内容替换为:

```
import './style.css';
import m from 'mithril';function App(): m.Component {
  return {
    view: () => m('div.app',
      m('header', 'Mithril Demo'),
      m('main', '[some content]'),
      m('footer', 'Mithril is simple yet powerful')
    )
  };
}m.mount(document.body, App);
```

这里发生了什么？

*   我们从`node_modules`导入 Mithril.js，我们称导入为`m`。这是一个附加了几个属性的函数。
*   我们定义了一个函数`App`，它返回一个 Mithril 组件。一个 Mithril 组件是任何具有返回某些东西的*视图*方法的对象。由 app 函数返回的组件我们此后将称为 App 组件。
*   view 方法调用`m`作为一个函数，看起来像 CSS 选择器(`*div.app*`)作为第一个参数。这将被渲染为`<div class="app"></div>`。对`m`的多次调用被作为参数添加到第一次调用中，这些调用将被呈现为嵌套元素。
*   最后，我们告诉 Mithril 呈现 App 函数返回的任何内容，并将其放入`document.body`元素中。

现在，通过在控制台中编写`npm run dev`来运行 Vite，然后导航到该站点。你会在一个带有类 app 的`div`中看到文本`‘[some content]’`。

只是为了清理，打开文件`index.html`，删除 id 为`app` *的 div 我们不需要它。*

# 3.设置页面之间的传送方式

我们首先将这三个函数添加到`src > main.ts`来开始路由:

```
function PostList(): m.Component {
 return {
  view: () => 'Post list'
 };
}function PostForm(): m.Component {
 return {
  view: () => 'Post form'
 };
}function setupRouting(root: Element): void {
 m.route.prefix = '';

 m.route(root, '/', {
  '/': PostList, // list of posts
  '/posts': PostForm, // form to create a post
  '/posts/:id': PostForm // form to update a post
 });
}
```

前两个是组件的函数，就像 App 函数一样。现在他们渲染一些虚拟文本。

`setupRouting`功能更有趣。下面是它的作用:

*   首先，它从 DOM 中获取一个 HTML 元素作为输入。一会儿会用到这个。
*   通过将`m.route.prefix`设置为空字符串，我们定义了如何从浏览器 URL 解析路由。省略它，Mithril 将——像其他前端框架一样——使用 hashbang ( `#!`)。这将为我们提供类似 [*的 URL http://localhost:3000/#！/posts/1*。通过将它设置为空字符串，我们得到了](http://localhost:3000/#!/posts/1)[*http://localhost:3000/posts/1*](http://localhost:3000/posts/1)。这两种方法都有合理的理由，但是这里我们将使用空字符串。
*   我们用三个参数调用`m.route(...)`。第一个是 HTML 元素，它将充当*路由容器*——所有路由的内容都将显示在这个元素中。第二个参数是在浏览器导航到没有定义路由的 URL 时使用的备用路由。第三个参数是一个将路由(URL 路径)映射到组件函数的对象。
*   最后看路线`/posts/:id`。这个路由将 URL 的一部分映射到变量 *id* 。我们可以在组件中检查路由变量，并根据我们发现采取行动。

我们需要从某个地方调用`setupRouting`，为它提供一个 HTML 元素。在`App`功能中，将`m('main', …)`行更改如下:

```
m('main', { 
    oncreate: (vnode: m.VnodeDOM) => setupRouting(vnode.dom) 
})
```

当`m`的第二个参数是非组件对象时，它用于配置由第一个参数创建的元素。在这种情况下，我们正在挂接一个 Mithril 的生命周期挂钩:`oncreate`。当 Mithril 在 DOM 中创建了元素并公开了一个附加了 DOM 元素的虚拟节点(元素的内部表示)时，这个钩子被调用。我们将 DOM 元素交给`setupRouting`。

现在转到浏览器，输入 [http://localhost:3000](http://localhost:3000) 以查看`main`元素中的“帖子列表”文本，输入[http://localhost:3000/posts](http://localhost:3000/posts)(或[http://localhost:3000/posts/ABC](http://localhost:3000/posts/abc))以查看文本“帖子表单”。

# 4.实现一个列表

我们现在要实现`PostList`组件。添加以下接口(为了 TypeScript 的方便)，并更新`PostList`函数:

```
interface IPost {
  serId: number;
  id: number;
  title: string;
  body: string;
}function PostList(): m.Component {
  let posts: IPost[] = [
  { userId: 1, id: 1, title: 'Dummy post 1', body: 'dummy post 1' },
  { userId: 1, id: 2, title: 'Dummy post 2', body: 'dummy post 2' }
 ]; return {
    view: () => m('section.post-list',
      m('ul',
        posts.map(post => 
          m('li',
            m(m.route.Link, { 
              href: `/posts/${post.id}`
            }, post.title)
          )
        )
      ),
      m(m.route.Link, { 
        href: '/posts', className: 'button' 
      }, 'Add new post')
    )
  };
}
```

这里发生了什么？

*   在视图方法中，我们创建一个带有项目符号列表的部分( *ul* )。请注意，我们还在该部分设置了一个 CSS 类。
*   我们使用 JavaScript `Array.map(...)`函数迭代文章来生成列表项；对于每个帖子，我们返回一个列表项(`m('li')`)。
*   在列表项中，我们正在创建一个特殊的元素— `m.route.Link`。这实际上只是一个 link ( *a/anchor* )元素，具有特殊的点击处理，确保页面在被点击时不会重新加载，而是改变路径。除了“防止重新加载”处理，这创建了一个正常的锚元素。
*   作为第二个参数，我们提供了一个对象。这一次，它既不附加到生命周期挂钩(如`oncreate`)也不附加到 DOM 事件(如`onclick`)。相反，它包含一个属性值(`href`)，由 Mithril 分配给元素。
*   我们还添加了一个创建新帖子的链接。这个链接我们给了类`button`——但是因为我们使用了`m(m.route.Link)`，我们不能简单地使用`m('a.button')`符号；相反，我们将其设置为第二个参数对象中的一个属性。设置类的两种方法产生相同的结果。

现在在浏览器中查看，你会看到一个帖子列表，带有有效的链接。

接下来，返回到`PostList`函数来加载一些真实数据:

```
function PostList(): m.Component {
  let posts: IPost[] = [];

  m.request<IPost[]>({
    method: 'GET',
    url: '[https://jsonplaceholder.typicode.com/posts'](https://jsonplaceholder.typicode.com/posts')
  }).then(data => posts = data);

  return ...
}
```

所以:

*   我们从 posts 数组中删除了虚拟数据，因为我们现在将获得真实数据。
*   我们用一个定义了 HTTP 方法和 URL 的 options 对象调用 *m.request* 。m.request 返回一个带有数据的承诺。
*   当使用 TypeScript 时，我们给`m.request<IPost[]>`添加了一个类型参数，这样我们就可以推断出承诺中的数据类型。

再次在浏览器中查找。您现在有一个帖子列表。神奇！

# 5.实施创建/更新表单

我们将更新`PostForm` 函数，并添加一个函数来保存帖子:

```
function savePost(post: IPost): void {
  const isCreate = post.id === -1;
  m.request({
    method: isCreate ? 'POST' : 'PUT',
    url: '[https://jsonplaceholder.typicode.com/posts'](https://jsonplaceholder.typicode.com/posts') + 
      isCreate ? '' : `/${post.id}`,
    body: post
  });
}function PostForm(): m.Component {
  let post: IPost = {
    userId: -1,
    id: -1,
    title: '',
    body: ''
  }; const id = m.route.param('id');
  if (id) {
    m.request<IPost>({
      method: 'GET',
      url: `[https://jsonplaceholder.typicode.com/posts/${id}`](https://jsonplaceholder.typicode.com/posts/${id}`)
    }).then(data => post = data);
  } return {
    view: () => m('form',
      m('label', 
        m('span', 'Title'), 
        m('input[type="text"]', { 
          value: post.title,
          onchange: (e: Event) => 
            post.title = (e.target as HTMLInputElement).value
        })
      ),
      m('label', 
        m('span', 'Body'), 
        m('textarea[rows="5"]', { 
          value: post.body,
          onchange: (e: Event) => 
            post.body = (e.target as HTMLTextAreaElement).value
        })
      ),
      m('button.button[type="button"]', { 
        onclick: () => savePost(post) 
      }, 'Save')
    )
  };
}
```

浏览代码:

*   在`savePost`中唯一的新东西是我们给`m.request`增加了一个身体。
*   在`PostForm`中，我们创建一个表单。它有两个输入字段(其中一个实际上是一个`textarea`)和一个按钮。
*   我们通过添加一个对象作为`m`函数的第二个参数来设置输入的属性。我们还设置了`onchange`事件处理程序来从输入字段更新帖子的值。
*   注意我们如何直接在`m`函数的第一个参数中设置简单的属性，使用与 CSS 选择器中相同的语法。我们可以把它们放在第二个参数的对象中，就像我们对`m.route.Link`的`href`所做的那样——但是我想演示这样做的方法。最终，这主要是一个品味和可读性的问题。
*   作为最后一步，我们包括一个按钮来保存文章。

# 6.添加删除按钮

作为最后一个 CRUD 操作，我们要实现 delete。我们将返回到 PostList 函数，并在每个列表项中添加一个按钮。该按钮将有一个 click 事件处理程序，向 api 发送删除请求。

实际上，这一部分没有什么新的东西，所以我将它作为一个练习留给你。但是如果你需要帮助，你可以在 GitHub 上看一下完成的代码。

# 摘要

仅仅通过六个简单的步骤，我们就构建了一个 CRUD 应用程序。Mithril 负责渲染、路由和 HTTP 请求，其余的由普通 JavaScript 完成。

到现在为止，你已经看到了秘银是多么容易使用。`m`函数允许你使用 CSS 选择器之类的语法创建元素。组件模型易于使用，并使大型应用程序易于管理。Mithril 自带路由器和 HTTP 请求处理器。

无论你是需要一个快速添加到你的网站还是一个大型的商业应用程序，那么你现在已经知道你需要什么了。

现在，您基本上可以将 Mithril.js 添加到您的工具箱中。如果你想了解更多，去 https://mithril.js.org 吧。