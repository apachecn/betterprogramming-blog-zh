# 角度为 9+的路径 101

> 原文：<https://betterprogramming.pub/routing-101-in-angular-9-c76144b28cbd>

## 关于使用角度布线显示视图和在零部件之间传递数据的快速教程

![](img/9458135a4e17c883a4e969f9041c96ea.png)

安妮·斯普拉特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 我们在学什么？

*   使用路线显示和隐藏组件，而不是输入和输出属性
*   通过在路由中传递数据来保持这些组件之间的通信
*   了解如何在路由模块中设置路由
*   使用`routerLink`设置点击事件并将数据传递给路线

在我最近发表的另一篇教程中，我介绍了如何使用输入和输出属性在 Angular 应用程序中传递数据。我们还使用这些属性来管理用户在 UI 中可以看到的内容。这种设置确实有效，是获取子组件所需数据的好方法，但是看起来不太清晰。我们的输入分散在各处，有点重复。

例如，这是那个项目中的`app.component.html` [:](https://gist.github.com/jessipearcy/d3c9e82053ebe05f298f1b3af93d55fb)

在这里，我们有许多由应用程序组件管理的逻辑，它承担着引导流量的工作。我们还有一个问题，需要将数据从`all-habits`传递到应用程序组件，这样我们就可以将数据传递回`habit-form`组件。应用程序组件根本不需要关心这些数据！

# SPAs 中的路由与 HTML 中的路由有何不同？

在 SPA 中，您可以通过一个页面提供所有内容和数据。该应用程序只是根据应用程序的状态显示不同的 HTML、CSS 和 JS。数据可以在运行的应用程序和服务器之间来回发送，而无需重新加载页面，因此在进行简单更新时无需等待资产回来。事实上，这就是你的应用程序中所有代码的入口点的样子，它出现在`index.html`:

```
<body>
    <app-root></app-root>
</body>
```

在 Angular 中，routes 不是告诉服务器发送回什么文件，而是根据用户与应用程序的交互方式告诉浏览器呈现什么。

# 路由什么时候方便？

*   **当你想要新的一页的体验。你可以透过经典的、非 SPA 的页面来思考这个问题。一条新路线应该会引导你进入应用程序中的一个全新视图。如果一个新功能的目标意味着隐藏应用程序的其余部分，并显示一个新的、独立的体验，那么考虑将路由作为呈现新内容的手段。**
*   **当您需要为列表中的项目呈现内容时。**通过在路线中传递 ID 或索引，路线参数可以轻松打开细节或编辑页面等功能
*   **需要管理角色权限时。** [这个主题](https://angular.io/guide/router#preventing-unauthorized-access)超出了本教程的范围，但是当您想要确保满足某些给定标准的用户*不能*访问由路由呈现的内容时，通常使用有角度的路由防护。新路由可以封装包含敏感信息的页面，并重定向未经授权的用户。

# 设置

## 开发环境

*   如果你已经在你的机器上使用了 Angular 应用程序，确保你已经安装了 [Node.js](https://nodejs.org/en/) 和 [Angular CLI](https://angular.io/guide/setup-local) 。
*   我将展示如何使用 [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) 下载启动文件，如果您以前从未使用过，也需要安装。

## 起始文件

要得到启动项目，从这个 GitHub [repo](https://github.com/jessipearcy/habit-tracker-basic) 中把它拉下来。要从命令行执行此操作，请导航到您想要下载应用程序的位置，并键入以下 Git 命令:

```
git clone [https://github.com/jessipearcy/basic-routing-start.git](https://github.com/jessipearcy/basic-routing-start.git)
```

这将把文件复制到文件夹中。接下来，运行以下两个命令导航到该文件夹，并安装 Angular 应用程序运行所需的软件包:

```
cd basic-routing-start
npm ci
```

在所有的软件包安装完成后，在命令行中键入`ng serve`，按 enter，然后导航到`[http://localhost:420](http://localhost:4202/)0`，你应该会看到正在运行的应用程序。

# 角度布线入门

我们的应用程序现在正在构建，但并不十分有趣。我们还没有设置任何路线！

打开`app-routing.module.ts`。您会注意到，默认情况下，Angular 将`routes`属性设置为一个空数组:

```
const routes: Routes = [];
```

让我们创建一条路线，这样当应用程序加载时，我们的主页将显示我们的习惯列表:

```
const routes: Routes = [
  { path: '', component: AllHabitsComponent }
];
```

这个对象告诉应用程序，当呈现我们的基本 URL(对于这个测试应用程序，它将是`[http://localhost:4200/](http://localhost:4200/)`)时，显示`AllHabitsComponent`。不要忘记为组件添加导入。

接下来，我们需要告诉 Angular 将路由中的内容放在哪里。在`app.component.html`中，在工具栏后添加以下元素:

```
<router-outlet></router-outlet>
```

完美！我们现在有一些内容可以处理。

# 传送到表单

为了连接我们的页面以便添加新习惯，我们需要了解 Angular `routerLink`属性。将这个属性添加到一个元素中会使它的行为几乎像一个`<a>`标签，并且会将你引导到指定的位置。

首先，让我们在按钮上添加一条新路线。转到`app-routing.module.ts`并将另一个路径对象添加到`routes`数组:

```
const routes: Routes = [
    { path: 'habit-form', component: HabitFormComponent },  
    { path: '', component: AllHabitsComponent },
];
```

接下来，在添加新习惯按钮上，添加一个`routerLink` 将我们发送到新路线:

保存所有这些并尝试单击您的按钮—我们现在可以进入我们的表单了！如果您在浏览器中查看 URL，您应该会看到我们到达那里的路线:

```
[http://localhost:4200/habit-form](http://localhost:4201/habit-form)
```

# 从组件导航

通常，一些逻辑将与视图间的导航相关联。在这些情况下，您可以从组件中的一个函数导航，而不是使用`routerLink`。让我们演示如何使用 Angular 的内置`Router`类。

更新`habit-form.component.ts`:

*   从`@angular/router`导入`Router`。
*   在类的构造函数中注入一个路由器属性。
*   使用路由器的`navigate()`功能路由您的应用。

现在，与我们的习惯表单相关联的 click 事件将在习惯数组中创建新习惯，并将用户重定向回主列表——注意，新习惯已经被动态添加，并且在初始化`all-habits`组件后立即显示。

# 设置路线参数

要编辑列表中的一个项目，我们可以将该项目的索引传递给 route，然后在表单中拾取它，以检测是否有正在编辑的内容。这需要增加一条新的路线。在`app-routing.module.ts`中，添加以下路线，其中包含一个名为`id`的参数:

```
const routes: Routes = [
    { path: 'habit-form/:id', component: HabitFormComponent },
    { path: 'habit-form', component: HabitFormComponent },
    { path: '', component: AllHabitsComponent },
];
```

**注意:**顺序很重要！更具体的路由需要列在不太具体的路由之上。如果带有参数的路线列在第二位，应用程序将首先路由到习惯表单，并且永远不会获取 id。

接下来我们将在`all-habits.component.html`中添加另一个`routerLink`属性，这一次我们将把被点击的`habit`的索引值传递给 route:

```
<mat-icon 
    class="habit-icon" 
    color="primary" 
    [routerLink]="['/habit-form', i]">edit
</mat-icon>
```

**关于这个的一些事情:**

*   在本例中，我们使用了方括号，这样我们在双引号内传递的文本将被视为一个 JavaScript 表达式。方括号对任何角形绑定都是这样，如果没有方括号，双引号之间的内容将被视为文字字符串。
*   我们传入的`i`变量将填充我们路线的`:id`参数。它对应于我们将要编辑的`habit`的索引，并允许我们从表单中查找。

在这一点上，如果您点击编辑图标，您将看到路径填充了被点击习惯的索引。但是我们还没有用编辑习惯填充表单——让我们解决这个问题吧！

# 访问路线参数

在`habit-form.component.ts`中，我们将使用 Angular 的`ActivatedRoute`类来访问路由中发送的 id。进行以下更新:

**此代码片段中需要注意的更改:**

*   我们使用`ActivatedRoute`属性`route`从 URL 获取参数。我们传递给`get()`的字符串对应于我们在`app-routing.module.ts`中定义的参数名称。
*   那个`get()`方法返回一个字符串，所以我们在第 21 行使用`+`操作符将返回的字符串转换成一个数字，我们可以在`editingIndex`中存储和使用这个数字。
*   我们使用`id`参数的存在来告诉我们是否处于编辑模式。
*   当您退出表单时，不要忘记将`editing`设置回`false`，否则您可能会得到一些意想不到的行为！

最后，最后一步是确保我们的 Cancel 按钮正在路由我们，而不是试图提交一个空的`habit`。在`habit-form.component.html`中，更新取消按钮以包含一个空的`routerLink`:

```
<button mat-raised-button routerLink="">Cancel</button>
```

这将引导我们回到主列表。

# 结论

干得好！我们现在可以使用角度路由来创建、更新、读取和删除习惯。本教程向我们介绍了:

*   在`app-routing.module.ts`中设置基本路线
*   通过 HTML 中的一个`routerLink`属性在路由中传递数据
*   使用角度的`Router`和`navigate()`从控制器中的功能导航到不同的路线
*   使用`ActivatedRoute`和`paramMap`访问路线参数数据

谢谢你陪我走过这一切——我希望你学到了一些新东西！

# 资源

*   [Stackblitz 演示](https://stackblitz.com/edit/angular-ivy-gtddhr?file=src/styles.scss)
*   [最终代码回购](https://github.com/jessipearcy/basic-routing)
*   [角度文件布线教程](https://angular.io/tutorial/toh-pt5)

## 更多角度教程

[](https://medium.com/better-programming/inputs-and-outputs-working-with-angular-components-f3204e6599f) [## 输入和输出:使用角度组件

### 学习使用输入属性和输出事件来管理父组件和子组件

medium.com](https://medium.com/better-programming/inputs-and-outputs-working-with-angular-components-f3204e6599f) [](https://medium.com/better-programming/learn-angular-basics-by-building-a-simple-app-using-angular-material-9bbc19aa33cf) [## 通过使用角度材料构建一个简单的应用程序来学习角度基础知识

### 奖励:包括使用角度反应形态的一瞥

medium.com](https://medium.com/better-programming/learn-angular-basics-by-building-a-simple-app-using-angular-material-9bbc19aa33cf)