# 单元素集合在角度上的力量

> 原文：<https://betterprogramming.pub/single-element-collections-in-angular-8b1c263e3d18>

## **避免创建复杂的 setters 迷宫**

![](img/a4c7d9e3f4b90bf80ed0b00f352245a4.png)

埃斯特万·洛佩兹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

今天，我想分享我对在 Angular 应用程序中使用单元素集合的力量的想法。我在日常工作中经常发现这方面的用例，所以也许你也会发现它很有用。

让我们从一些典型的事情开始:

```
<workitem-card 
  *ngFor="let item of selectedWorkItems"
  [item]="item" 
> 
```

这样的代码我们已经见过很多次了，在我们看来非常自然。每当我们向`selectedWorkItems`数组添加一个新元素时，Angular 将初始化组件的一个新实例，并在视图中呈现它。一切照旧。

现在考虑这个:`selectedWorkItems`集合可以有`0`或`1`元素。

在这种情况下，是否还有一个`*ngFor`的位置，或者我们应该在组件上切换到一个单独的`selectedWorkItem`字段？

# *ngFor 可用于渲染单个元素

让我们想象一下组件模板中的以下结构:

在组件中:

```
selectedItem: WorkItem = null;
selectItem(item: WorkItem) { this.selectedItem = item };
```

又一次，看起来很典型。我们有一个集合(`workItems`)，我们允许用户通过点击它来选择一个元素。一旦我们选择了它，我们通过`*ngIf`显示`workitem-card`组件。

这是一种经典的方法，但是如果使用不当，可能无法达到预期的效果…

# *只要条件为真，ngIf 就保持相同的组件实例

想想当用户开始点击项目并改变所选项目时会发生什么。

条件将保持真实，因为`selectedItem`字段永远不会是`null`，因此`*ngIf`将保持`workitem-card`组件可见。只有它的输入`[item]`会改变它的值。

`workitem-card`组件设置逻辑将不会重新运行。其`constructor`和`ngOnInit`将不会根据新的输入值运行。第一次点击后会初始化，仅此而已。您可能知道，init 逻辑通常高度依赖于组件的键输入。不重新运行它，我们只是冒着组件行为不正常的风险。

当然，您可以重构`workitem-card`组件。添加一个 setter，给它的代码增加额外的复杂性，然后像那样处理问题。但是有多个输入设置器的组件，它们的初始化逻辑分布在设置器之间，总是很难维护和扩展。所以对我来说，触摸`workitem-card`显然是错误的方式。

您现在真正想要的唯一一件事是，每当您选择另一个项目时，生成一个`workitem-card`组件的新实例。

# 单项集合可用于实现项目选择机制

如开头所述，每当您向集合中添加一条新记录时，`*ngFor`都会为您提供一个新的组件实例。那么为什么现在不使用它呢？

我们可以按以下方式重写代码:

在组件中:

```
selectedItems: WorkItem[] = [];
selectItem(item: WorkItem) { this.selectedItems = [item] };
```

一切都像以前一样工作——但更好。每当你选择一个新的项目，你就会得到一个`workitem-card`组件的新实例！

你把`*ngIf`换成了`*ngFor`，现在一切都更简单了。您不必接触`workitem-card`组件实现。您挑选工作项并为其获取组件。就这么简单。正如我们所希望的，每次都有新的例子。

# 结论

通过将选中的项保存在数组中而不是单个字段中，可以避免创建复杂的 setters 迷宫，并且只在`ngOnInit`方法中保存子组件的 init 逻辑。

事实上，在大多数情况下，您不会丢失任何东西，总体性能影响也很小。

编码快乐！