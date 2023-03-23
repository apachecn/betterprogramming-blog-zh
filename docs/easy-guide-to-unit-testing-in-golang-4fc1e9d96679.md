# Golang 单元测试简易指南

> 原文：<https://betterprogramming.pub/easy-guide-to-unit-testing-in-golang-4fc1e9d96679>

![](img/11ee1bfcded60f97e740adbe73b6dd95.png)

布莱克·康纳利在 [Unsplash](https://unsplash.com/s/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

单元测试是编写好代码的必要组成部分。每个开发人员都应该知道如何编写好的单元测试。随着越来越多的组织使用 Golang，作为开发人员，我们必须了解如何为 Golang 函数编写漂亮的单元测试。

本指南是对 Golang 单元测试最基本的介绍。它没有涵盖高级主题，如模拟、基准测试和幂等性。我将在以后尝试涵盖这些主题。

所以让我们开始吧。

# 代码

我们今天测试的功能非常简单。我们有一个接口`Operator`，它有两个方法。

`Generate`获取两个整数并基于某个模板创建一个密钥，`Degenerate`获取一个密钥并解码出创建密钥的整数。

如果密钥无效，`Degenerate`提供一个错误。

现在，让我们创建这个接口的一个实现，叫做`keyOp`。

这里面的逻辑非常简单。

我们提供了一个函数`GetKeyOperator`，它用模板`%v_%v`创建了一个新的`keyOp`实例。

`Generate()`将两个整数合并到模板中。

`Degenerate()`将键解组成相应的两个整数。如果键与模板结构不匹配，这由`_`字符的位置决定，我们返回一个错误。

主函数现在可以使用这些方法来查看功能。

正如所料，输出是这样的:

```
key=2_3, a=2, b=3
```

现在我们已经建立了功能。让我们开始测试它。

# 测试

我们将首先在与`operators.go`相同的目录中创建一个名为`operators_test.go`的文件。运行`go test`命令时，Go 自动在带有`_test`后缀的目录中查找文件。

现在，让我们为我们的`Generate()`和`Degenerate()`函数编写测试用例。

对于测试函数，以`Test_`开始函数名，然后为这个测试提供结构。然后你会想要写方法的名字。例如，我们的测试方法将被分别命名为`Test_keyOp_Generate`和`Test_keyOp_Degenerate`。

所有的测试方法都需要一个`testing.T`参数。`T`是传递给测试函数的类型，用于管理测试状态和支持格式化的测试日志。

所以我们的方法签名是这样的:

```
func Test_keyOp_Generate(t *testing.T)
```

`Generate`的参数是两个整数。所以让我们定义`args`来存储这两个整数。

```
type args struct {
   x int
   y int
}
```

现在，让我们定义测试的需求。

我们将把测试组织成它们的名字——以及测试的参数和想要的结果。我们还将添加一些这样的测试:

```
tests := []struct {
   name string
   args args
   want string
}{
   {
      name: "success",
      args: args{
         x: 5,
         y: 50,
      },
      want: "5_50",
   },
   {
      name: "success large integers",
      args: args{
         x: 50000,
         y: 999999,
      },
      want: "50000_999999",
   },
}
```

正如你所看到的，我们已经添加了两个测试用例。一个用于一般成功，另一个用于测试大输入大小。我们还为这些测试用例定义了正确的结果。

现在，我们可以简单地遍历`test`切片并运行测试。

```
for _, tt := range tests {
   t.Run(tt.name, func(t *testing.T) {
      kp := GetKeyOperator()
      if got := kp.Generate(tt.args.x, tt.args.y); got != tt.want {
         t.Errorf("keyOp.Generate() = %v, want %v", got, tt.want)
      }
   })
}
```

我们使用在`testing.T`结构上提供的`Run`方法。`Run`将测试名称和函数(`func (t *testing.T)`)作为输入参数，并返回一个`bool`。它在单独的 goroutine 中运行第二个参数中提供的函数，并阻塞，直到它返回。

在`if`条件下，我们创建并实例化`keyOp`的`kp`，通过检查`got!=want`来检查我们是否得到了预期的结果。如果测试用例失败，我们使用`t.Errorf()`将错误消息写入`testing.T`对象，并适当地构造我们的错误消息。

我们完整的`Generate`测试函数在这里。

现在，让我们试着对`Degenerate()`方法做同样的事情。这里的变化是`args`将只接受一个`string`作为参数。

同样，由于我们从`Degenerate()`返回三个结果，我们将用`wantX`、`wantY`和`wantErr`变量来检查它们。`wantErr`是一个`bool`，它将检测我们是否从被测试的方法收到了一个非 nil 错误。

```
type args struct {
   s string
}
tests := []struct {
   name    string
   args    args
   wantX   int
   wantY   int
   wantErr bool
}{
   {
      name: "success",
      args: args{
         s: "40_99",
      },
      wantX: 40,
      wantY: 99,
   },
   {
      name: "failure",
      args: args{
         s: "4099",
      },
      wantErr:*true*,
   },
}
```

正如您所看到的，我们添加了两个简单的测试用例:一个测试成功，另一个测试失败，它有一个无效的输入。在`success`测试中，我们使用`wantX`和`wantY`检查`x`和`y`的正确值，而在`failure`测试中，我们使用`wantErr`检查非 nil 错误。

我们再次编写一个 for 循环，遍历测试切片，并检查预期结果的匹配情况。这一次，我们检查所有三个参数。`Degenerate`的完整测试功能如下:

# 运行测试用例

我们可以使用`test`命令运行我们的测试用例。`go test`具有多种功能，可在其详细文档[中找到，此处为](https://golang.org/cmd/go/#hdr-Test_packages)。

`go test`最简单的用法是运行当前目录和所有子目录中的所有测试。这可以通过运行以下命令来完成:

```
go test ./...
```

# 结论

我希望这个指南可以作为单元测试 Golang 方法的简单介绍。