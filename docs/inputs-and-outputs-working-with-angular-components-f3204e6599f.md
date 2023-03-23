# 输入和输出:使用角度组件

> 原文：<https://betterprogramming.pub/inputs-and-outputs-working-with-angular-components-f3204e6599f>

## 学习使用输入属性和输出事件来管理父组件和子组件

![](img/d2dc54e9aabfbbdaf69702f9a4da26c8.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[Magnus eng](https://unsplash.com/@magnusengo?utm_source=medium&utm_medium=referral)拍摄的照片

在我最近发表的一篇[教程](https://medium.com/better-programming/learn-angular-basics-by-building-a-simple-app-using-angular-material-9bbc19aa33cf)中，我们走过了构建一个应用程序并让它运行的过程。这个应用程序运行良好，但是它的结构还有待改进。现在，视图和逻辑的所有代码都存在于一个组件中，这意味着随着应用程序的增长，事情会变得越来越复杂，越来越难以理解。

这个应用程序需要改头换面。

> "随着程序的发展，它的复杂性会增加，除非做些工作来维护或减少它."——曼尼·雷曼

# 我们今天学了什么？

*   本教程强调了保持 Angular 应用程序整洁和结构化的重要性，这样你就不会在一个文件中有一堆代码。
*   我将展示一个我们最近构建的应用程序的例子，它可以使用更多的结构，并提供分解成组件的代码供我们使用。
*   我们将学习如何使用输出事件让父组件知道数据的变化。
*   我们将使用输入属性将数据从父组件传递到子组件。

如果你[按照我们之前离开时的样子看应用](https://stackblitz.com/edit/habit-tracker-basic?file=src%2Fapp%2Fapp.component.html)，一切都存在于应用组件中。随着时间的推移，当我们添加新的功能并更改这个应用程序时，这一大堆代码将变得令人遗憾地难以处理，因此重要的是要确保我们将代码封装到组件中，这样事情才不会失控。

## 为什么组件是必由之路？

*   组件是一段自我关注的代码。它只包含它需要的东西，可以作为一个独立的视图提供。
*   将代码放入一个组件中使其可重用，所以如果你需要在代码中的多个地方使用给定的表单或 UI 元素，你可以通过将它放入一个组件中来大大减少重复。
*   组件使得你的代码**更易测试**。您可以更容易地对相关的逻辑进行分组，并编写更集中的测试，这也使得当您稍后回来进行更改或添加特性时，事情变得更容易推理。

如果你看一下`[app.component.html](https://stackblitz.com/edit/habit-tracker-basic?file=src%2Fapp%2Fapp.component.html)`中的所有 HTML，我们已经有了一些可以成为组件的非常清晰的部分:

## 在我们的启动文件中，我已经将这个应用程序重组为四个组件:

*   `app`组件——这是父组件。
*   `toolbar`组件——它保存导航的代码，在本教程的剩余部分是静态的。
*   `all-habits`组件——这个子组件包含管理已创建习惯列表的所有代码。
*   `habit-form`组件——这是一个子组件，封装了我们将用来为应用程序添加和编辑习惯的表单。
*   此外，我们已经将习惯数据移动到导出的 const 文件中，这样就可以共享了。

# 设置

## 开发环境

*   如果你在你的机器上使用过 Angular 应用程序，确保你已经安装了 [Node.js](https://nodejs.org/en/) 和 [Angular CLI](https://angular.io/guide/setup-local) 。
*   我将展示如何使用 [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) 下载启动文件，如果您以前从未使用过，这也需要安装。

## 起始文件

要获得启动项目，从这个 GitHub [repo](https://github.com/jessipearcy/habit-tracker-basic) 中把它拉下来。要从命令行执行此操作，请导航到您想要下载应用程序的位置，并键入以下 Git 命令:

```
git clone [https://github.com/jessipearcy/habit-tracker-components-split](https://github.com/jessipearcy/habit-tracker-components-split)
```

这将把文件复制到文件夹中。接下来，运行以下两个命令导航到该文件夹，并安装 Angular 应用程序运行所需的软件包:

```
cd habit-tracker-components-split
npm ci
```

在所有的包安装完成后，在命令行中输入`ng serve`，按 enter，然后导航到`[http://localhost:420](http://localhost:4202)0`，你应该会看到正在运行的应用程序。

## 让我们检查一下我们从什么开始

*   app 组件模板包含三个子组件。
*   我们习惯列表的数据源位于`habits.ts`中导出的 const 中——这允许习惯数组在组件间共享和操作。
*   现在，应用程序组件正在呈现其中列出的所有组件——我们将添加一些属性和结构指令，以根据用户操作决定视图中应该显示的内容。

好吧，让我们开始吧！

# 使用输出事件和输入属性

## 实现结构化指令来管理视图

目前，我们同时显示表单和习惯列表，这在视图中占据了太多的空间——我们希望只在需要时才看到编辑表单！让我们以此为契机，学习如何结合使用`*ngIf`和`<ng-template>`来显示或隐藏代码。

在`app.component.ts`中添加一个标志属性，如果`true`则显示表单，如果`false`则显示列表。默认情况下，我们将其设置为`false`:

在`app.component.html`中，我们将使用角度结构指令设置一个 if/else:

```
<app-habit-form ***ngIf**="formOpen; **else** allHabits"></app-habit-form><ng-template **#allHabits**>
    <app-all-habits></app-all-habits>
</ng-template>
```

`*ngIf`表达式中的`else`指的是我们在`<ng-template>`上设置的局部参考。使用`<ng-template>`意味着`all-habits`组件不仅不在视图中显示，而且实际上根本不在 DOM 中呈现。

## 向`all-habits`组件添加一个输出事件

习惯列表和我们的习惯表单现在都是 app 组件的子组件。输出事件允许子组件让它们的父组件知道数据的变化。我们将向`all-habits`组件添加一个输出事件，然后监听父`app`组件中的事件。

在`all-habits.component.ts`中，使用`Output()`装饰器添加一个属性，并将其设置为新的`EventEmitter`。**确保从** `**@angular/core**`导入 `**EventEmitter**` **！然后我们将在一个函数中发出事件。**

现在我们将更新 HTML 模板，以便在点击添加新习惯按钮时调用`onAdd()`函数:

现在我们正在发出一个事件，我们的父组件需要监听它。

在`app.component.html`中，放置一些事件监听器语法来监听事件，并调用一个新方法。

```
<app-habit-form *ngIf="formOpen"></app-habit-form><app-all-habits *ngIf="!formOpen"
    (addEvent)="onAdding()"></app-all-habits>
```

然后在`app.component.ts`中添加方法来接收事件并采取一些行动:

现在我们应该可以点击显示我们的表单了。

## 从表单中输出事件

该表单还需要能够让父组件知道我们何时想要取消表单并返回列表。

让我们给`habit-form.component.ts`添加一个新的输出事件属性，并更新`exitform()`方法以在被调用时发出事件:

我们仍然需要重置表单，这样当我们在列表和表单之间来回移动时就有了新的数据。

我们已经在`habit-form`的模板中调用了`exitForm()`，所以我们现在需要做的就是开始监听`app`父组件中的这个事件:

```
<app-habit-form *ngIf="formOpen; **else** allHabits"
    (onExit)="**closeForm()**"></app-habit-form>
```

将`closeForm()`方法添加到`app.component.ts`:

现在，我们可以打开和关闭习惯表单。

## 用数据发出输出事件

我们从 starter 项目继承了添加新习惯的能力，所以如果您测试添加一个新习惯，它应该工作得很好。然而，我们需要有一种方法来告诉我们的表单我们处于编辑模式，并给它更新现有项目所需的所有信息。

这提出了一个挑战，因为我们想要给表单的习惯数据将来自`all-habits`，而我们想要输入来自`app`的数据。我们需要采取一些步骤来实现这一点:

*   从`all-habits`发出一个包含习惯数据的事件。
*   将习惯存储在`app`中。
*   在模板中的`habit-form`元素上输入习惯。
*   从输入习惯中填充数据。
*   更新列表中已有的习惯。

让我们从当用户点击编辑按钮时发出一个事件开始:

`all-habits.component.ts`

`all-habits.component.html`

关于这些变化的一些说明:

*   在这个输出事件中，我们添加了一个类型。这是因为当我们发出这个事件时，我们想要传递我们的习惯对象，它将是类型`Habit`。
*   还要注意，我们的`onEdit()`函数接受一个参数，我们可以通过在 HTML 中传递来自`*ngFor`的习惯来访问我们需要的特定习惯。

现在让我们监听`app`中的事件并存储它:

`app.component.html`

```
<ng-template #allHabits>
    <app-all-habits
        (addEvent)="onAdding()"
        (editEvent)="onEditing($event)">
    </app-all-habits>
</ng-template>
```

`app.component.ts`

此代码片段中有趣的变化:

*   别忘了导入`Habit`型号。
*   我们已经更新了`closeForm()`,以便在表单关闭时清空`editHabit`属性。这可以防止下次打开表单时不小心将不需要的数据传递到表单中。

## 将输入添加到元素中

现在，如果您保存了所有内容并进行尝试，我们的 click 事件将被触发，我们将在应用程序组件中获取数据——但是，当表单打开时，它仍然是空白的！这就是输入装饰器的用武之地。

首先，我们将输入添加到`app.component.html`中的`habit-form`元素:

```
<app-habit-form 
    *ngIf="formOpen; else allHabits"
    (onExit)="closeForm()"
    [habit]="editHabit">
</app-habit-form>
```

左侧绑定括号内的变量引用了我们将要创建的子组件的`habit`属性，我们将它设置为当前作用域内的 app 组件的`editHabit`属性的值。

## 将输入属性添加到表单中

为了获取和使用被输入的属性，我们将更新`habit-form`组件来使用它:

注意事项:

*   当点击添加新习惯按钮时，输入属性将为空或未定义，这就是为什么我们可以方便地检查它的存在，以了解我们是否处于编辑模式。
*   注意，我们正在使用`.indexOf()`检查我们的习惯对象的索引，并传入整个对象。点击阅读更多关于[在 JavaScript 中使用数组和对象的信息。](https://www.freecodecamp.org/news/javascript-array-of-objects-tutorial-how-to-create-update-and-loop-through-objects-using-js-array-methods/)
*   为了更新我们现有的习惯，我们使用`.splice()`从数组中删除条目，并用表单中提交的值替换它。
*   如果没有习惯传入，我们知道我们处于添加模式，所以我们可以简单地将新习惯推送到数组中。

好吧！现在，您可以通过传递整个角度组件系列的数据来创建、更新、编辑和删除习惯。干得好！

# 包裹

非常感谢您完成本教程。让我们回顾一下今天所学的内容:

*   为什么我们应该将代码分割成组件，以保持应用程序的整洁和可维护性
*   如何使用输出事件属性将警报和数据从子组件传递到父组件
*   如何使用输入属性将数据从父组件向下传递到子组件

## 多练习一下

更多的实践，尝试分裂出这个应用程序中的另一个组件。`all-habits`组件包括一个习惯列表，可以很好地放入它自己的组件。尝试将这些代码放入一个组件中，并弄清楚如何确保列表随着编辑和添加而保持最新。

## 资源

*   [最终代码演示](https://stackblitz.com/edit/angular-ivy-xd4hjl?file=src%2Fapp%2Fapp.component.html)
*   [了解关于组件交互性的更多信息](https://angular.io/guide/component-interaction)
*   [了解更多关于角度结构指令的信息](https://angular.io/guide/structural-directives)