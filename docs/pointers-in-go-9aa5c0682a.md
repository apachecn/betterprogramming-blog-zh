# 围棋指针的基本原理

> 原文：<https://betterprogramming.pub/pointers-in-go-9aa5c0682a>

## 理解指针类型以及如何在数据结构中使用它们

![](img/72829283d331ab25c746cc19546e3d4e.png)

由[迈克尔·帕尔祖乔斯克](https://unsplash.com/@mparzuchowski)本人在 [Unsplash](https://unsplash.com/) 的“市场广场咖啡馆”

假设，你和你的朋友计划在每个人都喜欢的咖啡馆聚会。你们都到了那个地方，在咖啡馆门口。但是上面写着:“抱歉，我们已经搬到了 XYZ 的这个地方”。所以，因为这是你最喜欢的咖啡馆，你会去新的地址。指针就是这样。有点像咖啡馆门上的标志，指引你去不同的地址/位置。

## 那么，什么是指针，为什么我们需要使用它们？

指针是存储内存地址的变量。简单地说，它将值存储在计算机内存中。虽然以后我们可以使用指针地址来访问它。

当我们想通过引用某个函数来传递一个变量时，或者指出数组、结构、接口、函数等时，我们使用指针。

像 C 这样的语言在使用指针时经常会面临一些崩溃和安全漏洞。Go 使用指针，但具有内存安全性。所以我们现在很安全。

嗯，这是一个关于指针的简介。所以我们来多了解一下围棋中的指针。

# 与号(&)和星号(*)

地址操作符(&)只是决定一个变量在内存中的地址。假设你给一个变量赋值。你的计算机将把这个值以十六进制数的形式存储在内存地址(RAM)中。

```
value := 901
fmt.Println(&value)
//Prints -> 0xc000018030
```

当我们需要解引用指针的值时，我们可以使用(*)操作符来解引用指针变量并检索它的值。好了，让我们更熟悉一个好例子:

```
value := 65
memoryAdd := &value
fmt.Println("Value:", value) // 65
fmt.Println("Memory address:", &memoryAdd) // 0xc000100018 
fmt.Println("Dereferencing using pointer:", *memoryAdd) // 65
```

我们还可以使用指针操作符通过解引用来更新原始值。下面是一个例子:

```
var name string = "John"
var pointer *string = &namefmt.Println("Name =", name) //John
fmt.Println("pointer =", pointer) //0xc000010230
fmt.Println("*pointer =", *pointer) //John*pointer = "Robert Downy Junior"fmt.Println("*pointer =", *pointer) //Robert Downy Jr
fmt.Println("name =", name) //Robert Downy Jr
```

在上面的例子中，定义为名称的变量有一个值`John`,我们希望在变量前使用(*)用指针变量来取消对它的引用。

因此，在我们的例子中，分配给新值`Robert Downy Jr`的指针变量可以改变指向内存地址的指针的值。你猜怎么着？它还会更改变量名本身的值。这就是解引用的美妙之处——它可能令人困惑，但同时又非常容易重用。

# 指针类型

简单来说，指针类型是在数据类型之前初始化的指针变量。这里有一个例子:

```
types := 881
address := &types
fmt.Printf("Address is a %T\n", address) // Address is a *int
```

`*int`表示类型是指针。如果指针类型是`*int`，它可以指向同类型的任何其他变量。

在上面的解引用示例中，我们可以看到，首先，我们定义了一个值为`*string`的变量`name`，我们用另一个字符串值解引用了指针值——它还用另一个字符串值更新了原始变量名。

在 Go 中使用指针类型的主要优点是它避免了不安全的操作，比如更新或操作任何值。因此，Go 也是一种严格类型化的语言。

> *注意:在 C 语言类型中，一个内存地址可以保存不同的指针类型，而在 Go 中，它会抛出一个错误。查看此链接以供参考:【https://go.dev/play/p/PFBD3_5BnLP】T5*

如果我们在 go 中使用数组、切片、映射等数据结构来定义指针会怎么样？，而那个时候 go 编译器会返回什么呢？所以，让我们更深入地了解一下指针。

# 指针在数据结构中的本质

首先，让我们通过在 Go 中使用数据结构来进一步熟悉指针

## 在结构中使用指针

这个指针指向在结构中存储数据的内存块的地址。这里有一个例子:

```
type Person struct {
 name    string
 age     int
 canSing bool
}func main() {
 john := &Person{
  name:    "John",
  age:     22,
  canSing: false,
 }fmt.Printf("%+v\n", john) // &{name:John age:22 canSing:false}
}
```

正如你在这里看到的，我们定义了一个名为`Person` 的结构，之后，我们通过在结构前使用地址操作符初始化一个名为 john 的变量来定义值。

上述示例的输出将返回前面带有地址运算符的已定义变量的值。

```
fmt.Printf("%+v\n", *john) // {name:John age:22 canSing:false}
john.canSing = true
fmt.Printf("%+v\n", *john) // {name:John age:22 canSing:true}
```

在这里，我们可以使用指针操作符取消对变量的引用，并在 struct 中定义值。

## 在数组中使用指针

数组可以以地址操作符为前缀，创建一个指向数组的新指针。数组还提供自动解引用。这里有一个例子:

```
person := &[3]string{"John", "22", "12-12-2021"}
fmt.Println(person[0]) // John
fmt.Println(person[:2]) // [John 22]
```

正如我们在这里看到的，即使在索引或者切片中，它也会自动取消对返回值的引用。

## 在地图中使用指针

在地图中使用指针理论上不是一个好的尝试，因为 go 在后台为一些内置的集合使用指针。如果映射中的键或值可以是指针类型，这没问题，但对于整个映射来说不是这样。它会在自然界中冗余地活动。

# 函数中的指针

一般来说，函数是一种将输入作为参数并以解决任何问题的方式给出期望输出的方法。所以，在编写函数时，我们可以将参数定义为按值传递和按引用传递。下面是对同一篇的[精彩解读:](https://www.educative.io/edpresso/pass-by-value-vs-pass-by-reference)

> 在按值传递中，它创建值的副本，该副本作为形式参数发送给函数，对函数的任何更改只会影响函数的变量，不会更新函数范围之外的值。

为了更好地理解，这里有一个例子:

```
func main() {
 var name string = "Nikhil"
 fmt.Println("Name = ", name) // Nikhil
 changeName(name)
 fmt.Println("Name = ", name) // Nikhil
}func changeName(name string) {
 name = "John"
 fmt.Println("Updated name = ", name) // John
}
```

在上面的例子中，我们可以看到，name 的值只有在`changeName` 函数中被更新时才会被更新，所以 name 的值现在只对这个函数有所不同，因为这些值是在函数范围内的。

在全局和外部函数作用域中，它将按照最初定义的方式运行。但是，如果我们想更新函数的值，而它也应该更新或改变原始值呢？在这种情况下，我们可以使用按引用传递。

> 在按引用传递中，当值在函数调用中被更新时，它更新原始值。因此，在这种情况下，它在实际参数前使用指针并通过引用将参数传递给函数调用，同时更新值。这里有一个例子:

```
func main() {
 var name string = "Nikhil"
 fmt.Println("Original name =", name) // Nikhil
 changeName(&name)
 fmt.Println("Original name is now changed to =", name) // John
}func changeName(name *string) {
 *name = "John"
 fmt.Println("Updated name =", *name) //John
}
```

# 方法中的指针

在方法中，与函数有一点不同，在这点上，我们在方法声明中有一个接收器作为参数。

就像函数一样，无论是否将值作为参数作为指针发送，方法的行为都是一样的。

在方法的情况下，如果我们想作为接收者发送值，它不会影响原始值。例如:

```
type About struct {
 name string
 age  int
}func main() {
 var details About = About{name: "Nikhil", age: 25}
 fmt.Printf("%+v\n", details) // {name:Nikhil age:25}
 details.UpdateDetails()
 fmt.Printf("%+v\n", details) // {name:Nikhil age:25}
}func (a About) UpdateDetails() {
 a.age = 35
 a.name = "Antman"
 fmt.Printf("%+v\n", a) // {name:Antman age:35}
}
```

在这个例子中，我使用了 structs，因为它是 Go 中用户定义的类型。它还支持结构类型上的方法。因此，接收方可以是结构或非结构类型。

回到我们的代码，这里我们定义了一组数据，其中有两个字段:`name`和`age` *。*我们还定义并调用了一个名为`UpdateDetails` 的方法，该方法实际上只是在使用函数 receiver 作为来自结构类型为`About` *的形参的参数时更新值。*

因为我们也定义了结构类型为`About` 的`details`变量来初始定义值。在这里，我们可以看到与我们在函数调用中使用*传值*时看到的相同的预期结果。

如果我们想要更新值，就像我们在*按引用传递中所做的一样，*我们只需要在结构类型前添加一个指针来更新方法范围之外的原始值，不管它是否也在主函数之外声明。这里也有一个例子:

```
type About struct {
 name string
 age  int
}func main() {
 var details About = About{name: "Nikhil", age: 25}
 fmt.Printf("%+v\n", details)
 details.UpdateDetails()
 fmt.Printf("%+v\n", details)
}func (a *About) UpdateDetails() {
 a.age = 22
 a.name = "John"
 fmt.Printf("%+v\n", *a)
}
```

这就是指针在方法中的工作方式，很简单。

# 指针中的 Nils

假设有这样一种情况，你已经初始化了一个指针，但你还没有定义或分配它。所以，在围棋中发生的是所有的变量都有零值。所以，如果只是初始化一个数组，切片，指针，它将有零值，所以 Go 把这个零值当作 nil，意思是“什么都不初始化”。

让我们举一个例子来检查它实际上是如何发生的。因此，我将举一个我在上面的函数使用指针中解释过的例子

```
type Person struct {
 name string
}func main() {
 var name *Person
 fmt.Printf("%+v\n", name)
 changeName(name)
 fmt.Printf("%+v\n", name)
}func changeName(name *Person) {
 name = &Person{name: "Nikhil"}
 if name == nil {
  fmt.Println("Name is nil")
  return
 }
 fmt.Printf("%+v\n", name)
}
```

我没有给变量`name` *，*赋值，而是对它进行了初始化，只是为了检查实际发生了什么，所以在运行它的时候，我得到了这个错误:【https://go.dev/play/p/1MFlmIqSGwM】

错误说**运行时错误:内存地址无效。**所以它证明了一点，如果我们只是初始化了一个指针变量，Go 就把它当作零值，零值本质上是 nil。不过，我们可以为 nil 指针添加一个错误处理检查。

```
func changeName(name *string) {
 if name == nil {
  fmt.Println("Name is nil")
  return
 }
 fmt.Printf("%+v\n", name)
}
```

现在，如果我们想通过将指针的值赋给一个`name` 变量来更新指针的值，我们可以这样做:

```
func main() {
 var name *Person
 fmt.Printf("%+v\n", name) // <nil>
 changeName(name)
 fmt.Printf("%+v\n", name) // <nil>
}func changeName(name *Person) {
 name = &Person{name: "Nikhil"}
 if name == nil {
  fmt.Println("Name is nil")
  return
 }
 fmt.Printf("%+v\n", *name) // {name:Nikhil}
}
```

每当我们使用指针时，我们都应该非常小心，因为它会使程序惊慌。为了避免恐慌，我们需要检查指针本质上是否不为零。

感谢阅读。