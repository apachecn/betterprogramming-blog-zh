# 什么是内容有棱角的孩子？

> 原文：<https://betterprogramming.pub/understanding-contentchildren-with-an-example-e76ce78968db>

## 内容儿童举例说明

![](img/b88aa5c30ba34a9c8d208aacd7b298d6.png)

# **什么是内容儿童？**

`ContentChildren`是一个参数装饰器，用于从内容 DOM 中获取元素或指令的`QueryList`。每当添加或删除子元素/组件时，都会更新`QueryList`。

子元素引用设置在`ngAfterContentInit` 生命周期钩子方法之前的`QueryList`中。

下面，我们使用`ContentChildren`来获取包含子组件`ChildComp` **列表的`QueryList`。**列表存储在`Parent`组件的`contentChildren`变量中。

```
@[ContentChildren](https://angular.io/api/core/ContentChildren)(ChildComp) contentChildren : [QueryList](https://angular.io/api/core/QueryList)<ChildComp>;
```

让我们看一个利用`ContentChildren`能力的例子…

我们正在创建一个子组件`TabComponent` ，它有一个输入属性对象 tab 和一个打印 tab 对象的 title 属性的函数。模板只显示属性。

```
import { Component, Input } from "@angular/core"; @Component({**selector**: "app-tab",**template**: `<h4>{{ tab.title }}</h4> <p>{{ tab.content }}</p>`})export class **TabComponent** {@**Input**() tab: object; printTitle() {console.log(this.tab.title);
}
}
```

`TabListComponent` 是父组件，我们使用`ContentChildren`装饰器从内容 DOM 中访问子组件`TabComponent` 列表。

由于子元素引用是在`ngAfterContentInit`回调之前在`QueryList`中设置的，我们正在访问`ngAfterContentInit`生命周期钩子中的`QueryList`。

在模板中，我们使用了`ng-content`指令，它将`<app-tab-list><app-tab-list>` 标签之间的元素投影到组件的视图中。

在选择器元素之间添加的元素被称为内容投影元素，可以通过`ContentChildren`访问。

如您所见，我们能够从父组件访问子组件方法(如`ngAfterContentInit`方法所示)。

```
import {ContentChildren,QueryList,Component,AfterContentInit} from "@angular/core";import { TabComponent } from "./tab.component";@Component({**selector**: "app-tab-list",**template**: `<ng-content></ng-content>`})export class **TabListComponent** implements **AfterContentInit** {@**ContentChildren**(**TabComponent**) tabList: **QueryList**<**TabComponent**>;**ngAfterContentInit**() {this.tabList.toArray()[0].printTitle();}}
```

最后，这是顶视图组件，其模板中有上述两个选择器。

```
import { Component, Input } from "@angular/core"; @Component({import { Component, OnInit } from "@angular/core";@Component({**selector**: "app-root",**template**: `<app-tab-list><app-tab *ngFor="let tab of tabs" [tab]="tab"></app-tab></app-tab-list>`})export class **AppComponent** implements **OnInit** {tabs = [];**ngOnInit**() {this.tabs = [{ title: "First Tab title", content: "First Tab content" },{ title: "Second Tab title", content: "Second Tab content" },{ title: "Third Tab title", content: "Third Tab content" }];}}
```

注意:如果我们有一个子内容元素，那么我们可以使用`ContentChild`而不是`ContentChildren`。