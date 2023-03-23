# 一种利用 Ngxs 为 Angular 调度动作的新方法

> 原文：<https://betterprogramming.pub/a-new-way-of-dispatching-actions-with-ngxs-for-angular-7541f0adb812>

用这个新的库整理你的代码

![](img/154fbdfff9ef37f9aef9eaf8940efc5d.png)

照片由 [Pexels](https://www.pexels.com/photo/brown-and-black-wooden-wall-decor-1816230/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的[扎卡里·德波蒂斯](https://www.pexels.com/@zachtheshooter?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄

开发中的另一个基本优先事项是尽量减少样板文件的使用。一般来说，保持代码尽可能简单是一个好的做法，避免冗长以控制技术债务。

对我来说，没有什么比一个有数百行代码的文件更令人沮丧的了，这就是为什么我总是在寻找以一种更干净和更清晰的方式编写的方法。在这篇文章中，我将向您展示一种更好的方法来将动作分派给 Angular NgXs `Store`。

# 问题是

为我们的状态管理编写样板代码可能是一个漫长的过程。如果我们正在开发一个复杂的应用程序，其中有大量的操作由`Store`处理，我们可能需要几十个不同的`@Select()` 函数(以及`Action` 声明)。

当涉及到从我们的组件分派动作时，我们可以很容易地找到下面这样的情况，我们将多个更新分派给`Store`，以在路由到`success`页面之前执行一系列不同的动作:

# 解决方案:引入*分派*装饰器

[@ ngxs-Labs/dispatch-decorator](https://www.npmjs.com/package/@ngxs-labs/dispatch-decorator)是 NGXS 实验室为 [NGXS](https://www.ngxs.io/) 开发的另一个有趣的实验特性。

尽管它仍然不是官方 NGXS 软件包的一部分，但它很可能会很快成为官方特性。这个包允许我们使用@Dispatch 装饰器，而不用在组件的构造函数中注入`Store`。此外，它帮助我们编写更加紧凑的代码。

我们可以使用脚本来安装它:

```
npm i @ngxs-labs/dispatch-decorator
```

并且我们可以把它包含在我们的`app.module.ts` *:* 中

```
**import** { NgModule } **from** '@angular/core';
**import** { NgxsModule } **from** '@ngxs/store';
**import** { NgxsDispatchPluginModule } **from** '@ngxs-labs/dispatch-decorator';@NgModule({
imports: [
NgxsModule.forRoot(CartState, ProductState),
NgxsDispatchPluginModule.forRoot()
]
})**export** class AppModule {}
```

现在，我们准备用@Dispatch 装饰器替换对`Store`的调用:

好多了。尽情享受吧！

# 进一步阅读

[](https://medium.com/better-programming/how-i-got-rid-of-state-observables-in-angular-2c69cbbbbd0e) [## 我是如何在 Angular 中消除状态可观测量的

### 我不会回去的

medium.com](https://medium.com/better-programming/how-i-got-rid-of-state-observables-in-angular-2c69cbbbbd0e) [](https://medium.com/better-programming/how-to-create-a-simple-store-in-angular-577a8f21a3d6) [## 如何在 Angular 中创建一个简单的商店

### 用单一的事实来源增强你的应用

medium.com](https://medium.com/better-programming/how-to-create-a-simple-store-in-angular-577a8f21a3d6)