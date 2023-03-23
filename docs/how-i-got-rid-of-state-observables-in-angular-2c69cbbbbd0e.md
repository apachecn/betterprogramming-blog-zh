# 我是如何在 Angular 中消除状态可观测量的

> 原文：<https://betterprogramming.pub/how-i-got-rid-of-state-observables-in-angular-2c69cbbbbd0e>

## 我不会回去的

![](img/6a6376d326227426e30cdc114f0b2a76.png)

Kha Ruxury 在 pexels.com 拍摄的照片

在 Angular 中开发复杂的 SPA 时，使用状态管理库会使事情变得容易得多。

app store 作为一个单一的事实来源，包含了对应用程序的正确运行至关重要的所有数据。多亏了这个商店，我可以从任何智能组件(或容器)中访问或编辑这些数据。

然而，处理商店的所有订阅可能会令人沮丧；很多时候，我不得不在一个组件中处理多个`@Select()` ，每个组件都返回其在 app store 中对应属性的一个可观察值。

对于这些可观察到的每一个，我需要创建一个订阅，该订阅将在组件生命周期结束时销毁…这需要很多代码！

但是，如果我们可以摆脱所有的可观测量呢？

在最简单的情况下，我们可以使用`async`管道避免显式订阅，它将订阅可观察对象并读取为我们发出的最后一个值。

假设我们有这样一个`@Select()` 装饰器 *:*

```
@Select(ListState.SelectAllItems) listItems: Observable<string[]>;
```

我们可以通过 HTML 中的`async`管道立即使用它:

```
<ul>
 <li *ngFor="let item of listItems | async">
   {{item}}
 </li>
</ul>
```

另一方面，许多情况要求我们订阅组件中的可观察值，以便我们可以在其他方法中使用它们发出的值。

(要了解如何设置店铺，在一个项目中看到以上例子，可以阅读我的文章 [*如何在 Angular*](https://medium.com/better-programming/how-to-create-a-simple-store-in-angular-577a8f21a3d6?source=friends_link&sk=1a40ae0d4e6f413cd84b1af52315a3b9) 中创建一个简单的店铺。)

# 介绍@ngxs-labs/select-snapshot

[@ ngxs-Labs/select-snapshot](https://github.com/ngxs-labs/select-snapshot)是 NGXS 实验室为 [NGXS](https://www.ngxs.io/) 开发的一个实验性特性。

尽管它仍然不是官方 NGXS 软件包的一部分，但它很可能会很快成为官方特性。这个包允许我们用`@SelectSnapshot()`替换`@Select()`装饰器。

但是它们之间有什么区别呢？

前者返回一个我们需要订阅的可观察对象，而后者为我们订阅存储并返回最后发出的值！要查看它的运行情况，让我们安装这个包:

```
npm install @ngxs-labs/select-snapshot
```

然后，让我们将它包含在我们的`appModule`中:

```
@NgModule({
declarations: [
AppComponent,
ListContainerComponent,
ListItemInputComponent,
ListComponent
],
imports: [
BrowserModule,
AppRoutingModule,
FormsModule,
ReactiveFormsModule,
NgxsModule.forRoot([ListState]),
NgxsReduxDevtoolsPluginModule.forRoot(),
NgxsSelectSnapshotModule.forRoot()
],
providers: [],
bootstrap: [AppComponent]
})
export class AppModule {}
```

现在我们可以替换`@Select()`装饰器了:

```
//@Select(ListState.SelectAllItems) listItems: Observable<string[]>;@SelectSnapshot(ListState.SelectAllItems) listItems: string[];
```

并且使用商店发出的值而不订阅！

```
<ul>
 <li *ngFor="let item of listItems">
   {{item}}
 </li>
</ul>
```

尽情享受吧！

[](https://medium.com/better-programming/how-to-create-a-simple-store-in-angular-577a8f21a3d6) [## 如何在 Angular 中创建一个简单的商店

### 用单一的事实来源增强你的应用

medium.com](https://medium.com/better-programming/how-to-create-a-simple-store-in-angular-577a8f21a3d6) [](https://medium.com/better-programming/a-new-way-of-dispatching-actions-with-ngxs-for-angular-7541f0adb812) [## 一种利用 Ngxs 为 Angular 调度动作的新方法

### 用这个新的库整理你的代码

medium.com](https://medium.com/better-programming/a-new-way-of-dispatching-actions-with-ngxs-for-angular-7541f0adb812)