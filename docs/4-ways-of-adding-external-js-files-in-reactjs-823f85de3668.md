# 在 React 中添加外部 JavaScript 文件的 4 种方法

> 原文：<https://betterprogramming.pub/4-ways-of-adding-external-js-files-in-reactjs-823f85de3668>

## 反应钩子和其他方法

![](img/06624249cc14a982ce7cff271b286f87.png)

凯利·西克玛在 [Unsplash](https://unsplash.com/s/photos/script?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

尽管 npm 包广泛可用，但有时我们可能需要依赖一些外部库，这些库要求我们导入一些 JS 文件。

对于跨应用程序使用的特性，我们可以简单地使用全局`index.html`文件中的`<script>`标签将 JS 文件添加到`head` 中。

但是，对于在特定组件中使用的功能，这没有意义。由于 React 不支持`Component`*中的`<script>`标签，这里有几种将 JS 文件附加到特定组件的方法。*

# *反应脚本标签*

*对于初学者来说，这是加载 JS 文件最简单的方法。*

*[React-script-tag](https://www.npmjs.com/package/react-script-tag) 是一个 npm 包，提供了支持通用渲染的 React `<script>`标签。支持所有标准的`<script>` [属性](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script)，如`async`、`src`、`type`、`defer`，包括`onLoad`和`onError`回调。*

```
***import** ScriptTag **from** 'react-script-tag';const Demo = props => (
**<**ScriptTag type**=**"text/javascript" src**=**"/path/to/resource.js" **/>
)***
```

*我们还有一个`isHydrating`标志，是一个`boolean`值。默认为`false`，如果客户端正在使用服务器渲染，则必须为`true`。*

*你可以在 React 文档中阅读更多关于`hydrate()` [的内容。](https://reactjs.org/docs/react-dom.html#hydrate)*

# *反应头盔*

*[头盔](https://www.npmjs.com/package/react-helmet)是一个 React 组件，管理你对文档头的所有更改。这是另一个简单、初学者友好的包，支持服务器端和客户端渲染。*

*[头盔](https://github.com/nfl/react-helmet)取普通 HTML 标签，输出普通 HTML 标签。*

*注:React 头盔比 react-script-tag 重，如果你的唯一目的是使用`script`标签，我不建议使用。*

# *DOM 方法*

*虽然上面的解决方案很容易实现，但是它需要我们添加额外的包来扩充我们的应用程序。如果你有一些编码经验，那么你可以做:*

*如果您在多个组件中追加 JS 文件，最好有一个自定义文件，它返回一个函数来完成这项工作。*

*在您的组件中:*

*如果您不想在页面重定向/重新加载时保留附加的 JS 文件，那么您应该删除`componentDidUnmount`生命周期方法中的脚本。要实现这一点:*

*在您的组件中:*

# *反应钩*

*如果你是 React 中钩子的粉丝，那么`useEffect`是添加外部 JS 文件的好方法。使用效果钩可以阅读我关于[的文章。](https://medium.com/better-programming/tips-for-using-reacts-useeffect-effectively-dfe6ae951421?source=friends_link&sk=87e8a3137afd3b78b22651508f340c57)*

*给出一个要点`useEffect`类似于 React 类组件中的`componentDidMount`和`componentDidUpdate`生命周期方法。*

*如果你的效果返回一个函数，React 会在清理的时候运行它，基本上就是`componentDidUnmount`生命周期方法。这是我们将删除附加脚本的地方。*

*React 允许我们构建[定制钩子](https://reactjs.org/docs/hooks-custom.html)，这让我们能够将组件逻辑提取到可重用的函数中。将外部 JS 文件附加到组件的逻辑可以存储为如下所示的自定义挂钩:*

*可用于以下组件:*

```
*import importScript from 'customHooks/importScript';const Demo = props => {
  importScript("/path/to/resource.js");
}*
```

# *资源*

*[](https://stackoverflow.com/questions/34424845/adding-script-tag-to-react-jsx) [## 将脚本标签添加到反应/JSX

### 您希望在每次渲染该组件时都反复获取并执行脚本，还是只在…

stackoverflow.com](https://stackoverflow.com/questions/34424845/adding-script-tag-to-react-jsx) [](https://www.npmjs.com/package/react-script-tag) [## 反应脚本标签

### 特性:支持通用渲染客户端渲染的 React 标签正确地将脚本标签附加到页面上…

www.npmjs.com](https://www.npmjs.com/package/react-script-tag) [](https://github.com/nfl/react-helmet) [## NFL/react-头盔

### 这个可重用的 React 组件将管理您对文档头的所有更改。头盔采用普通的 HTML 标签和…

github.com](https://github.com/nfl/react-helmet) 

感谢阅读！*