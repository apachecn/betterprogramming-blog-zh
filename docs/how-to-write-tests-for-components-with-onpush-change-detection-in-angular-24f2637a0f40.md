# 如何在 Angular 中为带有 OnPush 变化检测的组件编写测试

> 原文：<https://betterprogramming.pub/how-to-write-tests-for-components-with-onpush-change-detection-in-angular-24f2637a0f40>

## 让 Angular 意识到组件测试中的变化

![](img/0d237876bd613d3d71f2cf77784fd71a.png)

罗斯·芬登在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

Angular 框架做了大量工作来检测变化并相应地更新 UI。与 React 或 Vue.js 等其他框架类似，Angular 支持数据绑定，总是显示最新的数据。

虽然 Angular 已经是一个快速的框架，但我们总是可以改进的。一个改进的想法是减少变更检测的工作量，以保持 UI 尽可能的平滑。对于角度分量，有两种[变化检测策略](https://angular.io/api/core/ChangeDetectionStrategy)可用:

*   `Default`使用默认的`CheckAlways`策略，在该策略中，变化检测是自动的，直到被明确取消激活。如果你没有指定一个策略，那么这个将被使用。
*   `OnPush`使用`CheckOnce`策略，这意味着自动变化检测被禁用，直到通过将策略设置为`Default`重新激活。仍然可以通过使用`[ChangeDetectorRef](https://angular.io/api/core/ChangeDetectorRef)`显式调用变更检测。

`OnPush`变更检测策略是[哑组件](https://www.digitalocean.com/community/tutorials/react-smart-dumb-components)的好选择，哑组件通常:

*   很少或没有内部状态。
*   声明`[Input](https://angular.io/api/core/Input)`属性以允许它们的父元素提供数据。
*   借助`[Output](https://angular.io/api/core/Output)`属性与其父元素通信。

可悲的是，使用`OnPush`变更检测策略的组件在单元测试中更难测试。问题是这样一个组件在第一次调用`[fixture.detectChanges()](https://angular.io/api/core/testing/ComponentFixture#detectchanges)`的时候只会运行一次变更检测。GitHub 上有一些流行的问题([例子](https://github.com/angular/angular/issues/12313))凸显了这个问题的严重性。让我们来看看解决这个问题的一些思路:

*   您可以尝试以这样一种方式编写您的测试代码，即您只需要触发一次变更检测。然而，这可能很麻烦，并且一些测试需要多次变更检测运行。
*   您可以将您的组件包装在另一个组件中。只要包装器组件提供的组件的`Input`属性被更改，Angular 就会发现这些更改。对于这种方法，您需要创建一个包装器组件，这样就不必处理变更检测问题。

这些方法是可行的，但是在我看来，它们需要付出更多的努力。开源项目的一个优势是，即使没有官方支持的方法，社区通常会找到一种方法。现在，我想和你们分享社区解决这个问题的方法。

# 如何在测试中触发 OnPush 组件的变更检测

不满足于必须处理这个问题，我尝试了多种方法来触发测试中这类组件的变更检测。在寻找解决方案时，我偶然发现了一个巧妙的功能，我想与您分享一下:

每当您需要使用`OnPush`变更检测策略对一个组件运行变更检测时，您可以调用这个函数(确保使用`await`，因为它会返回一个`Promise`)，您的变更应该会生效。您需要提供的唯一参数是一个`ComponentFixture`，它通常是在使用`[TestBed](https://angular.io/api/core/testing/TestBed)`创建组件时得到的。

# 结论

感谢阅读这篇文章。使用`OnPush`变化检测策略是通过减少角度应用中变化检测的数量来提高性能的好方法。通过使用上面概述的一个简单的助手函数，我们可以很容易地在组件测试中随时触发变更检测——甚至对于`OnPush`组件。我希望 Angular 团队将来能让`OnPush`组件的变更检测变得更容易，而不必求助于这种变通方法。

你知道其他可靠地检测`OnPush`组件的单元测试变化的方法吗？请在评论中告诉我。