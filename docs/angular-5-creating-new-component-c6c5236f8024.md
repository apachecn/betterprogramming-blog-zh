# 角度 5-创建新元件

> 原文：<https://betterprogramming.pub/angular-5-creating-new-component-c6c5236f8024>

## 要获取(使用 HttpClient)并显示数据列表的角度组件

![](img/6f43ff1c4881acdaa5bb0e26f01bc8fb.png)

丹尼尔·冯·阿彭的照片

TK 本文是系列的一部分— [用 angular 5](https://medium.com/all-is-web/make-a-crud-web-app-with-angular-5-caa83e06ab60) 制作一个 crud web app。

*   [角度 5 —引导](https://medium.com/all-is-web/angular-5-bootstrapping-using-angular-cli-918050c4dd7d)
*   [角形 5 —内部组件](https://medium.com/all-is-web/angular-5-inside-components-3001813034b9)
*   角度 5-创建新组件(您在这里)
*   [角度 5——布线](https://medium.com/all-is-web/https-medium-com-all-is-web-angular-5-routing-fad9b3648f67)

# angular-cli.json 的更改

在继续之前，我们先对`angular-cli.json`做一些改动。

1.  将`apps.styles`改为`styles.scss`。同时将`src`目录下的文件`styles.css`重命名为`styles.scss`。我们想使用 [Sass](https://sass-lang.com/) 预处理器来处理样式。
2.  将`defaults.styleExt`改为`"scss"`。
3.  移除`prefix`属性。

# 成分

我们要创建的第一个组件是`pokemon-list`组件。

这个组件只是从一个远程 URL(本例中为 [super-crud-api](https://github.com/SF-WDI-LABS/super-crud-api) )获取数据，并显示在一个列表中。

1.  使用`ng generate`创建一个新组件。

```
ng generate component pokemon-list
```

2.让我们看看我们的组件类`PokemonListComponent`。

这是它现在的样子:

除了我们在[上一篇文章](https://medium.com/all-is-web/angular-5-inside-components-3001813034b9)中讨论的基本组件特性，这个类还实现了来自`Core`库的`OnInit`接口。

每个组件都有一个创建、更新和销毁的生命周期。

当这些事件在组件的生命周期中发生时，Angular 向接口公开钩子。这些钩子可以使用 Angular 的核心库中相应的生命周期钩子接口来实现。

`ngOnInit`就是这样一个钩子，它在`OnInit`接口(如下图)中声明，最初被调用一次。

```
export interface OnInit { ngOnInit(): void;}
```

这相当于`this.$onInit`由`angular1.x`组件组成。

我们将在`OnInit`期间调用 API 来获取口袋妖怪数据。

当我们使用`ng generate`创建组件时，Angular CLI 也导入了我们的新组件，并将其添加到我们的`AppModule`的`declarations`数组中。

3.我们的组件现在需要进行 API 调用来从远程服务器获取数据，但是组件不应该这样做。

这就是`service` s 的作用；因此组件可以专注于显示数据并将数据传递给需要它的其他组件。

让我们创建一个`pokemon service`，它将为我们的`pokemon-list`组件获取数据。

4.创建新的口袋妖怪服务:

```
ng generate service pokemon --module=app
```

这将生成一个新的`PokemonService`类，

并将上述服务类添加到 app 模块`providers`。

由于我们需要稍后将该服务注入到我们的组件中，因此该服务需要被`provide` d 到依赖注入系统中。一种方法是将它添加到我们模块的`providers`数组中。

服务用一个`@Injectable`装饰器来装饰。我们稍后会研究这个问题。

5.Angular 使用`HttpClient`类与远程服务器通信。

为了在我们的服务中(以及应用程序中的任何地方)使用`HttpClient`，我们需要将`HttpClientModule` 导入到`AppModule`中。

6.现在我们可以将`HttpClient`注入到`pokemon service`构造函数中。

7.让我们在这个服务中定义`getPokemon`方法，它对 [super-crud api](https://github.com/SF-WDI-LABS/super-crud-api) 进行 HTTP 调用以获取 Pokemon 数据并返回一个泛型`Observable`。

*   与 angular 1.x `$http`服务返回一个承诺不同，`HttpClient`从`rxjs`返回一个`Observable`。
*   您可以`subscribe`到可观察对象以获得 HTTP 响应数据，这可以被认为是相当于承诺的`then`。当然，这两者的工作方式是不同的。

8.在`OnInit`期间，将`PokemonService`注入到`PokemonListComponent`构造函数中，并在注入的实例上调用`getPokemons`。

我们已经订阅了返回的可观察值，并将返回的数据赋给了一个私有声明的`pokemonData`变量。这可用于组件模板中的绑定。

9.更新组件模板`pokemon-list.component.html` 迭代返回的口袋妖怪列表，列表显示口袋妖怪名称。

在上面的模板中，我们使用了两个*指令* `*ngIf`和`*ngFor`。

*   `*ngIf`检查传递的变量中的值，如果发现为真，则从模板创建 DOM。相当于角度 1.x 中的`ng-if`
*   `*ngFor`是一个 repeater 指令，它遍历一个条目列表，并为每个条目提供要在 DOM 中显示的 HTML 内部块。相当于 Angular 1.x 中的`ng-repeat`指令。

10.让我们更新我们的根 shell 组件来显示`pokemon-list`组件。

11.在你的终端中运行`ng serve`，打开 [http://localhost:4200/](http://localhost:4200/) 。您应该会看到视图中显示的口袋妖怪名称列表。

我们已经创建了第一个组件！我们接下来要做的是，点击每个口袋妖怪的名字，并查看该口袋妖怪的详细信息。

让我们继续创建一个`pokemon-details`组件。

[*<上一张*](https://medium.com/all-is-web/angular-5-inside-components-3001813034b9) | [*下一张>*](https://medium.com/all-is-web/https-medium-com-all-is-web-angular-5-routing-fad9b3648f67)