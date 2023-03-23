# 如何在笑话和故事书中使用自定义角形控件

> 原文：<https://betterprogramming.pub/how-to-use-custom-angular-form-controls-in-jest-and-storybook-6479e52b7636>

## 开发功能强大且美观的自定义窗体控件

![](img/4f2f0acd9c35940d3524b4cf76b8b3cf.png)

[万花筒](https://unsplash.com/@kaleidico?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

大多数网站和应用程序都有表单。Angular 提供了许多功能来构建可重用的复杂表单。

你可以构建[模板驱动表单](https://angular.io/api/forms/FormsModule)和[反应式表单](https://angular.io/api/forms/ReactiveFormsModule)。表单不仅应该从用户那里收集数据，还应该提供一个很好的 UX 来避免用户的混乱。

Angular 有一个名为 [ControlValueAccessor](https://angular.io/api/forms/ControlValueAccessor) 的接口，自定义表单控件需要实现这个接口。

模板驱动的表单对于初学者和简单的组件来说是一个很好的选择。另一方面，对于更高级的用法，反应式更强大。

```
<!-- Template-driven Forms -->
<app-select [(ngModel)]="selectValue" (ngModelChange)="onSelectChange()"></app-select>
<!-- Reactive Forms -->
<app-select formControlName="selectValue"></<app-select>
```

如果您有多个前端开发人员和 UX 设计人员，就需要协作来创建一致且易于使用的 ui。 [Storybook](https://storybook.js.org/) 是一个 UI 组件浏览器和开发工具，可以与 Angular、React 等一起使用。

Storybook 提供了一个很好的开发环境，有助于独立地创建 UI 组件。开发人员和设计人员都有一个现有 UI 组件的概述，这有助于创建更加一致的 UI。

如果你以前没有用过 Storybook，Storybook 的网站上有一些来自 IBM 等公司的例子。

虽然漂亮的用户界面看起来很好，但功能性不应该被牺牲。为了确保我们不会意外破坏现有的功能，开发人员会编写不同种类的测试，这些测试通常会在像 [Jenkins](https://jenkins.io/) 或 [GitLab](https://gitlab.com/) 这样的配置项上自动运行。

Jest 是一个流行的 JavaScript 测试框架，对于编写和执行测试来说，它既快速又简单。这使得 Jest 成为编写组件测试的好选择。

在这篇文章中，我将展示如何在 Jest 中测试一个自定义的表单控件 Angular 组件，并在 Storybook 中展示它。

# 如何在故事书中使用自定义角形控件

1.  创建一个包含这些故事的新文件。
2.  导入组件和必要的依赖项。
3.  将必要的道具传递给组件。我们需要传递`ngModel`来启用数据绑定，传递`ngModelChange`来监听自定义表单控件组件的值变化。
4.  可选:提供[旋钮](https://github.com/storybookjs/storybook/tree/master/addons/knobs)来轻松编辑故事书 UI 中的道具，以查看组件的不同状态。

这里有一个使用故事书来创建我们的`JsonFormControlComponent`的两个故事的例子。

使用 Storybook 显示自定义表单控件组件

如果你想了解 Storybook 如何在内部处理自定义的角形控件，请查看 GitHub 上的[源代码。](https://github.com/storybookjs/storybook/blob/d0b93325d94095dbcdb417d49552509eb6148b1d/app/angular/src/client/preview/angular/components/app.component.ts#L117)

# 如何在 Jest 中使用自定义角形控件

1.  创建一个包含测试的新文件。
2.  使用`[TestBed](https://angular.io/api/core/testing/TestBed)`导入您的组件和必要的依赖项。
3.  编写测试用例。我们可以监视触发表单控件值更改的函数，以检查是否发出了正确的值。

这里有一个用 Jest 测试我们的自定义`JsonFormControlComponent`的例子。

使用 Jest 测试自定义表单控件组件

顺便说一下:如果你使用 Jasmine 作为测试框架，上面的例子也是有效的。

# 结论

感谢您阅读这篇关于如何使用 Jest 和 Storybook 在 Angular 中开发和测试自定义表单控件的短文。

你对笑话和故事书有什么体验？请在评论中告诉我。