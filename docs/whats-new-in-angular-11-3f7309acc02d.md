# Angular 11 有什么新功能？

> 原文：<https://betterprogramming.pub/whats-new-in-angular-11-3f7309acc02d>

## 快速回顾 Angular 的最新功能

![](img/929631f69db55861d695ab8a9d5728f3.png)

由 [Esteban Lopez](https://unsplash.com/@exxteban?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

Angular 刚刚发布了其卓越框架的第 11 版，所以让我们来探索 Angular 的最新特性。

# 组件测试线束

组件测试工具允许测试使用支持的 API 与组件进行交互。这是在 Angular 9 中首次引入的。这些 API 能够以类似于终端用户的方式与组件进行交互，测试可以将自己与 DOM 中的变化隔离开来。

当时这个机构只支持有角的材料。但是在最新版本中，所有组件都可以使用组件线束。

以下示例显示了`MatButtonHarness`在角形材料中的用法:

# 更新了热模块更换(HMR)支持

热模块替换是一种允许在不完全刷新浏览器的情况下替换模块的机制。HMR 对 Angular 来说并不是一个全新的东西，开发者也可以在以前的版本中使用它。但是有了 Angular 11，您需要配置 HMR 的工作量大大减少了，他们为此提供了 CLI 命令。

现在您可以简单地使用带有`hmr`标签的`ng serve` 命令来启用 HMR:

```
ng serve --hmr
```

# TypeScript 4.0 支持

在这个最新的更新中，Angular 团队已经放弃了对 TypeScript 3.9 的支持。现在 Angular 11 只支持 TypeScript 4.0。

这次升级的主要原因之一是为了加快构建速度。Angular 11 确保比以前的版本更快的构建。

# Webpack 5 支持

如你所知，webpack 用于将大量文件编译成单个包或单个文件。Webpack 5 是最新版本，在上个月发布。它还没有完全稳定。

然而，Angular 11 为 webpack 5 提供了实验性支持，您可以将它与 Angular 11 一起使用来尝试新事物。根据发行说明，Angular 团队相信，一旦事情稳定下来，他们可以扩展这种实验性支持，以实现更快的构建和更小的捆绑包。

如果您准备尝试 webpack 5，您可以从将以下几行添加到您的`package.json`文件开始:

```
“resolutions”: {
 “webpack”: “5.4.0”
}
```

# 搬到 ESLint

在很长一段时间里，TSLint 是开发人员中最受欢迎的林挺工具之一。但它最近被弃用，职责移交给了 ESLint。

随着版本 11 的更新，Angular 也从 TSLint 迁移到 ESLint，您将无法在 Angular 中再次使用 TSLint 进行林挺。Angular 推出了从 TSLint 迁移到 ESLint 的三步方法。

## **1。添加相关依赖关系**

```
ng add @angular-eslint/schematics
```

## **2。在项目**上运行 `**convert-tslint-to-eslint**` **原理图**

```
ng g @angular-eslint/schematics:convert-tslint-to-eslint {{YOUR_PROJECT_NAME_GOES_HERE}}
```

## **3。移除根 TSLint 配置，仅使用 ESLint**

移除根级`tslint.json`。

您可以从以下网址找到有关迁移的更多详细信息:

*   [迁移指南](https://github.com/angular-eslint/angular-eslint#migrating-from-codelyzer-and-tslint)
*   [typescript-eslint](https://github.com/typescript-eslint/typescript-eslint)
*   [角度限制](https://github.com/angular-eslint/angular-eslint)
*   [tslint-to-eslint-config](https://github.com/typescript-eslint/tslint-to-eslint-config)

# 更新的语言服务预览

> Angular 语言服务为代码编辑器提供了一种在 Angular 模板中获得完成、错误、提示和导航的方法。它可以在单独的 HTML 文件中使用外部模板，也可以使用内嵌模板。”— [Angular 的文件](https://angular.io/guide/language-service)

以前，这种语言服务是基于视图引擎的。Angular 11 提供了一个新的更加强大和准确的基于 Ivy 的语言服务。

虽然这项功能仍在开发中，但它具有类似于 TypeScript 编译器的行为能力。

# 其他变化

除此之外，Angular 11 带来了大量的小变化，例如:

*   新的自动化迁移和示意图。
*   服务人员的改进。
*   命名出口的延迟加载支持。
*   Angular CLI 现在可以生成 resolve guards。
*   更严格的管道内置类型。
*   `formatDate`函数现在支持 ISO 8601 周数年份格式。

## **一些突破性的变化**

*   Angular 11 完全移除 IE 9，10，IE 移动支持。
*   不再支持 TypeScript 3.9。
*   `preserveQueryParams`已从路由器中移除，可以使用`queryParamsHandling=”preserve”`代替。
*   ICU 中的表达式现在再次进行类型检查。
*   异步管道不再声称为类型为`undefined`的输入返回`undefined`。

# 结论

我想你现在已经对 Angular 11 的一些重要特性有所了解了。但这些只是 Angular 11 提供的功能的子集，你也可以在他们的博客上找到更多细节。

同时，如果您愿意将您的项目更新到 Angular 11，您只需要运行以下命令:

```
ng update @angular/cli @angular/core
```

感谢您的阅读！

# 资源

*   [棱角分明的博客](https://blog.angular.io/version-11-of-angular-now-available-74721b7952f7)