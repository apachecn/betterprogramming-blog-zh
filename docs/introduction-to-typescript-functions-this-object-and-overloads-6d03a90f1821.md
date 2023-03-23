# TypeScript 函数:“this”对象和重载

> 原文：<https://betterprogramming.pub/introduction-to-typescript-functions-this-object-and-overloads-6d03a90f1821>

## 看看 JavaScript 常量“this”以及如何在 TypeScript 中使用它

![](img/2506c1dab5dcc66fe16316c9a2ac4580.png)

由 [Marvin Meyer](https://unsplash.com/@marvelous?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/tech?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

函数是一些小的代码块，它们接受一个输入，或者返回一个输出，或者有副作用，这意味着`if`在函数之外修改变量。我们需要函数将代码组织成可重用的小块。如果没有函数，如果我们想重新运行一段代码，我们必须将它复制到不同的地方。函数对于任何类型脚本程序都是至关重要的。在本系列的这一部分中，我们继续研究 TypeScript 函数的不同部分，包括如何用 TypeScript 处理`this`对象，以及重载函数。

# 这个物体

如果`this`在用`function`关键字声明的常规函数中被引用，那么`this`对象不会被设置在包含`this`的函数的箭头函数中。如果一个箭头函数在一个构造函数中，那么`this`被设置为新的对象。如果它不在对象内部，那么箭头函数内部的`this`在严格模式下就是`undefined`。`this`如果箭头功能在对象内部，将被设置为基础对象。然而，如果我们在一个箭头函数中引用`this`，我们总是得到`window` 对象。例如，下面的代码无法编译和运行，因为 TypeScript 不允许我们的代码将全局变量作为`this`的值:

```
const fn = () => this
console.log(fn());
```

当`console.log`运行时，我们记录了`window`对象。同样，如果我们运行这个:

```
let obj = {}
obj.f = () => {
  return this;
};
console.log(obj.f());
```

我们得到和以前一样的东西。这与用`function`关键字声明的传统函数形成对比。让我们看看如果我们用上面代码中的传统函数替换上面的函数会发生什么，如下面的代码所示:

```
const fn = function() {
  return this
};
console.log(fn);
```

现在，如果在我们的`tsconfig.json`中有`noImplicitThis`标志，我们会从 TypeScript 编译器那里得到下面的错误:`'this' implicitly has type ‘any’ because it does not have a type annotation.(2683)`

我们离让代码工作更近了，但它仍然无法编译。要修复这个错误，我们需要在函数签名中放一个假参数`this: void`，如下面的代码所示:

```
const fn = function(this: void) {
    return this;
};
console.log(fn);
```

用上面的代码，我们使`this`变量不可用。如果我们想用它做点什么，我们可以为`this`而不是`void`添加一个显式类型，让它做点什么。例如，如果我们想创建一个构造函数对象，我们可以编写类似下面的代码:

在上面的代码中，我们创建了一个名为`Person`的接口，向`person`对象的`greet`函数中的`this`对象添加数据类型。当我们调用`greet`函数时，`this`参数被忽略，因为我们将它作为第一个参数。TypeScript 只查看参数中`this`的类型，不会期望我们通过传入`this`的参数来调用`greet`函数。在定义了`person`对象之后，我们就可以给它外部的`name`和`age`属性赋值了。当我们定义`person`对象时，我们已经满足了接口中列出的要求，但是我们还应该给它分配一些有意义的值，这样我们就可以使用`greet`函数。

然后，当我们运行上面例子最后一行中的`console.log`，我们得到`Hi Jane. You’re 20 years old`。我们已经成功地为`this`创建了一个数据类型，所以对于`this`的值不会有任何歧义。这有助于开发人员理解`this`在代码中有什么，因为这是 JavaScript 中比较令人困惑的方面之一。在普通 JavaScript 中，`this`可以根据`this`关键字在代码中的位置取不同的值。在传统函数中，`this`的值就是函数本身。箭头函数不会改变`this`的值，所以无论它在外面还是在箭头函数里面都是一样的。

使用 TypeScript，我们不能使用`this`和传统函数来创建类。例如，如果我们写:

然后我们会得到错误`Cannot find name ‘Person’. Did you mean ‘person’?(2552)`并且代码不会被编译。TypeScript 不允许我们使用传统的函数作为类。要使用 make 类，我们必须使用`class`语法。

![](img/01178c7f862619e06d579de6f8a5e70b.png)

[Icons8 团队](https://unsplash.com/@icons8?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 回调中的“this”参数

对于用于事件侦听器的回调函数，我们传入的回调函数应该在接口中键入。例如，为了设置自定义输入控件组件的类型，我们可以编写如下代码:

```
interface InputElement {
  addKeyUpListener(onclick: (this: void, e: Event) => void): void;
}
```

然后，当人们使用这个控件时，他们可以写这样的东西让它运行:

如果使用该库的开发人员试图引用`this`，如下面的代码所示，该怎么办？：

在这种情况下，TypeScript 编译器将抛出一个错误，因为在`InputElement`接口中`this`被标记为`void`类型。

# 函数重载

重载一个函数就是用相同的名字创建不同签名的函数。这在 JavaScript 中是不允许的，因为函数是对象，我们不能多次重复声明同一个对象。然而，由于 TypeScript 是强类型的，这与 JavaScript 的动态性质相反，因此它必须找到一种方法来适应 JavaScript 的动态方面。为此，TypeScript 为我们提供了一种用不同的签名重载函数的方法。要用 TypeScript 重载函数，我们只需在用相同的名称定义实际函数之前，用相同的名称编写多个函数签名。例如，我们可以这样写:

```
function getPerson(person: { name: string, age: number }): { name: string, age: number };
function getPerson(person: { name: string }): { name: string };
function getPerson(person: any): any {
  return person;
}
```

有了上面的代码，我们有了一个`getPerson`函数，它可以接受一个具有属性`name`和`age`的对象，或者只接受属性`name`。冒号后面的返回类型可以是同时具有`name`和`age`属性的对象，也可以是只有`name`属性的对象。这就是我们在上面的代码示例的前三行中所拥有的，在这里我们只是为我们的`getPerson`函数定义了我们想要的签名。

然后在实际的`getPerson`函数定义中，我们有了实际的函数定义。我们将参数和返回的类型标注为`any`，这是可以的，因为我们已经定义了`getPerson`分别接受和返回的参数和返回类型。我们可以调用`getPerson`，如下面的代码所示:

```
console.log(getPerson({ name: 'Jane', age: 20 }));
console.log(getPerson({ name: 'Jane' }));
```

从上面的`console.log`陈述中，我们得到:

```
{name: "Jane", age: 20}
{name: "Jane"}
```

如果我们试图用一个我们没有在签名中定义的参数调用它，比如:`console.log(getPerson({}))`我们会得到一个`No overload matches this call`错误。

在传统函数中处理`this`对象的主要方式是将它作为函数的第一个参数传入，然后通过为该类型定义一个接口来为其设置一个类型。TypeScript 将忽略`this`参数，并将其视为不存在。仅用于设置`this`的数据类型。我们可以在 TypeScript 中重载函数，这在 JavaScript 中是不允许的。为此，我们只需为函数定义不同的签名并赋予它们相同的名称，然后在定义了函数将接受的签名后，用相同的名称定义函数。