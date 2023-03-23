# 创建对象的 JavaScript 最佳实践

> 原文：<https://betterprogramming.pub/javascript-best-practices-for-writing-more-robust-code-creating-objects-428e55be6fd4>

## 工厂功能、扩展原型等等

![](img/55d7034fe034c8887ee02dcfb2274c15.png)

[郭锦恩](https://unsplash.com/@spacexuan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

JavaScript 是一种容易学习的编程语言。编写运行并执行某些操作的程序很容易。然而，很难考虑所有用例并编写健壮的 JavaScript 代码。

在本文中，我们将研究一些编写健壮 JavaScript 代码的最佳实践。

# 使用工厂功能

工厂函数是返回构造函数或类的新实例的函数。

我们可以使用它们来创建对象，而不用编写任何带有`new`关键字的代码来实例化类或构造函数。

构造函数经常是 JavaScript 中令人困惑的部分，如果我们愿意，我们肯定可以避免它。

例如，我们可以让自己的工厂运行如下:

在上面的代码中，我们有一个`createPerson`工厂函数，它接受一个`firstName`和`lastName`参数，并使用`firstName`、`lastName`和`fullName`方法返回一个对象。

我们使用了一个箭头函数，这样我们就不会与返回对象中的值`this`混淆。由于箭头函数没有绑定到`this`，我们知道`fullName`中`this`的值就是返回的对象。

工厂函数可以用任何东西返回对象，所以可能性是无穷的。

要使用我们的`createPerson`工厂函数，我们可以编写以下代码:

```
const person = createPerson('Jane', 'Smith');
const fullName = person.fullName();
```

我们用`createPerson`函数创建了一个`person`对象，然后在其上调用了`fullName`方法。

然后我们得到`fullName`是‘简·史密斯’，因为`this`引用了`person`对象。

很多 JavaScript 函数像`Number`和`Boolean`都是工厂函数。它们接受任何对象作为参数，然后分别返回一个数字或布尔值。

这使得我们的 JavaScript 代码更加健壮，因为我们不必担心类或构造函数实例。我们只需要考虑对象和组成函数。

组合函数使代码更具可重用性和可测试性，从而创建更健壮的代码。

# 通过附加到 prototype 属性为构造函数创建实例方法

当我们想要创建构造函数的实例方法时，我们应该将这些方法附加到构造函数的`prototype`属性上。

这样，当我们实例化构造函数时，我们也可以调用实例上的方法。

直接附加到函数的方法是所有方法共享的静态方法。

例如，我们可以将实例方法添加到构造函数中，方法是将它附加到它的`prototype`属性，如下所示:

在上面的代码中，我们创建了一个返回`Fruit`实例的`Fruit`构造函数。然后我们通过编写来添加`grow`实例方法:

```
Fruit.prototype.grow = function() {
  console.log(`${this.name} grew.`);
}
```

那么当我们如下实例化它时:

```
const fruit = new Fruit('apple');
fruit.grow();
```

实例方法将方法封装在构造函数实例中，防止它暴露给外部代码，并允许它进行意外的更改。

因此，这使得我们的代码更加健壮。此外，每个构造函数都有自己的职责，因为它有自己的实例方法。这可以防止多个构造函数对同一个对象做不同的事情。

备选方案(如上)为每个实例创建一个`grow`实例方法的副本。该方法没有被缓存，所以以这种方式使用 attach instance 方法会比较慢。

# 使用。类型属性来创建可以创建多种对象类型的工厂函数

因为工厂函数可以被组合，所以我们可以创建一个可以创建多种类型对象的工厂函数。

我们可以用`type`属性来区分它们，这是一种区分我们想要创建的不同类型对象的传统方法。

为此，我们可以编写以下代码:

在上面的代码中，我们有`createPet`工厂函数，它调用`createDog`或`createCat`工厂函数，这取决于我们传递给`createPet`的是哪种宠物`type`。

我们还通过调用`createPet`工厂函数内部的工厂函数来组合工厂函数。

现在我们只需要担心一个工厂函数(`createPet`)，而不是使用多个工厂函数来创建不同种类的对象。这隐藏了代码下面的复杂性，因此当我们进行更改时，破坏东西的风险更小。

# 结论

使用工厂函数，我们可以创建它们来创建新的对象。我们也可以组合它们，并在一个工厂函数中调用不同的函数来做不同的事情。

当我们有一个构造函数时，我们应该将实例方法附加到构造函数的`prototype`属性上，这样就不必一直重新创建它们。