# 如何在 Golang 中使用模板？

> 原文：<https://betterprogramming.pub/how-to-use-templates-in-golang-46194c677c7d>

## 标准模板包概述

![](img/8cd2c7e108c485479b71da5b0fab6cb5.png)

克拉克·范·德·贝肯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我将解释 Go 语言(Golang)的标准模板包的基础。这些基础知识包括在 Golang 模板中使用变量、条件语句、循环变量以及对变量应用函数。

Golang 提供了用于处理开箱即用模板的`text/template`和`html/template`包。

第一个包是最通用的——您可以用它来为所有类型的文本字符串创建模板。第二个包更适合 HTML——它可以方便地处理 HTML web 环境中的不安全变量。

这些包包含各种函数，可以加载、解析和评估模板文本或(HTML 或文本)文件。

例如，您可以使用以下函数:

*   使用`Parse`解析程序中的文本字符串。
*   使用`ParseFiles`加载并解析模板文件。
*   使用`Execute`使用特定的数据字段将模板呈现给一些输出。

在下文中，我将讨论在 Golang 中创建强大模板的基本构件。

# 外部(程序)变量

你可以将变量从你实际的 Go 程序中发送到你的模板中。然后，您可以在模板中使用这些变量。

首先，当您希望在模板中呈现特定的动作时，您可以通过在文本字符串中的双括号`{{}}`之间添加这些动作来实现。

我们可以用它来显示你在程序中提供的变量。因此，如果您在双括号内添加一个点，您的所有数据字段都将呈现在那里。

例如，`{{.}}`会将所有数据字段呈现为格式化字符串。

此外，还可以通过指定字段的名称来访问数据的特定字段。

例如，`{{ .Title }}`只会将`Title`字段呈现为格式化字符串。

您可以在下面的代码示例中看到这些操作的应用。

```
package main import ( 
    "os" 
    "text/template" 
) type Book struct { 
    Title     string 
    Publisher string 
    Year      int 
} func main() { 
    t1 := template.New("Template") 
    t1, _ = t1.Parse("External variable has the value [{{.}}]\n") 
    t1.Execute(os.Stdout, "Amazing")     b := Book{"The CSound Book", "MIT Press", 2002} 
    t1.Execute(os.Stdout, b)     t2 := template.New("Template") 
    t2, _ = t2.Parse("External variable Book has the values [Title: {{.Title}}, Publisher: {{.Publisher}}, Year: {{.Year}}]\n") 
    t2.Execute(os.Stdout, b) 
}// Output
// External variable has the value [Amazing] 
// External variable has the value [{The CSound Book MIT Press 2002}] 
// External variable Book has the values [Title: The CSound Book, Publisher: MIT Press, Year: 2002]
```

[*运行这里的代码……*](https://go.dev/play/p/uZ8imiNxXjW)

我们首先在上面的代码中用`template.New()`创建一个新的空模板。然后我们使用`Parse()`将一个字符串解析到这个模板中。在这个字符串中，我们在双括号之间添加了一个动作。

由于我们在括号之间放了一个点，这就向模板呈现器发出信号，需要在这里呈现所提供的变量。

使用`Execute()`来呈现带有变量的模板。第一个参数(`os.Stdout`)是渲染模板输出的位置。第二个参数(`Amazing`)是我们希望在模板中呈现的变量。在这种情况下，它只是一个字符串。

例如，我们可以提供一个结构，而不是向呈现器提供一个字符串。这里我们创建了一个结构来保存一本书的数据。随后，我们可以将它提供给之前创建的同一个模板。

也可以呈现结构的单个数据字段。为此，创建一个新的模板字符串。我们没有在双括号之间放一个点，而是在我们希望呈现的结构中放一个点，后跟数据字段的名称。请注意，该数据字段用大写字母书写，表示它是可导出的。

注意，`Parse`函数实际上返回了两个变量。第一个变量是解析后的模板，第二个变量是错误消息。我们不会在上面的例子中使用错误信息，所以我们只放了一个下划线。

# 内部(模板)变量

可以定义内部变量，即只在模板中定义。

以下模式显示了如何使用模板字符串中定义的变量。首先是定义，然后是使用。内部变量应该以美元符号($)开头。

```
{{$var:=`value`}}  ... {{$var}} ...
```

下面是一个简单的例子:

```
package mainimport (
    "os"
    "text/template"
)func main() {
    t, _ := template.New("Template").Parse("{{$var:=2150}}Internal variable has the value [{{$var}}]") t.Execute(os.Stdout, nil)
}// Output:
// Internal variable has the value [2150]
```

[*运行这里的代码……*](https://go.dev/play/p/CZUi54PaiYZ)

与使用点来呈现外部变量不同，这里我们提到我们之前在双括号中定义的变量。

# 条件式

下面，你可以看到条件句的一般模式:

```
{{if [..]}} if-part {{end}}
{{if [..]}} if-part {{else}} else-part {{end}}
{{if [..]}} if-part {{if else}} if-else-part {{end}}
```

在最后一个模式中，您可以根据需要多次使用`{{if else}}`部件，也可以添加一个`{{else}}`部件。下面是第二种模式的简单示例:

```
package main import ( 
    "os" 
    "text/template" 
) func main() { 
    t, err := template.New("Template").Parse("{{if eq . `filler`}}This is filler...{{else}}It's something else...{{end}}\n") 
    if err != nil { 
        panic(err) 
    } 
    t.Execute(os.Stdout, "filler") 
}// Output:
// This is filler...
```

[*运行这里的代码……*](https://go.dev/play/p/QGJqP1x9g1N)

注意，在上面的例子中，我们还使用了从`Parse()`函数返回的错误消息。如果没有错误，错误信息等于' *nil* '。如果有错误，我们可以用`panic()`函数处理错误。

同样，代替等号`eq` (for ==)，你也可以用:

*   全盘否定`ne`(为！=)
*   低于`lt`(对于<)
*   低于或等于`le`(对于< =)
*   大于`gt`(对于>)
*   大于或等于`ge`(对于> =)

此外，可以编写一个布尔变量来代替比较语句。

```
package mainimport (
    "os"
    "text/template"
)func main() {
    t, _ := template.New("Template").Parse("{{if .}}This is true.{{else}}This is false.{{end}}\n")
    t.Execute(os.Stdout, false)
}// Output:
// This is false
```

[*运行这里的代码……*](https://go.dev/play/p/pTrgHbifWU-)

# 环

在数组、切片或映射上循环时，有几种模式可以在模板中使用。首先，我们来看看最简单的形式。

```
{{range .Var}}
    {{.}}
{{end}}
```

这里，我们对数组、切片或映射中的每个变量进行循环。Var)。在循环的每一步中，都有一个变量可供循环使用。

现在`{{.}}`不再代表我们的模板可用的所有变量，而只代表循环中可用的变量。

```
package main import ( 
    "os" 
    "text/template" 
) func main() { 
    computerList := []string{"Arduino", "Raspberri Pi", "NVidia Jetson Nano"} 
    t, err := template.New("Template").Parse("My favorite computers are:\n{{range .}}{{.}}\n{{end}}\n") 
    if err != nil { 
        panic(err) 
    } 
    t.Execute(os.Stdout, computerList) 
}// Output:
// My favorite computers are: 
// Arduino 
// Raspberri Pi 
// NVidia Jetson Nano
```

[*运行这里的代码……*](https://go.dev/play/p/odSFyC_hXwA)

在对一组变量进行循环时，可以使用一种更复杂的模式，如下所示:

```
{{range $index, $element := . }} 
    {{$index}} ... {{$element}} ...
{{end}}
```

当您需要循环中可用的变量索引时，这种模式非常方便。例如，如果您希望输出一个带有编号的有序列表，您可以使用这个模板模式。

注意这里的变量`$index`和`$element`是在模板中定义的内部变量。因此，他们也以“ *$* ”开始。

```
package main import ( 
    "os" 
    "text/template" 
) func main() { 
    dishesList := []string{"Enciladas con Pollo", "Hot&Spicy Pizza", "Spaghetti Bolognese"} 
    t, err := template.New("Template").Parse("My favorite dishes are:\n{{range $index, $item:=.}}{{$index}}) {{$item}}\n{{end}}\n") 
    if err != nil { 
        panic(err) 
    } 
    t.Execute(os.Stdout, dishesList) 
}// Output
// My favorite dishes are: 
// 0) Enciladas con Pollo 
// 1) Hot&Spicy Pizza 
// 2) Spaghetti Bolognese
```

[*运行这里的代码……*](https://go.dev/play/p/22s-igRk-uS)

如您所见，这种方法有一个问题。我们从输出中得到的有序列表从 0 开始。原因是第一个索引总是 0。

对于许多应用程序来说，这不是我们想要的。我们希望有序列表从 1 开始。那么我们如何将每个索引递增 1 呢？我们需要一个函数来增加一个给定的变量。

在下一部分中，我们将研究在模板字符串中运行函数。

# 模板内的函数

可以在模板中使用函数。

为此，您需要将希望使用的函数映射到您将在模板中使用的关键字。

在下面的例子中，我们将创建一个名为`add()`的加法函数。我们将在`FuncMap()`函数中使用`template.FuncMap{}`将其添加到函数映射中。

使用`add`函数，我们将能够给`$index`变量加 1，从而递增它。结果将是一个可读性更好的有序列表。

```
package main import ( 
    "os" 
    "text/template" 
) func add(a, b int) int { 
    return a + b 
} func main() { 
    dishesList := []string{"Enciladas con Pollo", "Hot&Spicy Pizza", "Spaghetti Bolognese"}     t, err := template.New("Template").Funcs(template.FuncMap{"add": add}).Parse("My favorite dishes are:\n{{range $index, $item:=.}}{{add $index 1}}) {{$item}}\n{{end}}\n")     if err != nil { 
        panic(err) 
    }     t.Execute(os.Stdout, dishesList) 
}// Output:
// My favorite dishes are: 
// 1) Enciladas con Pollo 
// 2) Hot&Spicy Pizza 
// 3) Spaghetti Bolognese
```

[*运行这里的代码……*](https://go.dev/play/p/nDL1cdNLolH)

你可以在下面找到一个更复杂的例子。这里我们添加第二个函数。我们的想法是创建一个 CSV 样式的输出，但是当我们将“分隔符”添加到函数映射时，我们可以选择我们想要的分隔符符号。

您还可以看到映射中使用的名称与实际的函数名称不同。

```
package mainimport (
    "os"
    "text/template"
)func add(a, b int) int {
    return a + b
}func delimiter(s string) func() string {
    return func() string {
        return s
    }
}func main() {
    dishesList := []string{"Enciladas con Pollo", "Hot&Spicy Pizza", "Spaghetti Bolognese"} tmpl := "Index{{dl}}Dish\n{{range $index, $item:=.}}{{add $index 1}}{{dl}}{{$item}}\n{{end}}\n" funcMap := template.FuncMap{"add": add, "dl": delimiter(",")} t, _ := template.New("Template").Funcs(funcMap).Parse(tmpl) t.Execute(os.Stdout, dishesList)
}// Output:
// Index,Dish
// 1,Enciladas con Pollo 
// 2,Hot&Spicy Pizza 
// 3,Spaghetti Bolognese
```

[*运行这里的代码……*](https://go.dev/play/p/cRDjZdztFZ7)

有一个方便的带有模板函数映射的库，叫做 Sprig。这里可以找到[。然而，这个库似乎只适用于`html/template`包。](http://masterminds.github.io/sprig/)

# 例子

为了进一步说明 Golang 中模板的使用，我创建了两个例子:

[](/how-to-generate-code-using-golang-templates-942cba2e5e0c) [## 如何使用 Golang 模板生成代码

### 如何根据构建器设计模式使用 Golang 模板生成代码的示例

better 编程. pub](/how-to-generate-code-using-golang-templates-942cba2e5e0c) [](/how-to-generate-html-with-golang-templates-5fad0d91252) [## 如何用 Golang 模板生成 HTML

### 如何使用 Go 模板生成 HTML 页面的示例

better 编程. pub](/how-to-generate-html-with-golang-templates-5fad0d91252) 

# 参考

*   [“使用 Golang 构建 Web 应用程序—第 7.4 章模板”，作者 Astaxie](https://astaxie.gitbooks.io/build-web-application-with-golang/content/en/07.4.html)
*   [“以身作则:文本模板”，作者 Mark McGranaghan 和 Eli Bendersky](https://gobyexample.com/text-templates)
*   [Go Dev 的模板包](https://pkg.go.dev/text/template)