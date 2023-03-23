# 每个 Angular 开发人员都应该知道的关于 Zone.js 的 10 件事

> 原文：<https://betterprogramming.pub/zone-js-for-angular-devs-573d89bbb890>

## 每个开发人员都应该了解 Zone.js 的基础知识

![](img/11b2dcfc593bd6d96e9fb72473822df9.png)

Anton Scherbakov 在 [Unsplash](https://unsplash.com/s/photos/meerkat?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 1.我为什么要关心 Zone.js？

Angular 引入了 [Zone.js](https://github.com/angular/zone.js/) 来处理变化检测。这允许 Angular 决定何时刷新 UI。通常情况下，你不必关心这些，因为 Zone.js 就可以工作。

然而，如果 Zone.js 出了问题，分析和理解起来会非常令人沮丧。这就是为什么每个开发人员都应该了解一些关于 Zone.js 的基础知识。

# 2.一言以蔽之:Zone.js 是如何工作的？

Zone.js 修补了所有常见的异步 API，如`setTimeout`、`setInterval`、promise API 等。跟踪所有异步操作。

以下是您应该了解的基本概念:

## **区域**

区域是一种拦截和跟踪异步工作的机制。

## **任务**

对于每个异步操作，Zone.js 都会创建一个任务。任务在一个区域中运行。

## **NgZone**

默认情况下，在 Angular 应用程序中，每个任务都在“Angular”区域运行，这被称为`NgZone`。只有一个角度区域，变化检测仅针对在`NgZone`中运行的异步操作触发。

## **根区域/分支**

Zone.js 区域是分层的，这意味着您总是从顶级区域开始，即“根”区域。可以通过分叉根区域来创建新区域。NgZone 也是根区域的一个分支。

## **区域规格**

派生一个区域时，将基于一个`ZoneSpec`创建一个新的区域。一个`ZoneSpec`可以只包含一个新的子区域的名称，或者可以包含各种钩子方法，这些方法可以用来拦截某些区域/任务事件。

还有更多概念，如果您想了解更多关于 Zone.js 如何工作的信息，您可以在这里找到更多信息:

[](https://blog.thoughtram.io/angular/2016/01/22/understanding-zones.html) [## 了解区域

### 加入我们即将到来的公开培训！获得入场券→在 NG-Conf 2014 上，Brian 就区域以及它们如何……

blog.thoughtram.io](https://blog.thoughtram.io/angular/2016/01/22/understanding-zones.html) 

# 3.您不必将 Zone.js 与 Angular 一起使用(但您可能应该这样做)

通过传递一个`noop` Zone，Zone.js 可以在 Angular 应用程序的引导过程中很容易地被停用。然而，您放弃了变化检测，这意味着您必须自己决定 UI 何时刷新(例如，通过`ChangeDetectorRef.detectChanges()`)。

一般来说，不建议这样做，因为你放弃了自动变化检测的便利，但是，对于自定义元素(角元素)来说，这可能是有意义的。

关于如何禁用 Zone.js 的更多细节可以在 [Software Architect](https://www.softwarearchitekt.at/aktuelles/angular-elements-part-iii/) 找到。

# 4.自动变更检测很好，但是如果我想要控制呢？

在某些情况下，您可能会因为频繁触发变更检测而遇到问题，这可能会导致性能问题。

如果您仍然想要 Zone.js 的好处，但是想要控制什么触发更改检测，什么不触发，您仍然可以这样做。

通过注入`NgZone`，您可以获得一个 API 来决定异步操作是在`NgZone`内部还是外部运行。方法`[runOutsideAngular](https://angular.io/api/core/NgZone#runOutsideAngular)`可用于在`NgZone`之外运行代码，因此不会触发变更检测。

这里有一个例子:

```
constructor(private ngZone: NgZone) { this.ngZone.runOutsideAngular(() => {
    // this will not trigger change detection
    setInterval(() => doSomething(), 100)
  });
}
```

# 5.Zone.js 如何影响量角器测试？

一旦不再运行异步操作，区域就稳定了。“健康”区域开始时稳定，然后运行一些任务，这意味着该区域变得“不稳定”，在某个时间点，任务完成，这意味着该区域再次变得稳定。

这和量角器有什么关系？如果你在 E2E 测试中使用`browser.waitForAngular`，量角器会检查`NgZone`来决定测试是否可以继续。

所以，在你发送给浏览器的每一个命令之后，量角器会一直等到区域稳定下来，然后才继续。例如，如果您使用的`setInterval`间隔时间很短，这将意味着`NgZone`永远不会稳定，您的测试将会冻结和超时。

这类问题可以通过在角度区域之外长时间运行或重复运行任务来解决。

# 6.RxJS 如何与 Zone.js 配合使用？

默认情况下，Zone.js 和 [RxJS](https://rxjs-dev.firebaseapp.com/) 可能不会像您预期的那样运行。例如，您可能将您的可观察代码包装在一个函数中，并将其传递给`NgZone.runOutsideAngular`，并且仍然以运行在`NgZone`中的任务结束。

当使用 RxJS 时，您应该总是从 Zone.js 导入补丁，这提供了更合理的行为。在订阅、操作或观察的构建时间，它将记住当前区域，并且将总是在记住的区域中运行，而不管您在哪里订阅。

您可以在导入 Zone.js(通常在`polyfill.ts`)后导入补丁:

```
import ‘zone.js/dist/zone-patch-rxjs’;
```

在[文档](https://github.com/angular/zone.js/blob/master/NON-STANDARD-APIS.md)中可以找到一个很好的例子。

# 7.如何从 Zone.js 中排除某些事件

如果出于某种原因，您不希望对某些事件类型(`Scroll-Events`，…)进行更改检测，您可以将这些事件类型全局列入黑名单。

但是，请记住，在这些情况下，您必须手动触发变更检测。如何做到这一点的例子可以在`[BlackListEvents](https://github.com/angular/zone.js/blob/master/STANDARD-APIS.md)`下的文档中找到。

# 8.用 Zone.js 怎么分析问题？

第一次调试 Zone.js 问题时，这可能是一项艰巨的任务。

区域不提供用于检查计划的/当前运行的任务的 API。此外，有时很难确定任务实际上来自哪里，因为在堆栈跟踪中，您只能看到与区域相关的条目。

幸运的是，Zone.js 提供了一些工具，可以帮助理解正在发生的事情。

Zone.js 包含一些区域规范，可以用来构建区域，从中我们可以派生出我们想要研究的区域。有两个`ZoneSpecs`非常有用:

*   `TaskTrackingZoneSpec` : 可以用来跟踪所有当前的任务，并提供一个 API 来检查它们。
*   `LongStackTrace` : 为每个任务记住所有前置任务的所有堆栈跟踪。所以，你基本上有一个大的堆栈跟踪，覆盖所有异步步骤，导致你想要检查的任务的创建。

这里有一小段关于如何使用这些技术来检查角度区域的内容:

```
// increase stack trace length (optional)
Error.stackTraceLimit = 100;
const longStackTraceZoneSpec = Zone.longStackTraceZoneSpec;// increase stack trace limit (optional)
longStackTraceZoneSpec.longStackTraceLimit = 1000;Zone.current
.fork(new Zone.TaskTrackingZoneSpec())
.fork(longStackTraceZoneSpec)
.run(() => {
   // NgZone forks currentZone, which is here longStackTraceZone
   const ngZone = new NgZone({enableLongStackTrace: true});
   platformBrowserDynamic()
      .bootstrapModule(AppModule, {ngZone: ngZone});
});
```

然后，您可以访问`macroTask`类型的任务，如下所示:

```
let macroTasks = Zone.current
 // get the TaskTrackingZone via it's name
 .get(‘TaskTrackingZone’)
 .getTasksFor(‘macroTask')
```

# 9.什么是微观和宏观任务？

调试区域时，您会看到有不同类型的任务:微任务和宏任务。一般来说，最好先从宏观任务开始看，因为大多数问题都与宏观任务有关。

但是这些类型之间的区别是什么>

简而言之，微任务在当前任务之后立即被调度(在当前 VM 回合中)，而宏任务仅在下一个 VM 回合中被调度。详细的解释可以在杰克·阿奇博尔德的这篇优秀文章中找到:

[](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/) [## 任务、微任务、队列和时间表

### 当我告诉我的同事 Matt Gaunt，我正在考虑写一篇关于微任务排队和执行的文章…

jakearchibald.com](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/) 

# 10.Zone.js 如何与 Ng-Upgrade 配合使用？

当您使用`ng-upgrade`从 Angular.js 应用程序迁移到混合应用程序时，这可能会导致难以调试的问题。

使用`ng-upgrade`时，您会注意到的第一件事是所有异步操作也在`NgZone`中执行。这很有意义，因为 Zone.js 会自动修补所有异步 API。

您可能会注意到，在某些情况下，性能越来越差。这可能是查看 Zone.js 和 Angular.js 的摘要周期之间发生了什么的一个指标。

每当所有的微任务在 NgZone 中完成，`ng-upgrade`触发`rootScope`上的`$digest`。

如果一个摘要触发了任何类似于`setTimeout`的异步代码，这将在`NgZone`中创建另一个任务。一旦完成，将会一次又一次地触发`$digest`。