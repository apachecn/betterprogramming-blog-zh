# 如何加快角度开发并减少编译时间

> 原文：<https://betterprogramming.pub/how-to-speed-up-angular-development-and-reduce-compilation-times-a32262d478bc>

![](img/bad17d3184e800cead7d07ce5e9def24.png)

法比安·格罗斯在 [Unsplash](https://unsplash.com/search/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

[Angular](https://angular.io/) 是一个伟大的 JavaScript 框架，用于创建可以在许多平台上运行的应用程序。

然而，你的应用程序越长，你的编译时间可能会变得越长。这使得编写代码更加麻烦，降低了开发人员的工作效率。您不应该为了编译完成而等待两分钟，只是为了看看这一行修改后的代码是否成功。

当然，如果你的应用程序是一个待办事项应用程序的大小，你可能会对编译时间感到满意。然而，在有多个开发人员和越来越多的第三方依赖项的大型项目中，编译时间会变得相当长。

从 2016 年 Angular 2 最终版发布开始，我就一直在用 Angular。从那以后，我在 Angular 项目中看到并使用了不同的方式来编写更好更快的代码。

以下是我为加速角度发育而挑选的最佳方法:

*   进行 TDD 风格的开发(例如用 [Jest](https://jestjs.io/) )
*   将你的代码分成可以延迟加载的功能模块
*   与角度生态系统保持同步
*   使用[故事书](https://storybook.js.org/)开发 UI 组件
*   将您的应用程序拆分成可以独立构建和测试的较小的应用程序和库

注意:这些步骤中的大部分并不直接与 Angular 相关，但也可以与流行的框架相关，如 [React](https://reactjs.org/) 和 [Vue.js](https://vuejs.org/) 。

我将更多地关注改善开发人员使用 Angular 的体验，而不是改善 Angular 应用程序的实际性能。

# 带有 Jest 的测试驱动开发

默认情况下，Angular 项目结合使用 [Jasmine](https://angular.io/guide/testing) 和 [Karma](https://angular.io/guide/testing) 来运行单元测试。对于简单的项目来说，这可能就足够了，但是对于较大的项目来说，缺点就变得非常明显了。简而言之，Jest 拥有强大的 CLI 和非常愉快的测试体验。

当你在编写简单的 JavaScript 代码(例如纯函数)或处理愚蠢的组件时，[测试驱动开发](https://en.wikipedia.org/wiki/Test-driven_development)方法非常有效。由于单元测试执行起来应该很快，所以你应该只导入你需要的东西，并模仿其他的东西(Jest 可以帮助你)。[我写过一篇如何在角度应用中使用 Jest 的文章](https://itnext.io/how-to-use-jest-in-angular-aka-make-unit-testing-great-again-e4be2d2e92d1)。对于测试角度组件，我推荐使用[测试库](https://medium.com/better-programming/how-to-write-better-ui-component-tests-with-testing-library-e80bc489051c)，它有助于编写尽可能模拟用户行为的测试。

# 特征模块的延迟加载

如果你把所有东西都放在一个模块中，编译时间会更长，因为 Angular 需要重新构建很多东西。

通过使用[延迟加载特性模块](https://angular.io/guide/lazy-loading-ngmodules)，你不仅可以提高你的应用性能(通过加载更小的 JavaScript 包，仅在你需要的时候)，你还可以减少编译时间。

如果你在一个懒惰加载的模块中做了一个改变，它应该只构建受影响的模块，而不是你的整个应用。将代码库分成更小的模块意味着编译器的工作量更少。

# 与时俱进

[TypeScript 增量编译](https://devblogs.microsoft.com/typescript/announcing-typescript-3-4-rc/)是在 TypeScript 3.4 中引入的，从版本 8 开始就在 Angular 中得到支持。

由于它不是默认的(还没有)，您需要在您的`tsconfig.json`中添加`incremental`标志。一旦 Angular 团队有足够的信心让他们违约，Ivy 和 Bazel 将会提高性能。

更新 2021: Ivy 是 Angular 的默认渲染引擎。另一方面，对巴泽尔的支持并没有像人们预期的那样发展。

# 使用故事书创建哑角组件

在 Angular 和 React 等流行框架中，使用哑组件(也称为表示组件)是一种很好的模式。

如果您需要做更多与 UI 相关的任务，TDD 方法可能不够。毕竟，您可能不仅想检查组件是否正常工作，还想检查它在浏览器中的外观。

这就是[故事书](https://storybook.js.org/)发挥作用的地方；一个开源工具，用于为 Angular、React 和 Vue.js 独立开发 UI 组件。

这个想法是，你专注于孤立地创建伟大的哑组件。由于您只需要组件(可能还有一些依赖项)，编译过程相当快，因为您不需要构建整个应用程序。

你甚至可以结合使用 Storybook 和 Jest 来确保你的 UI 和组件的功能都很好。

我将在不久的将来写一篇关于在 Angular 项目中使用 Storybook 的更详细的文章。

# 将您的应用程序拆分成更小的应用程序和库

即使您按照上面的描述进行延迟加载，您也可能会遇到构建和测试时间缓慢的问题。毕竟，您还有一个大的应用程序。像 [NX](https://nx.dev/angular) 这样的开发工具的想法是帮助你将开发从一个团队构建一个应用程序扩展到多个团队在同一个工作空间中构建多个前端和后端应用程序。NX 可以帮助加强边界，创建可以独立构建和测试的库和应用程序。

示例:当您只在应用程序的登录模块中做了一些更改时，您可以运行`ng test nameOfTheLibrary`，而不是运行`ng test`来测试您的应用程序。类似于 Storybook，你也可以创建小的开发应用程序，只导入你需要的东西(例如登录页面)，从而可能允许更快的开发。不用运行`ng serve`来运行完整的应用程序，你可以运行`ng serve nameOfTheApp`来只运行较小的开发应用程序。

# 结论

感谢阅读这篇文章。如您所见，有多种方法可以改善您在 web 开发项目中的开发体验！你知道在有角度的项目中提高开发时间的其他技巧吗？请在评论中告诉我。