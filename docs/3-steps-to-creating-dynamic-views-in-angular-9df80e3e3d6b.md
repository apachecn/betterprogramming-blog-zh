# 在 Angular 中创建动态视图的 3 个步骤

> 原文：<https://betterprogramming.pub/3-steps-to-creating-dynamic-views-in-angular-9df80e3e3d6b>

## 无指令动态加载组件

![](img/0ced5422e8e77e5af50ed39e80ba2ad3.png)

照片由[émile Perron](https://unsplash.com/@emilep?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 语境

很多时候，我们需要动态地呈现子视图。例如，我们可能有一个带有选项卡菜单的父视图，我们希望根据选择的选项卡来呈现视图。

作为 Angular 的新手，这是我经历过的事情，尽管遵循了官方的 [Angular](http://angular.io) 指南，我还是花了相当多的时间来实现工作。阅读 Maxim Koretskyi 关于 [DOM 操纵](https://hackernoon.com/exploring-angular-dom-abstractions-80b3ebcfc02)的文章无疑有助于我的理解，所以如果你想要更深入的解释，我也推荐你读一读。

在这篇文章中，我将强调并总结三个关键概念，让你快速运行。

# 1.创建视图

在插入视图之前，我们首先要知道如何创建一个视图。Angular 支持两种类型的视图，即*宿主视图*和*嵌入视图。*

## 主机视图

这是我们最常用的视图，也可能是我们最熟悉的视图。当 Angular 创建一个组件时，我们得到一个视图，其中也包含组件的数据。当我们谈论主机视图时，它们本质上是实例化的组件视图。

要创建一个组件，我们只需将它的选择器包含在另一个组件的模板中，Angular 会自动完成剩下的工作。然而，当我们动态地创建视图时，有两个必要的中间步骤。

我们需要一个`ComponentFactory`，它将用于手动(即动态)创建一个组件。为了得到一个`ComponentFactory`，我们需要通过将组件作为参数传入来使用`ComponentFactoryResolver`。

假设我们希望在初始化时创建一个动态组件:

```
<-- parentComponent.component.ts -->import { ..., ComponentFactoryResolver } from '@angular/core';
import { FirstComponent } from '<path>/firstcomponent.component.ts'...export class ParentComponent implements OnInit {constructor(private resolver: ComponentFactoryResolver) {}
}ngOnInit() {
    const componentFactory = this.resolver.resolveComponentFactory(
       FirstComponent);
    const dynamicallyCreatedComponent = componentFactory.create();
}
```

**重要:**你需要在`app.module.ts`文件内`NgModules`的`entryComponents` 中定义组件。在 Angular doc 的[动态组件加载](https://angular.io/guide/dynamic-component-loader)部分，这部分不包括在内，我花了一段时间才弄明白。

```
<-- app.module.ts -->
...providers: [],
  bootstrap: [AppComponent],
  entryComponents: [FirstComponent]
});
```

> “入口构件是任何按类型强制进行角度加载的构件(这意味着您没有在模板中引用它)。”— [Angular.io](https://angular.io/guide/entry-components)

## 嵌入式视图

```
<-- parentComponent.component.html -->
<div>This is rendered</div><ng-template #templateName>
    <div> This is not rendered</div>
<ng-template>
```

一个`TemplateRef`是创建 DOM 元素的蓝图，就像一个类对于它自己的一个实例一样。当你引用一个`ng-template`时，你得到一个`TemplateRef`。如果你在你的`component.html`文件中包含了一个`ng-template`，它将不会被渲染，因为你需要初始化视图并在之后嵌入它。在下一节中，我们将讨论如何引用 DOM 元素。

现在，我们只需要知道我们能够通过选择器名称`#templateName`选择`ng-template`，并且我们能够创建它的一个实例——这就是我们所说的*嵌入式视图。*

如果你有兴趣，你可以阅读更多关于[主机和嵌入式视图的区别](https://stackoverflow.com/questions/40423772/what-is-the-difference-between-a-view-a-host-view-and-an-embedded-view)。

# 2.选择 DOM 元素

如果您想要动态地呈现一个视图，您可以想象有一个元素作为锚点来告诉 Angular 视图应该插入的位置。如果您有使用 jQuery 的经验，它应该类似于:

```
<div id="selector></div> // HTML$('.selector').html('Dynamic Content Here') // jQuery selector
```

在 Angular 中，我们使用装饰器`@ViewChild`和`@ViewChildren`来搜索元素。它们都提供相似的功能，但是前者只返回一个引用。后者返回一个对元素有多个引用的`QueryList`对象。

## 句法

`@ViewChild(selector, [{read: any}], [{static: boolean}])`

这里再解释一下元数据属性:`read`和`static`都是可选参数，但是我会快速地讨论它们。

在 Angular 的文档中，它说`read`应该被设置为“true 以从被查询的元素中读取不同的标记”然而，更实际的用法是包含您感兴趣的元素的类。

在这篇[堆栈溢出](https://stackoverflow.com/questions/37450805/what-is-the-read-parameter-in-viewchild-for)文章中，它说可以有几个实例绑定到元素选择器。例如，对于每个元素，至少有一个`ElementRef`和一个`ViewContainerRef`。如果这两个类现在听起来很陌生，请不要担心，我稍后会谈到后者。

`Static`属性更简单。如果您希望查询结果在变更检测发生之前运行，请将其设置为`true`。

让我们看一个例子，看看我们如何访问元素的`ViewContainerRef`。

## 例子

```
<-- parentComponent.component.html --><div>
    <ng-container #viewContainer></ng-container>
</div> <-- parentComponent.component.ts -->export class ParentComponent implements OnInit {
  @ViewChild('viewContainer', read: ViewContainerRef) viewContainer: ViewContainerRef; ...
}
```

# 3.附着视图

既然我们能够选择一个锚元素，我们需要将视图附加到它上面。`ViewContainerRef`表示元素的容器，我们可以在它之后附加视图。

## 锚元素

在上面的例子中，我们使用`ng-container`作为锚元素。或者，我们也可以使用`ng-template`。我怀疑有些人会好奇我们是否可以使用其他元素，例如`div`。理论上，我们可以使用任何我们想要的元素。

然而，需要注意的是 Angular 并没有在元素的中插入视图*，而是在绑定到`ViewContainer`的元素的*之后插入*。*

`ng-container`和`ng-template`实际上并不在 DOM 中呈现，而是作为 Angular 知道要查找的注释。因此，使用这些元素允许我们避免在 DOM 中插入过多的元素，比如不需要的`div`。

## 插入视图

在上面的代码块中，我们已经有了一个名为`viewContainer`的实例属性，我们只需注入视图。

```
<-- parentComponent.component.ts -->import { ..., ComponentFactoryResolver } from '@angular/core';
import { FirstComponent } from '.../firstcomponent.component.ts';
import { SecondComponent } from '.../secondcomponent.component.ts';...export class ParentComponent implements OnInit {
  @ViewChild('viewContainer', read: ViewContainerRef) viewContainer: ViewContainerRef;constructor(private resolver: ComponentFactoryResolver) {}
}...onTabChange(selectedTab) {
  this.viewContainer.clear(); // clear all views
  if (selectedTab === 'firstComponent') {
    const componentFactory = this.resolver.resolveComponentFactory(
      FirstComponent
    );
    this.viewContainer.createComponent(componentFactory);
  } else if (selectedTab === 'secondComponent') {
      // similar idea here
  }
};
```

之前，我们使用了来自`ComponentFactory`对象的`.create()`方法，但是`ViewContainerRef`有一个方法接受一个`ComponentFactory`，通过内部调用它的`create` 函数创建主机视图，并自动插入视图。

## **向动态组件传递数据**

现在，我们能够动态创建主机视图。能够做的一件有用的事情是将数据传递给我们的组件。这样，我们就能够用动态数据动态地创建主机视图。

让我们以上面的例子为基础。

```
<-- parentComponent.component.ts -->const componentRef =
    this.viewContainer.createComponent(componentFactory);
(componentRef.instance).dynamicData = { a: 5, b: 3}; // passing data<-- firstComponent.component.html -->
<div> Dynamic Content: {{ dynamicData.a }} </div>
```

`createComponent`方法返回一个`ComponentRef`对象，我们可以用它来访问实例属性并添加一个`dynamicData`键。这样，在我们的子组件中，我们可以通过插值简单地引用数据变量名。

## **创建嵌入式视图**

现在我们知道了如何创建主体视图，嵌入视图在概念上几乎是相似的。还记得我们之前谈过的`TemplateRef`吗？我们可以用它创建一个嵌入式视图。

```
<-- parentComponent.component.html --><div>
    <ng-container #viewContainer></ng-container>
</div><ng-template #isLoggedInTemplate>
    <div> You are logged in! </div>
<ng-template><-- parentComponent.component.ts -->export class ParentComponent implements OnInit {
  @ViewChild('viewContainer', read: ViewContainerRef) viewContainer: ViewContainerRef;
  @ViewChild('isLoggedInTemplate', read: TemplateRef) template:
TemplateRef<any>;...displayLoginMessage() {
  this.viewContainer.createEmbeddedView(this.template);
};
```

我们简单地添加另一个`TemplateRef`实例属性，并使用`ViewContainer`的方法实例化视图并插入它。

# 最后的话

通过理解这三个概念，你应该能够很容易地在 Angular 中创建动态视图。

在官方指南中，在指令的帮助下，[动态组件加载](https://angular.io/guide/dynamic-component-loader)的实现略有不同。我决定多谈一谈如何不使用它，因为它似乎可以省去创建指令的麻烦。尽管如此，最好也看一下官方文件，以了解潜在的机制。