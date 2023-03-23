# 要避免的 5 个常见 Golang 编码错误

> 原文：<https://betterprogramming.pub/top-5-common-golang-coding-mistakes-the-ugly-sides-of-a-great-programming-language-e0b64915707>

## 伟大的编程语言丑陋的一面

![](img/64087e1f2afd9327127f293bc307400d.png)

由 [Sarah Kilian](https://unsplash.com/@rojekilian?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

Go 是一种简单易学的编程语言。围棋程序也非常快，因为它们被编译成机器码，有一个静态类型系统。此外，Go 具有内置的垃圾收集功能，并且仍然支持指针、按值传递和按引用传递的概念。这真的很强大，因为您不会受到语言的限制(像 Java 或其他高级编程语言)。

但是，它也带来了很多困惑，并导致许多开发人员犯错误。在本文中，我们将讨论其中的一些。

# **1。对切片的操作可能会也可能不会创建新的底层数组**

在 Go 中，切片是*引用类型*。它们引用基础数组。当您从现有切片创建新切片时，它们实际上引用相同的底层数组。在示例中，`s1`、`s2`和`s3`都引用同一个底层数组，因此当`s1[2]`被更新时，所有三个数据片都被更新。

然而，如果原始数组不能容纳更多的新数据，向片[添加新元素可能会导致](https://blog.golang.org/slices)进行新的数组分配。`s4`就是这种情况。因此，我们在处理切片时需要非常小心，因为底层数据可能会以您意想不到的方式发生变化。

下面的代码可用于确保我们可以获得对新底层数组的引用(如果它已被分配):

```
type Stack []interface{}
func (stack *Stack) Push(x interface{}) { 
    *stack = append(*stack, x)
}
```

# **2。range 子句中的数据是实际收集元素的副本**

与大多数编程语言中内置的垃圾收集不同，Go 中的`range`子句生成的数据值出人意料地不引用原始项。它们是实际集合元素的副本。因此，在上面的示例中，更新值不会更改原始数据。要更新原始值，需要索引操作符来访问它们:

```
for i, _ : range s {
    s[i].X = i
    s[i].Y = i
}
```

# **3。可寻址值与不可寻址值**

可寻址值在围棋中是一个棘手的概念。我们不会详细讨论这个概念(你应该阅读这篇文章)，但是我们会讨论它如何改变我们的编码行为。

上面的代码不能工作，因为 map 中的值是不可寻址的，也不能被赋值。函数的返回值也会出现类似的错误:

```
sfunc().field = .... // error when assigning struct field from func
afunc()[0] = .... // error when assigning the array item from func
```

在这种情况下，可以使用临时变量作为解决方法:

或者您可以使用指针映射，因为指针间接寻址是可寻址的:

```
m := make(map[string]*Point)
m["p1"] = &Point{1,1}
m["p1"].X = 2
fmt.Println(m["p1"])
--------------
&{2 1}
```

# **4。返回指向局部结构的指针**

```
func test() *int {
   var i int = 1
   return &i;
}
```

在 C/C++中，上面的代码不能工作，因为局部变量是在堆栈中分配的，当函数返回时，它将消失。然而，在 Go 中，编译器决定将变量分配到哪里。编译器根据变量的大小和“转义分析”的结果来选择存储变量的位置在这种情况下，编译器看到返回了局部变量`i`的地址，所以它只是将该变量放在堆上(而不是堆栈上)。

更多详情可以阅读[本文](https://www.ardanlabs.com/blog/2017/05/language-mechanics-on-escape-analysis.html)。

通过理解转义分析，您可以避免代码中的一些性能问题，因为在堆栈和堆中分配变量会产生性能差异。

在`go build`或`go run`中可以使用`-m gcflag`来知道你的变量被分配到哪里(例如`go run -gcflags -m test.go`)。

# **5。指针的零检查可能会令人困惑**

在本质上，Go 中的接口可以被认为是一个值和一个具体类型的元组(一个接口保存一个特定底层具体类型的值)。持有`nil`具体值的接口变量本身是非`nil`。如果要检查底层类型的值是否为`nil`，可以这样检查:

```
np == (*int)(nil) // return true
```

# 参考

1.  [https://www . ardan labs . com/blog/2017/05/language-mechanics-on-escape-analysis . html](https://www.ardanlabs.com/blog/2017/05/language-mechanics-on-escape-analysis.html)
2.  [https://golang.org/ref/spec#Address_operators](https://golang.org/ref/spec#Address_operators)
3.  [https://utcc . utoronto . ca/~ cks/space/blog/programming/goadressablevalues](https://utcc.utoronto.ca/~cks/space/blog/programming/GoAddressableValues)
4.  [https://golang.org/doc/effective_go](https://golang.org/doc/effective_go)