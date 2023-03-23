# React Router 6 分步指南

> 原文：<https://betterprogramming.pub/a-step-by-step-guide-on-react-router-6-8964c10af364>

## React 路由器的综合示例

![](img/4e81cc15a63b7aaa6f3a7badb7c493c9.png)

由[🇸🇮·扬科·菲利](https://unsplash.com/@itfeelslikefilm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

对于 web 应用程序，路由是根据提供的 URL、参数或用户对按钮、链接、图标等的操作来呈现整个或部分页面的机制。

React 本身不包括路由。`[react-router](https://npm.im/react-router)`兼容 React 最新版本，是 React 应用最受欢迎的路由选择。React Router 支持在各种组件的视图之间导航。它读取和控制浏览器 URL，并保持 UI 与 URL 同步。

React 路由器 6 于 2021 年 11 月 3 日发布。让我们过一遍如何使用它的细节。

# 导航术语

首先，我们回顾一些在浏览器导航中经常使用的术语。

## 统一资源定位器

URL(统一资源定位器)是网络上给定的唯一资源的地址。浏览器有一个地址栏，供用户键入特定的 URL，或者以编程方式填充。

`URL`接口用于解析、构造、规范化和编码 URL。它有许多可以读取和修改的属性。

创建一个 URL:

```
const url = new URL('https://john:password@www.mycompany.com:8080/users/?id=5#profile');
```

下面是生成的对象:

`url.searchParams.get('id')`返回 5。

## 位置

Location 是一个表示 URL 位置的对象。它基于浏览器的`window.location`对象。

在浏览器的地址栏中键入 URL，`www.google.com`，并且`window.location`被设置为以下值:

下面是 React 路由器中的`Path`和`Location`定义:

## 历史

有两个历史 API 可以跟踪和操作浏览器历史:

*   `history` API:它在概要文件的生命周期内跟踪用户在任何选项卡中访问过的主框架 URL。
*   `History`接口:管理浏览器会话历史。即它跟踪每个标签中的导航。

由于`History`界面在现代浏览器中更有意义，`window.history`包含了一个`History`的条目。这里有一个`window.history`的例子:

下面是 React 路由器中的`History`定义:

# 建立工作环境

React 路由器发布了 3 个包:

*   `react-router`包含 React 路由器的大部分核心功能，包括路由匹配算法和大部分核心组件和钩子。
*   `react-router-dom`包含了来自`react-router`的所有东西，并添加了一些特定于 DOM 的 API，包括`<BrowserRouter>`、`<HashRouter>`和`<Link>`。
*   `react-router-native`包含了来自`react-router`的所有内容，并添加了一些专门针对 React Native 的 API，包括`<NativeRouter>`和一个本地版本的`<Link>`。

对于 web 应用程序，我们需要的只是`react-router-dom`。

一如既往，我们构建了 [Create React App](/an-in-depth-guide-for-create-react-app-5-cra-5-b94b03c233f2) 环境:

```
npx create-react-app react-router
cd react-router
```

设置`react-router-dom`:

```
npm i react-router-dom
```

此外，安装了`[lorem-ipsum](https://github.com/knicklabs/lorem-ipsum.js)`来为页面生成 lorem ipsum 占位符文本。Lorem ipsum 文本通常用作出版、图形设计和 web 开发中的占位符文本。

```
npm i lorem-ipsum
```

`react-router-dom`和`lorem-ipsum`成为`package.json`中`[dependencies](/package-jsons-dependencies-in-depth-a1f0637a3129)`的一部分。

# 设置路线

路由器是一个有状态的顶级组件，它使所有其他导航组件和挂钩都可以工作。React 路由器有`BrowserRouter`、`HashRouter`、`StaticRouter`、`NativeRouter`、`MemoryRouter`。对于 web 应用程序，通常使用`BrowserRouter`。一个应用程序应该有一个`<BrowserRouter>`，它包装了一个或多个`<Routes>`。

`<Routes>`检查其所有的`children` `<Route>`元素，以找到最佳匹配，并呈现 UI 的该部分。

`<Route>`被定义为一个对象或一个路线元素。如果是物体，物体的形状为`{ path, element }`。如果是路由元素，组件的形状为`<Route path element>`。当路径模式匹配当前 URL 时，`element`属性被渲染。

我们准备在`src/Pages.js`管理几个页面:

第 3–10 行定义了函数`getPage`。它生成一个标题页面，页面内容由`loremIpsum()`生成，包含 5 个随机的句子(第 7 行)。

`getPage`函数用于生成`PageOne`(第 12 行)和`PageTwo`(第 13 行)。下面是`PageOne`的样子，和`PageTwo`看起来差不多。

![](img/14cccea5e6b2f55f89701ecee214bfd6.png)

在`src/App.js`中，创建了两条路线:

`<BrowserRouter>`和`<Routes>`用于定义路由器(第 6-11 行)。

app 里有两个`<Route>`。当 URL 与路径`"one"`匹配时，app 显示`PageOne`(第 8 行)。当 URL 匹配路径时，`"two"`，app 显示`PageTwo`(第 9 行)。

通过运行命令`npm start`执行应用程序。

`http://localhost:3000/one`显示`PageOne`。

![](img/02b4d0b37192b89d8db926eed7e48a74.png)

作者图片

`http://localhost:3000/two`显示`PageTwo`。

![](img/927bacce522e341487c0bb67bef9f056.png)

作者图片

该应用程序适用于路径`"one"`和`"two"`。但是，`http://localhost:3000`不显示任何内容，和任何无效的 URL 一样，比如`http://localhost:3000/anything`。

这个问题可以通过通配符路径(第 8 行)来解决:

`http://localhost:3000/two`显示`PageTwo`。否则显示`PageOne`。

由于 React Router 6 足够智能，可以选择最具体的匹配，因此路由顺序并不重要。

# 设置嵌套路线

上例中的两条路线如预期的那样工作。但是，在浏览器地址栏中键入 URL 并不方便。我们希望通过点击一个链接来获得导航功能，这个链接就是`<Link>`。

`<Link>`用一个真实的`href`呈现一个可访问的`<a>`元素，该元素指向它所链接的资源。单击该链接可以设置 URL 并跟踪浏览历史。

`src/MainPage.js`是用`<Link>`创造的 s:

第 4–13 行用一组导航链接定义了`<nav>`元素。

第 7 行是指向路径`"/one"`的链接。链接文字为`"Page One"`。

第 10 行是指向路径`"/two"`的链接。链接文字为`"Page Two"`。

`<Link to>`值可以相对于呈现它们的路线的路径。即指定路径时不带前导`/`。或者，7 号线可以指向`"one"`，10 号线可以指向`"two"`。

在`src/App.js`中使用`MainPage`:

9 号线是一条`index`路由，是一条没有路径的子路由。当网址匹配`"/"`时，app 显示`MainPage`。这是索引页:

![](img/72b789176408875c22fca7e5c9df78c1.png)

作者图片

点击`Page One`链接，进入`PageOne`。点击`Page Two`链接，进入`PageTwo`。

然而，在`PageOne`或`PageTwo`中，我们不能使用链接来导航。为了解决这个问题，我们在`MainPage`中创建了`<Outlet>`组件。outlet 组件呈现路径中的下一个匹配(`PageTwo`的`"one"`和`PageTwo`的`"two"`)。

这是带出口的`src/MainPage.js`:

第 15 行是`<hr>`元素，它是分隔内容的水平标尺。

第 16 行是呈现活动子路线的`<Outlet>`组件。

`<Outlet>`调出嵌套路由，其中每个路由可以有子路由来占据 URL 的一部分。嵌套路由通常使用相对链接(第 8 行和第 11 行)。

下面是修改后的`src/App.js`:

在第 9 行，顶层的 route 是`"/"`，它呈现了`MainPage`组件。它下面有三个子路由:

*   索引路径(第 10 行):它显示文本，`No page is selected`。

![](img/d40c97ac2241560898781ab0e320cec0.png)

作者图片

*   通配符路由(第 11 行):它显示了组件，`PageOne`。

![](img/eccb86b75b67044b98ed65110e82830f.png)

作者图片

*   `"two"`路线(第 12 行):显示组件`PageTwo`。

![](img/825ded75f6d7d39059faf25dc087ff7a.png)

作者图片

# 用户出口挂钩

代替`<Routes>`，我们也可以使用钩子`useRoutes`来完成同样的事情。

`src/App.js`中的嵌套路线可以用`useRoutes`重写:

在第 6–16 行，我们使用钩子`useRoutes`，从数据结构生成`routes`对象。

我们必须创建`AppWrapper`来将`routes`包装在`<browserRouter>`中(第 20–26 行)。否则，我们会遇到以下错误:

```
Error: useRoutes() may be used only in the context of a component.
```

# 使用位置

Location 是一个表示 URL 位置的对象。它基于浏览器的`window.location`对象。

钩子`useLocation`返回当前位置对象。

在第 7 行，`location`由钩子生成，`useLocation`。

第 8–10 行定义了`useEffect`，如果位置值发生变化，它将记录位置值。

*   当 URL 为`http://localhost:3000/`时，控制台记录:

```
Current location is {pathname: '/', search: '', hash: '', state: null, key: 'default'}
```

*   当 URL 为`http://localhost:3000/one`时，控制台记录:

```
Current location is {pathname: '/one', search: '', hash: '', state: null, key: 'f2114bru'}
```

*   当 URL 为`http://localhost:3000/two`时，控制台记录:

```
Current location is {pathname: '/two', search: '', hash: '', state: null, key: 'if9ngy0q'}
```

*   当 URL 为`http://localhost:3000/anything`时，控制台记录:

```
Current location is {pathname: '/anything', search: '', hash: '', state: null, key: 'default'}
```

# useParams 挂钩

为了让这个例子更有趣，我们嵌套了更多的路由。在`MainPage`上，除了`"Page One"`和`"Page Two"`的链接，还有更详细的`"P1"`(第 1 段)和`"P2"`(第 2 段)的链接。

![](img/59430780152cf9d200f4f2f6b7dbdcf5.png)

作者图片

下面是修改后的`src/App.js`:

在路径`"one"`(第 23 行)下，有另一个子路由，带有动态参数`":id"`(第 25 行)。

在路径`"two"`(第 28 行)下，有另一个子路由，带有动态参数`":id"`(第 30 行)。

下面是改装过的`src/MainPage.js`:

在链接`"Page One"`旁边，添加了链接`P1`(路径`"one/1"`)和 P2(路径`"one/2"`)(第 9-10 行)。

在链接`"Page Two"`旁边，添加了链接`P1`(路径`"two/1"`)和`P2`(路径`"two/2"`)(第 13–14 行)。

`useParams`钩子从当前 URL 返回动态参数的键/值对对象。这里是修改后的`src/Pages.js`，使用`useParams`挂钩:

因为在 React 组件中必须使用一个钩子，所以第 4–14 行将`getPage`函数改为`BuildPage`组件。

第 5 行使用钩子`useParams`来检索动态路径`id`。`id`值显示在内容的开头(第 10 行)。

14 线通过`BuildPage`产生`PageOne`。

15 线通过`BuildPage`产生`PageTwo`。

当 URL 为`http://localhost:3000/one/1`时，控制台记录:

```
Current location is {pathname: '/one/1', search: '', hash: '', state: null, key: 'qpdrrtyg'}
```

# `useNavigate`

`useNavigate`钩子返回一个函数，该函数可用于编程导航。将`src/MainPage.js`中的两个`<Link>`替换为`<button>`:

4 号线返回`useNavigate`钩。

第 10–12 行定义了按钮`"Page One"`，其中`onClick`处理程序导航到相对路径`"one"`。

第 16–18 行定义了按钮`"Page Two"`，其中`onClick`处理程序导航到相对路径`"two"`。

运行应用程序。按钮的作用类似于链接。

![](img/89dec8f0c0bdbae3c4d9896e6b764946.png)

作者图片

`navigate`函数有两个签名:

*   用可选的第二个参数`{ replace, state }`传递一个`To`值(与`<Link to>`类型相同)。
*   将增量编号传递到历史堆栈中。比如`navigate(-1)`就相当于点击了返回键。

# 其他挂钩

我们已经展示了如何使用`useLocation`、`useNavigate`、`useParams`和`useRoutes`。

还有其他 React 路由器挂钩:

*   `useHref`:它返回一个 URL，可以用来链接到给定的`to`位置，甚至在 React 路由器之外。
*   `useLinkClickHandler`:构建自定义链接时，返回一个用于导航的 click 事件处理程序。
*   `useInRouterContext`:如果组件正在`<Router>`的上下文中呈现，则返回`true`，否则返回`false`。
*   `useNavigationType`:返回用户如何到达当前页面，通过历史堆栈上的`Action.Pop`、`Action.Push`或`Action.Replace`。
*   `useMatch`:返回给定路径相对于当前位置的路径匹配数据。
*   `useOutlet`:返回该层路由层次中子路由的元素。
*   `useResolvedPath`:根据当前位置的路径名，解析给定`to`值中位置的`pathname`。
*   `useSearchParams`:用于读取和修改当前位置的 URL 中的查询字符串。

# 结论

React Router 6 功能丰富，易于使用。它与 React 的最新版本兼容。由于它从版本 5 引入了一些突破性的变化，建议在升级应用程序之前等待向后兼容包的发布。

感谢阅读。我希望这有所帮助。如果你有兴趣，可以看看[我的其他媒体文章](https://jenniferfubook.medium.com/jennifer-fus-web-development-publications-1a887e4454af)。

*注:感谢 Urian Chang 和 Josh Brown 与我一起评估 React Router 6。*