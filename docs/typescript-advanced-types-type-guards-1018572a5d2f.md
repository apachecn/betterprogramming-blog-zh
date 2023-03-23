# TypeScript 高级类型:类型保护

> 原文：<https://betterprogramming.pub/typescript-advanced-types-type-guards-1018572a5d2f>

## 检查对象数据类型的各种方法

![](img/3f89afee6498831700b57ad74f529b1b.png)

琳达·芬金在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

TypeScript 具有许多高级类型功能，这使得编写动态类型代码变得容易。它还有助于采用现有的 JavaScript 代码，因为它允许我们在使用 TypeScript 的类型检查功能的同时保留 JavaScript 的动态功能。

TypeScript 中有多种高级类型，如交集类型、联合类型、类型保护、可空类型和类型别名等等。在这篇文章中，我们将看看类型警卫。

# 防护类型

为了检查一个对象是否属于某种类型，我们可以创建自己的类型守卫来检查我们期望出现的成员以及值的数据类型。为此，我们可以使用一些特定于 TypeScript 的操作符以及 JavaScript 操作符。

检查类型的一种方法是使用`as`操作符显式转换一个具有类型的对象。这是访问未在构成联合类型的所有类型中指定的属性所必需的。

例如，如果我们有以下代码:

那么 TypeScript 编译器不会让我们访问`person`对象的`name`属性，因为它只在`Person`类型中可用，而在`Employee`类型中不可用。因此，我们将得到以下错误:

```
Property 'name' does not exist on type 'Person | Employee'.Property 'name' does not exist on type 'Employee'.(2339)
```

在这种情况下，我们必须使用 TypeScript 中可用的类型断言操作符将类型转换为`Person`对象，这样我们就可以访问`name`属性，我们知道该属性存在于`person`对象中。

为此，我们使用了`as`操作符，正如我们在下面的代码中所做的那样:

使用`as`操作符，我们显式地告诉 TypeScript 编译器`person`属于`Person`类，这样我们就可以访问`Person`接口中的`name`属性。

# 类型谓词

为了检查对象的结构，我们可以使用类型谓词。类型谓词是一段代码，我们在其中检查给定的属性名是否有与之关联的值。

例如，我们可以编写一个新函数`isPerson`来检查一个对象是否具有`Person`类型的属性:

在上面的代码中，`isPerson`返回一个`person is Person`类型，这是我们的类型谓词。

如果我们像在上面的代码中那样使用该函数，那么如果一个联合类型由两个类型组成，TypeScript 编译器将自动缩小该类型的范围。

在`if (isPerson(person)){ ... }`块中，我们可以访问`Person`接口的任何成员。

但是，如果有两个以上的类型组成 union 类型，这就不起作用了。例如，如果我们有以下代码:

然后，TypeScript 编译器将拒绝编译代码，我们将得到以下错误消息:

```
Property 'employeeCode' does not exist on type 'Animal | Employee'.Property 'employeeCode' does not exist on type 'Animal'.(2339)
```

这是因为它不知道`else`子句的类型，因为它可以是`Animal`或`Employee`。为了解决这个问题，我们可以添加另一个`if`块来检查`Employee`类型，就像我们在下面的代码中做的那样:

![](img/e7aad765b48291cc2bcf003787fedda6.png)

[摄影师](https://unsplash.com/@desmondlau?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# In 运算符

另一种检查结构以确定数据类型的方法是使用`in`操作符。这就像 JavaScript `in`操作符，我们可以用它来检查一个属性是否存在于一个对象中。

例如，要检查一个对象是否是一个`Person`对象，我们可以编写以下代码:

在`getIdentifier`函数中，我们像在普通 JavaScript 代码中一样使用了`in`操作符。如果我们检查某个成员的名称，该名称对于某个类型来说是唯一的，那么 TypeScript 编译器将会推断出`if`块中`person`对象的类型，正如我们上面所做的那样。

既然`name`是一个只存在于`Person`接口中的属性，那么 TypeScript 编译器足够聪明，知道里面的任何东西都是一个`Person`对象。

同样，因为`employeeCode`只是`Employee`接口的成员，那么它知道里面的`person`对象是`Employee`类型的。

如果两种类型都被消除了，那么 TypeScript 编译器知道它是`Animal`，因为其他两种类型都被`if`语句消除了。

# 防护类型

为了确定具有由基本类型组成的联合类型的对象的类型，我们可以使用`typeof`操作符。

例如，如果我们有一个联合类型为`number | string | boolean`的变量，那么我们可以编写以下代码来确定它是数字、字符串还是布尔值。例如，如果我们写:

然后我们可以像在第一个`if`块中一样调用 number 方法，因为我们使用了`isNumber`函数来帮助 TypeScript 编译器确定`x`是否是一个数字。

同样，这也适用于第二个`if`块中的`isString`函数的字符串检查。

如果一个变量既不是数字也不是字符串，那么它被确定为布尔值，因为我们有一个数字、字符串和布尔值类型的并集。

`typeof`型防护罩可以用以下方式编写:

*   `typeof v === "typename"`
*   `typeof v !== "typename"`

其中`“typename”`可以是`"number"`、`"string"`、`"boolean"`或`"symbol"`。

# 防护类型的实例

`instanceof`类型保护可用于确定实例类型的类型。

这对于确定一个对象属于哪个子类型很有用，给定父类型派生的子类型。例如，我们可以在下面的代码中使用`instanceof`类型的保护:

在上面的代码中，我们有一个返回`Cat`或`Dog`对象的`getRandomAnimal`函数，所以它的返回类型是`Cat | Dog`。`Cat`和`Dog`都实现了`Animal`接口。

`instanceof`类型守护通过对象的构造函数来确定对象的类型，由于`Cat`和`Dog`构造函数的签名不同，所以可以通过比较构造函数的签名来确定类型。

如果两个类有相同的签名，`instanceof`类型保护也将有助于确定正确的类型。在`if (animal instanceof Cat) { ... }`块中，我们可以访问`Cat`实例的`age`成员。

同样，在 `if (animal instanceof Dog) {...}`块中，我们可以访问`Dog`实例独有的成员。

# 结论

通过各种类型保护和类型谓词，TypeScript 编译器可以使用条件语句缩小类型范围。

类型谓词由关键字`is`表示，就像`pet is Cat`一样，其中`pet`是变量而`Cat`是类型。我们还可以使用`typeof`类型保护来检查原始类型，使用`instanceof`类型保护来检查实例类型。

此外，我们让`in`操作符检查一个属性是否存在于一个对象中，这反过来通过属性的存在来确定对象的类型。