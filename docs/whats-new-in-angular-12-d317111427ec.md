# Angular 12 有什么新功能？

> 原文：<https://betterprogramming.pub/whats-new-in-angular-12-d317111427ec>

## 快速回顾 Angular 的最新功能

![](img/66a05e6481f6e0a85558edcda8b5304b.png)

照片由[尼克·费因斯](https://unsplash.com/@jannerboy62?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

日前，Angular 发布了其最新版本 Angular 12。这篇文章将回顾在步入 Angular 12 之前你需要知道的所有重要的事情。

# 离艾薇更近了一步

众所周知，Angular Ivy 最初是在版本 9 更新中发布的，它成为了 Angular 的游戏规则改变者。所以，Angular 继续它的下一代编译和渲染管道之旅，他们通过**弃用视图引擎** *又迈出了一步。*

View Engine 是 Angular 早期版本中使用的编译管道，它将在下一个主要版本中被完全删除。

然而，开发人员不需要太担心这一点，因为 Ivy 将支持依赖于视图引擎的库。如果没有，你也可以使用 Angulars 兼容性编译器`ngcc`。

但是，如果你是一个图书馆作者，现在是时候搬进常春藤了。

# 量角器将不包括在新项目中

量角器于 2013 年推出，已广泛用于 E2E 测试。但是随着时间的推移，JavaScript 生态系统发生了显著的变化，各种替代 E2E 测试的方法被引入，并带来了令人兴奋的特性。

去年四月，Angular 团队开设了一个 GitHub 论坛，听取开发者关于他们使用的 E2E 框架的意见。根据反馈，Angular 团队决定停止对量角器的支持，并在 Angular CLI 中使用第三方解决方案。

根据发行说明，Angular 目前正在与 Cypress、WebdriverIO 和 TestCafe 就迁移进行合作。

所以，如果你在你的项目中使用量角器进行 E2E 测试，你将不得不转移到一个新的 E2E 测试框架。但是，您不必惊慌，因为 Angular 将提供您需要的所有迁移指南。

# 无效合并(？？)

Nulish 合并，它使用。？运算符，在 Typescript 中广泛使用。这有助于编写更整洁的代码。

> 随着版本 12 的更新，Angular 支持 nullish 合并，您可以使用它来简化 Angular 模板中的条件语句。

```
{{total !== null && total!== undefined ? total: calculateHeight() }}//bocomes{{}total ?? calculateTotal()}
```

# 风格改进

Angular 12 中引入了几项激动人心的风格改进。为了更好地理解，我将它们列举如下:

*   角度组件现在支持**内嵌 Sass。**以前，Sass 仅在外部资源中可用。您可以通过将`“inlineStyleLanguage”: “scss”`添加到`angular.json`文件来启用此功能。
*   从 v11.2 开始支持顺风 CSS。
*   棱角分明的 CDK 和棱角分明的材质内部采用了[萨斯的新模块系统](https://sass-lang.com/blog/the-module-system-is-launched)。
*   Angular CDK 和 Angular Material 公开了一个新的 Sass API 表面，它是为使用新的`@use`语法而设计的。
*   如果您正在从以前的版本升级，`ng update`会自动将您的项目切换到新的 Sass API。

# 其他变化

除此之外，Angular 12 带来了几个令人兴奋的变化，以改善整体开发人员的体验:

*   `ng build`命令现在默认为生产。这将有助于防止意外部署。
*   IE11 支持已被弃用，将在 Angular 13 中移除，以便为新浏览器提供更好的解决方案。
*   在以前的版本中，基于 Ivy 的语言服务是一项可选功能。对于 v12，此语言服务将是默认的。angular 团队认为，这次升级将通过在 Angular 模板中提供提示和错误来提高开发人员的生产力。
*   对 Webpack 5 的生产就绪支持。
*   TypeScript 4.2 支持。
*   默认情况下，将启用严格模式。

# 结论

我想你现在对 Angular 12 的重大变化有所了解了。但这些只是 Angular 12 提供的功能的子集，你也可以在他们的博客上找到更多细节。

同时，如果您愿意将您的项目更新到 Angular 12，您只需要运行以下命令:

```
ng update @angular/cli @angular/core
```

感谢您的阅读！

# 资源

*   [棱角分明的博客](https://blog.angular.io/angular-v12-is-now-available-32ed51fbfd49)

[](https://chameeradulanga.medium.com/membership) [## 通过我的推荐链接加入媒体

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

chameeradulanga.medium.com](https://chameeradulanga.medium.com/membership)