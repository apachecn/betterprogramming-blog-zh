# 用 Angular 编写自定义结构指令

> 原文：<https://betterprogramming.pub/writing-custom-structural-directives-in-angular-f9bb5bb93f30>

## 让 DOM 结构依赖于用户特权

![](img/e1e4aff8ede550b4c67922ca72a524e6.png)

亚历山大·席默克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

通常，您需要根据用户的权限来决定 DOM 的哪一部分是可见的。这听起来很容易。但是，我们如何为我们的应用程序创建一个松散耦合的声明式解决方案呢？我将向您展示一种方法，我们可以如何受益于有角度的定制结构指令来实现这些目标。

# 第一种解决方案

您可能想到的第一个解决方案如下:

在你的组件中提供属性和函数，让`*ngIf`决定你的模板的哪些部分被呈现到 DOM 中，哪些部分没有。代码可能看起来像这样:

```
<div **ngIf*="user.hasOneOfPrivileges(['VIEW_OVERVIEW'])">
  user can see the overview
</div>
```

在您的组件中，您提供了用户对象:

```
constructor(public user: User) {}
```

起初看起来很容易，但正如我们所知，事情会变得复杂。

## 这个解决方案的问题是

*   我们代码的可重用性——因为我们需要在任何需要的组件中实现这种行为。
*   每个组件取决于用户。如果我们的代码变得越来越复杂，构造函数中的参数长度就会越来越长。同样，如果我们的组件是一个基类，并且我们继承了它，子组件也必须在其构造函数中注入用户。
*   如果条件变得复杂，我们可以在模板中使用函数。但是在组件中使用函数对我们应用的整体性能是不利的(因为 Angular 必须在每个变化检测周期评估一个函数，以决定是否有变化)。
*   测试我们代码的复杂性。我们需要一种简单的方法来改变用户的特权，以测试不同的情况。

如果你想知道为什么我使用字符串作为值(例如`VIEW_OVERVIEW`):这样，我可以在模板中直接使用字符串值。

因为我已经创建了一个特殊的类型`Privilege`，编译器和 IDE 可以检查这些值并支持我们完成代码。

```
export type Privilege = 'VIEW_OVERVIEW' |'VIEW_DETAILS' | 'DO_COOL_STUFF';
```

当然，枚举也是可能的。但是在这种情况下，我们必须在组件类中提供枚举来访问模板中的枚举值。

# 结构指导方法

我们真正需要的是一个与我们的组件类完全解耦的解决方案，只有在模板中，我们才希望声明性地确定应该呈现 DOM 的哪一部分。

Angular 有内置的结构指令([https://angular.io/guide/structural-directives](https://angular.io/guide/structural-directives))来实现这一点。我们都知道他们。他们是`*ngIf`、`*ngSwitch`、`*ngFor`等等。但是我们可以自己写。让我们先看看代码:

```
@Directive({
  selector: '[appHasPrivileges]',
})
export class HasPrivilegesDirective implements AfterViewChecked {
  constructor(
    private vcRef: ViewContainerRef,
    private tmpRef: TemplateRef<unknown>,
    private user: User
  ) {
  }

  @Input()
  appHasPrivileges: Privilege[] | undefined = [];

  ngAfterViewChecked() {
    this.vcRef.clear();
    if (this.user.hasOneOfPrivileges(this.appHasPrivileges ?? [])) {
      this.vcRef.createEmbeddedView(this.tmpRef);
    }
  }
}
```

在模板中，我们可以写:

```
<div **appHasPrivileges*="['VIEW_OVERVIEW']">user can see the overview</div>
```

在构造函数中，我们需要提供`ViewContainerRef` —例如，根据我们的条件，我们的 div(`TemplateRef`)将作为嵌入视图插入的 DOM 元素。`TemplateRef`是一种通用类型。

在我们的例子中，我们不知道上下文有什么类型——因为我们没有。因此我们提供`unknown`来清楚地表明我们不知道它。

在上面的例子中，你可能已经注意到我们使用了`AfterViewChecked`生命周期钩子来决定是否将模板呈现到 DOM 中。

如果我们的`user`对象可以在我们的应用程序的生命周期内改变它的状态，并且组件在这个改变期间被使用，那么这是必需的。如果`user`永远不会改变，我们可以将代码放入`OnInit`生命周期函数中。

## 多参数

现在我们的指令只能处理用户的特权。但是如果我们有更多的相关条件来决定哪个部分应该被渲染或者不被渲染呢？

我们可以在指令中添加更多的输入属性。假设我们想要一个超级功能(例如，用户是管理员，可以做任何事情)。我们可以将输入属性`orIsAdmin`添加到我们的指令中，但是这会将模板中的两个信息分开。一种更优雅的方式是将我们的条件作为属性`appHasPrivileges`的一部分。`*ngFor`指令以类似的方式工作。

如果我们的新输入属性名称以结构化指令的名称开始，这是可能的。在我们的例子中，名称是:

```
@Input()
appHasPrivilegesOrIsAdmin = false;
```

现在，我们可以将我们的附加条件包含到属性值中:

```
<div **appHasPrivileges*="['VIEW_DETAILS']; **orIsAdmin: true**">
  user can see the details or is admin
</div>
```

正如你所看到的，现在附加属性(`orIsAdmin`)及其值被添加在属性值内的分号之后。这真是简洁易读。我们可以扩展我们的指令，而不需要改变代码中已经使用它的每个地方。

顺便提一下:我们这里使用的语法是简写版本(带星号的那个),我们也可以使用带`ng-template`元素的常规版本:

```
<ng-template
  [appHasPrivileges]="['VIEW_DETAILS']"
  [appHasPrivilegesOrIsAdmin]="true"
>
  <div>user can see the details or is admin</div>
</ng-template>
```

两个版本是等效的。但是带星号的版本更短，在我看来可读性更强。

如果你想知道如何为我们的结构组件编写单元测试，看看 GitHub 项目:[https://github.com/mseemann/angular-rights-in-templates](https://github.com/mseemann/angular-rights-in-templates)。作为如何开始的提示:您需要一个使用结构化指令的测试组件。

# 改进的结构指导法

到目前为止，我们的代码的缺点是，它在每次视图检查时执行——例如，每当我们的应用程序发生变化，angular 运行一个变化检测周期，DOM 就会重新呈现。让我们看看是否可以避免这些执行，并且只在`user`改变时才重新呈现 DOM。

实现这个目标的一个解决方案是我们的`user`对象的反应状态，订阅用户的更改，如果用户已经更改，重新呈现 DOM。

对于这个例子，我使用了【https://ngrx.io/】()但是当然你可以使用任何其他的反应状态库。您可能已经在应用程序中使用了这样的库。

使用原理都是一样的。如果特权改变，我们需要确保`user`对象也改变，并在我们的结构指令中订阅改变:

如果您的组件被销毁，不要忘记取消订阅，以避免任何内存泄漏。我使用了`takeUntil`模式，但是您也可以存储订阅，并在`onDestroy`生命周期挂钩中调用`unsubscribe`。

# 结论和进一步改进

就是这样。现在，我们的结构组件是松散耦合的，可以声明性地使用，是独立可测试的，并且可以根据每个新需求进行扩展。

当然，我们可以进一步改进它:

*   我们可以将逻辑放在`user`类中。这将使测试更加容易。
*   如果您需要对输入变化做出反应，您可以使用 setters，将值放入`BehaviorSubject`并将`rxjs`选择与`combineLatest`组合。

如果你对带有完整功能代码的完整示例感兴趣，请查看 [GitHub 项目](https://github.com/mseemann/angular-rights-in-templates)。