# 在短短 5 分钟内将面包屑添加到您的 Angular 应用程序中

> 原文：<https://betterprogramming.pub/add-breadcrumbs-to-your-angular-app-in-just-5-minutes-3119e376e901>

## 谁不喜欢面包屑痕迹呢？

![](img/b3b09129c07d3da9dda161bc93bbcc7a.png)

照片由 [Jamshed Khedri](https://unsplash.com/@jamked?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/trail?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄。

# 为什么要用面包屑？

面包屑是允许用户在嵌套页面之间导航的一种简单有效的方式。通过使用面包屑，Angular 应用程序将允许用户返回到以前的嵌套页面，而不必返回到 web 浏览器或从父页面重新开始。

在本文中，我将涵盖以下内容，以便在任何 Angular 应用程序中设置面包屑:

```
1\. [Setting up Nested Routes with Breadcrumbs](#f796)
2\. [Implimenting Breadcrumbs in Each Component](#cd8b)
```

这是我们将要制作的一个快速演示。样式可以完全根据您的应用程序进行定制，但出于演示的目的，我省略了样式。

![](img/36be933f9a39bd8c29f18ff61288c2b8.png)

面包屑演示

我还会在本文末尾链接完整的项目，请放心使用。

# 使用面包屑设置嵌套路线

下面是两个路由文件。第一个是角度`App`布线文件，第二个是`Component`布线文件。您不一定需要以这种方式构建路由。你也可以在 Angular `App`路由文件中设置所有路由，这取决于你想如何构建你的应用。

应用程序路由文件

这个配置中的`App`路由文件只是加载了`PlatformComponent`及其所有子路由。我喜欢这种设置，因为它允许多个组件，每个组件都有自己的子路由。

我将在`App`路由中指出的主要内容是平台路由中的`data: {breadcrumb: {skip: true }}`对象。这个 breadcrumb 对象是我们将用来将我们的 bread crumb 链接到我们的 routes 的对象(稍后将详细介绍)。这个 breadcrumb 设置为`skip`的原因是这个路由只是加载了`PlatformComponent`路由文件，并不包含实际的组件本身。

平台组件路由文件

`PlatformComponent`路由文件设置有一个主组件(`ParentComponent`)和一个子组件(`ChildOneComponent`)，子组件有两个嵌套子组件(`ChildTwoComponent`)和(`ChildThreeComponent`)。

重要的是要注意每条路线中的`data: {breadcrumb: {alias: '<alias>'}}`物体。这些别名是路由链接到我们的面包屑的方式，因此它们对于每个路由必须是唯一的。

现在是有趣的部分:实现面包屑。

# 在每个组件中实现面包屑

为了实现面包屑，我们将使用 NPM 库 [xng-breadcrumb](https://www.npmjs.com/package/xng-breadcrumb) 。我不会深入探讨这个库的不同风格和定制，而将更多地关注于实现。

首先，我们需要使用`npm i xng-breadcrumb`安装这个库。安装完成后，我们可以将其添加到`app.module.ts`和`platform.module.ts`文件中:

面包屑的应用和平台模块

一旦我们安装了库，我们需要将 HTML 元素添加到我们想要显示面包屑的页面中。这是通过添加特定于库的元素来实现的，如下所示:

xng-breadcrumb HTML 元素

这个实现有两个组件——第一个是实际的 breadcrumb 元素，第二个是分隔符或分隔符。breadcrumb 元素使用由传入`[separator]`属性的元素 ID 标识的分隔符。在我的实现中，我选择使用`chevron_right mat icon ( >)`，但是您可以选择使用不同的图标或字符。

现在我们的页面将显示面包屑，我们需要命名我们的面包屑。面包屑功能实际上并不需要这最后一步。但是，如果不提供名称，将使用路由路径。幸运的是，`xng-breadcrumb`库包含了一个提供名字的服务。这是在组件的 TypeScript 文件中实现的，如下所示:

Angular 提供面包屑名称

为了提供面包屑的名称，我们只需要从`xng-breadcrumb`库中导入`BreadcrumbService`。然后，我们可以使用`set(...)`方法来更改我们的面包屑的名称。其中重要的部分是使用正确的别名作为 set 方法中的第一个参数。在上面的例子中，我们使用别名(`ChildOne`)将路径设置为具有一个`Child One`的面包屑名称。这些别名将在我们的路由文件中设置。

# 结束了！

随着一切的实现，你的 Angular 应用程序现在已经升级到使用面包屑导航！

正如所承诺的，这里是这个演示的完整代码库:

[](https://github.com/EJOzyazgan/Angular-Breadcrumb-Demo) [## EJOzyazgan/Angular-bread crumb-演示

### GitHub 是超过 5000 万开发人员的家园，他们一起工作来托管和审查代码、管理项目和构建…

github.com](https://github.com/EJOzyazgan/Angular-Breadcrumb-Demo)