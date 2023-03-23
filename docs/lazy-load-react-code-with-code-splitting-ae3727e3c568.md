# 用代码分割延迟加载 React 代码

> 原文：<https://betterprogramming.pub/lazy-load-react-code-with-code-splitting-ae3727e3c568>

## 以更优雅和高效的方式加载代码

![](img/834cf2e1ddbc5534fcb28bd99f20db0a.png)

照片由[霍尔格链接](https://unsplash.com/@photoholgic?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

React 是一个用于创建前端视图的库。它有一个庞大的图书馆生态系统与之合作。此外，我们可以用它来增强现有的应用程序。

在本文中，我们将探讨如何通过拆分代码来加快应用程序的加载速度，以便只加载需要的部分。

# 代码分割

我们需要代码分割，这样产品反应包就不会太大。随着我们的应用变得越来越大，如果我们不把它们分开，只在需要的时候加载，产品包也会变得越来越大，加载时间也会变长。

[Create React App](https://github.com/facebook/create-react-app) 内置了代码拆分支持。我们可以使用 React 的`lazy`和`import`函数来实现这一点。

例如，我们可以如下使用这些函数:

`Foo.js`:

```
import React from "react";export default function Foo() {
  return <div>foo</div>;
}
```

`App.js`:

在上面的代码中，我们有了带有`Foo`组件的`Foo.js`。

然后，在`App.js`中，我们有`App`组件，它有`Suspense`来加载回退。UI 有`Foo`导入载荷。我们需要这个，因为`Foo`是在运行时加载的。

`React.lazy`在运行时而不是构建时加载`Foo`组件。它还将捆绑包中的代码分割成一个单独的文件，这样每个捆绑包文件都更小。

如果缺少`Suspense`组件，我们将得到一个错误，告诉我们添加一个回退 UI。

在等待组件加载时，`fallback` prop 接受我们想要呈现的任何 React 元素。我们可以将`Suspense`放在惰性组件之上的任何地方。

将多个元素包装在一个`Suspense`元素中也是可行的。

# 误差边界

为了在其他模块加载失败时优雅地失败，比如发生网络故障时，我们可以使用错误边界组件。

为了使用它们，我们将它们包装在`Suspense`组件周围，如下所示:

`ErrorBoundary.js`:

`Foo.js`:

```
import React from "react";export default function Foo() {
  return <div>foo</div>;
}
```

`App.js`:

在上面的代码中，我们在`ErrorBoundary.js`中添加了`ErrorBoundary`组件。

然后，在`App`中，我们将它包装在我们的`Suspense`组件周围，这让我们可以捕捉加载`Foo`组件时发生的任何错误。

# 基于路由的代码分割

很多 React 应用都是单页应用。他们有将 URL 映射到组件的路径。

我们可以根据路线分割代码。例如，我们可以将 React 路由器路由整合到我们的应用程序中，并根据路由分割代码，如下所示:

`Foo.js`:

```
import React from "react";export default function Foo() {
  return <div>foo</div>;
}
```

`Bar.js`:

```
import React from "react";export default function Bar() {
  return <div>bar</div>;
}
```

`App.js`:

在上面的例子中，我们在所有组件周围添加了`Router`组件。

我们将`Route`放在`Suspense`组件中，这样它们就可以被延迟加载。也就是说，只有当我们在浏览器中加载路线时，它们才会加载。

这也是我们在路线周围需要`Suspense`组件的原因。我们需要回退用户界面，以便在加载路线时显示它。

# 指定出口

`React.lazy`仅支持默认导出。如果我们的模块使用命名导出，那么我们必须创建一个中间模块，将它作为默认导出。

例如，我们可以将代码安排如下:

`Foo.js`:

```
import React from "react";export const Foo = function() {
  return <div>foo</div>;
};
```

`FooDefault.js`:

```
import { Foo } from "./Foo";export default Foo;
```

`App.js`:

在上面的代码中，我们有将`Foo`导出为默认导出的`FooDefault.js`。

然后，我们在`App.js`用`React.lazy`导入。

# 结论

使用 Create React App 和 React 可以轻松实现代码拆分。React 拥有动态导入组件的`lazy`方法。

我们需要一个后备 UI，这样我们就可以在动态导入的组件加载时看到一些东西。

还支持错误边界，当动态导入的组件加载失败时，我们可以使用它来优雅地失败。

React 路由器也支持代码分割。`React.lazy`聪明到可以按路线拆分代码。