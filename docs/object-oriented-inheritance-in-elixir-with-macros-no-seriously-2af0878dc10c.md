# 用宏实现 Elixir 中的面向对象继承？不，说真的

> 原文：<https://betterprogramming.pub/object-oriented-inheritance-in-elixir-with-macros-no-seriously-2af0878dc10c>

## Elixir 的宏系统让开发者可以做一些不可思议的事情——包括一些你可能不应该做的事情

![](img/827e2c60217fe1ac68154d6c10079ba7.png)

Reddit [的 Elixir 论坛上最近的一篇帖子询问了 Elixir 如何在没有继承的情况下实现代码重用](https://www.reddit.com/r/elixir/comments/yy6k69/how_does_a_functional_language_based_framework/)。来自面向对象的背景，OP 习惯于通过用类继承行为来解决这个问题，但不确定如何用像 [Elixir](http://elixir-lang.org) 这样的[函数式语言](https://en.wikipedia.org/wiki/Functional_programming)来解决这个问题。

一般的答案是，Elixir 倾向于一种[组合](https://en.wikipedia.org/wiki/Object_composition)方法，通过从其他模块导入功能来构造复杂的行为。但是这篇文章让我思考:尽管这可能是一件可怕的事情，但在 Elixir 中实现经典的面向对象风格继承有可能吗？

事实证明，*是的* : Elixir 的宏系统足够强大，可以用模块实现面向对象的风格继承。在这篇文章中，我们将学习 Elixir 宏系统以及如何使用它来实现继承。我们还将谈一谈为什么这样做可能不是一个好主意。

[*元编程*](https://en.wikipedia.org/wiki/Metaprogramming) *，*广义来说，就是将代码作为数据来操作的能力。这意味着编程语言可以读取、修改和生成自己的代码，它就像听起来一样强大和危险。

[Elixir 通过](https://amzn.to/3Y65r2I) [其*宏*系统](https://elixir-lang.org/getting-started/meta/macros.html)启用元编程。宏系统让我们*编写并执行生成代码的代码，*这为各种语言扩展和代码重用机制打开了大门。让我们看一个简单的例子，我们有一些“mixin”函数想要注入到一个模块中:

```
defmodule Mixin do 
  defmacro __using__(_) do 
    quote do 
      def mixin_func1(value), do: IO.inspect(value) 
      def mixin_func2(value), do: IO.inspect(value * 2) 
    end 
  end 
end 

# add the "use" macro to inject functions into MyModule 
defmodule MyModule do 
  use Mixin 
end 

MyModule.mixin_func2(5) 
> 10
```

不是简单地*执行代码，*宏可以*返回调用者可以执行的代码-* 包括动态生成的代码*。*这是通过使用`quote do ... end`块引用 [*代码并返回其数据表示*](https://elixir-lang.org/getting-started/meta/quote-and-unquote.html)而不是执行它*来完成的。*在这个简单的用例中，`[__using__](https://elixir-lang.org/getting-started/alias-require-and-import.html#use)`[宏](https://elixir-lang.org/getting-started/alias-require-and-import.html#use)让我们有效地用`use Mixin`将我们的函数定义代码注入到`MyModule`中。

对于一个更真实的例子，考虑在 [Phoenix Framework](http://phoenixframework.org) 的默认项目中生成的这段代码(有一些修改)。在这个例子中，像`MyAppWeb.UserController`这样的控制器模块需要访问某些功能。代替*子类化*一些基本控制器类，我们使用一个宏来注入期望的代码:

```
defmodule MyAppWeb do 
  def controller do
    quote do 
      use Phoenix.Controller, namespace: MyAppWeb 
      import Phoenix.LiveView.Controller 
      import Plug.Conn import MyAppWeb.Gettext 
      alias MyAppWeb.Router.Helpers, as: Routes 
    end 
  end 

  defmacro __using__(which) when is_atom(which) do 
    apply(__MODULE__, which, []) 
  end 
end 

defmodule MyAppWeb.UserController do 
  use MyAppWeb, :controller 
end
```

从代码重用的角度来看，元编程让我们完成与继承相同的事情——以及不完全符合继承模型的其他形式的代码重用。注意，这个例子不仅导入了一些功能，它还调用了*的另一个*用途。

但是宏远远超出了简单的代码重用:除了使用*注入代码之外，*宏还可以用来构造控制结构或者[特定领域语言](https://en.wikipedia.org/wiki/Domain-specific_language)API(DSL)。这里有一个来自流行的 [Ecto 数据库映射器库](https://hexdocs.pm/ecto/Ecto.html)的 API 示例:

```
import Ecto.Query, only: [from: 2] query = from u in "users", where: u.age > 18, select: u.name Repo.all(query
```

Ecto 为`from`使用了一个宏定义，而不是一个函数，这样 API 用户就可以编写类似`u.age > 18`的表达式，这些表达式可以被转换成数据库查询，而不是被直接求值。

在函数调用中，表达式照常计算，函数接收*值。*

在宏中，表达式以代码的形式提供*，可以根据宏作者的选择进行读取、操作或执行。在这种情况下，表达式被转换成 SQL 数据库查询片段，而不是在 Elixir 中执行。*

为了更深入地了解这一点，让我们来看一个来自[酏剂文档](https://elixir-lang.org/getting-started/meta/macros.html)的例子。

在下面的例子中，一个宏被用来生成一个名为`Unless`的新控制结构。

该示例显示了一个使用函数的实现和一个使用宏的实现，以说明不同之处:

```
defmodule Unless do 
  def fun_unless(clause, do: expression) do 
    if(!clause, do: expression) 
  end 

  defmacro macro_unless(clause, do: expression) do 
    quote do 
      if(!unquote(clause), do: unquote(expression)) 
    end 
  end  
end
```

在函数示例中，`expression`作为正常程序执行的一部分被立即评估。但是通过使用*宏，*表达式被注入到一个控制结构中，并且只有当`clause`为假时才被执行——这是我们期望从一个流控制结构中得到的行为！

```
iex> require Unless 
iex> Unless.macro_unless true, do: IO.puts "this should never be printed" 
nil 
iex> Unless.fun_unless true, do: IO.puts "this should never be printed" 
"this should never be printed" 
nil
```

`Unless`宏使用了一个`quote do ... end`块，就像我们之前的例子一样，但是也使用了`unquote`将传入的表达式注入到引用的代码块中。

虽然元编程非常强大，但它也是“危险的”,因为它可以修改预期的代码行为，如果使用不当，会使代码非常难以理解。正如酏剂文献所述:

> *宏只能作为最后的手段使用。记住* ***显性比隐性*** *。* ***清晰的代码胜过简洁的代码***

编写宏对于日常的 Elixir 开发来说有些不常见，除了一些常见的和已经建立的用例，比如上面的`__using__`例子。

## 为面向对象的样式继承构建宏

现在我们已经看到了如何在 Elixir 中使用宏来扩展模块行为，让我们回到最初的问题:我们可以使用 Elixir 宏来实现 OO 风格的继承吗？我们可以，但是需要一个更复杂的宏！

首先，让我们陈述我们的继承宏的目标:

*   我们希望实现一个“继承”宏，它将接受一个父模块，并将父模块中的所有函数注入到当前模块中。
*   它应该与多级继承一起工作，这样如果 C 从 B 继承，B 从 A 继承，那么 C 也应该能够调用 A 函数。
*   像大多数面向对象语言一样，函数应该是可重写的:我们应该允许继承的函数被重写和重新实现
*   最后，我们应该支持多重继承:从单独的基类继承并从两者获得函数的能力。

如果我们正确地实现了 inherit 宏，我们应该能够编写如下代码:

```
defmodule Base do 
  def f1(a), do: a * 1 
  def f2(a), do: a * 2 
end 

defmodule Base2 do 
  def f3(a), do: a * 3 
end 

defmodule Derived do 
  use Inherit, Base 
end 

defmodule MyModule do 
  use Inherit, Derived 
  use Inherit, Base2 

  def f2(a), do: a * 10 
end 

# Call an inherited function 
MyModule.f1(a) |> IO.inspect() 
> 5 

# Call an overridden function 
MyModule.f2(a) |> IO.inspect() 
> 50 

# Call a function inherited with multiple-inheritance 
MyModule.f3(5) |> IO.inspect() 
> 15
```

经过一些尝试和错误来解决下面描述的一些棘手的问题，我能够设计出一个通过所有测试的宏:

```
defmodule Inherit do 
  defmacro __using__(quoted_module) do 
    module = Macro.expand(quoted_module, __ENV__) 

    module.__info__(:functions) |> Enum.map(fn {name, arity} -> 
      # Generate an argument list of length `arity` 
      args = arity== 0 && [] || 1..arity |> Enum.map(&Macro.var(:"arg#{&1}", nil)) 

      quote do 
        defdelegate unquote(name)(unquote_splicing(args)), to: unquote(module) 
        defoverridable [{unquote(name), unquote(arity)}] 
      end 
    end) 
  end 
end
```

我们在这个宏中所做的是

*   用`defmacro`实现一个宏，它接受一个基本模块来继承——注意，我们需要用[宏来获取未引用的模块](https://hexdocs.pm/elixir/1.12/Macro.html#expand/2)
*   使用`[module.__info__](https://hexdocs.pm/elixir/1.12/Module.html#c:__info__/1)(:functions)`遍历模块公开的所有函数
*   使用`[defdelegate](https://hexdocs.pm/elixir/1.12/Kernel.html#defdelegate/2)`来定义一个指向基本实现的函数——这是最棘手的部分，下面会有更详细的描述。
*   如果需要，使用`[defoverridable](https://hexdocs.pm/elixir/1.12/Kernel.html#defoverridable/1)`允许函数在派生类中被覆盖

如上所述，这个宏的棘手部分是构造`defdelegate`调用，特别是用*正确数量的参数*生成一个委托定义。

例如，如果我们需要定义一个有 3 个参数的方法`do_stuff`，我们需要生成如下代码:

```
defdelegate do_stuff(a1, a2, a3), to: unquote(module)
```

构造一个参数变量列表很简单，但是我们如何把它放到函数定义中呢？事实证明，一个简单的`unquote`并不能解决我们这里的问题——它本质上是一个只有一个列表参数的函数定义。相反，我们使用一个名为`[unquote_splicing](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#unquote_splicing/1)`的函数:它取消对列表的引用，就地展开元素。让我们快速看一个简单的`unquote`对`unquote_splicing`的例子:

```
iex(1)> quote do [1, unquote(list), 2] end 
[1, [2, 3], 2] 
iex(2)> quote do [1, unquote_splicing(list), 2] end 
[1, 2, 3, 2]
```

有了这个扩展参数的技巧，我们实现函数定义块如下:

```
quote do 
  defdelegate unquote(name)(unquote_splicing(args)), to: unquote(module) 
  defoverridable [{unquote(name), unquote(arity)}] 
end
```

瞧啊。只用几行代码，我们就在 Elixir 中实现了 OO 风格的继承。

# 结论:说真的，不要这样做

在 Elixir 中实现面向对象的风格继承是学习更多关于宏系统的一个很好的小练习，但是在 Elixir 应用程序中使用它可能不是一个好主意。虽然继承在其他语言中是一种常见的工具，但它不是一种常见的灵丹妙药习语，如果在灵丹妙药代码库中使用，很可能会引起混淆(甚至是理所当然的嘲笑)。

也就是说，宏系统的强大之处在于，我们*可以在必要时*实现这些种类的专门结构。虽然我无法想象在日常应用程序开发中使用这样的宏，但是像这样的功能肯定可以用于专门的库或 DSL，类似于 Ecto 对宏的专门使用。

要了解更多关于 Elixir 中的元编程，我推荐书[元编程 Elixir:编写更少的代码，完成更多的工作(并从中获得乐趣！)](https://amzn.to/3VFsY8R)作者 Phoenix Framework 作者 Chris McCord。

*原载于*[*https://blixtdev.com*](https://blixtdev.com/dont-do-this-object-oriented-inheritance-in-elixir-with-macros/)