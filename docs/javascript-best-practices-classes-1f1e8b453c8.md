# JavaScript 最佳实践—类

> 原文：<https://betterprogramming.pub/javascript-best-practices-classes-1f1e8b453c8>

## 使用默认参数和属性快捷键，清理 JavaScript 代码很容易

![](img/eabd0f7a1738a4620570d45e7855af40.png)

金伯利农民在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

使用默认参数和属性缩写，清理我们的 JavaScript 代码很容易。

在本文中，我们将研究创建类的最佳实践。

# ES6 课程基础

为了使 JavaScript 的原型继承模型不那么混乱，ES6 中引入了类语法。

它有`constructor`、实例变量和方法，就像 Java 等语言中的类一样，但是它们的行为不同。

例如，我们可以通过编写以下代码来创建一个类:

我们有了`name`实例变量和`speak`方法。`this`是类本身的实例。

# Getters 和 Setters

Getters 是只返回值的方法，而 setters 是让我们为实例变量设置值的方法。

它们让我们以一种受控的方式来做，而不是直接访问实例变量。

我们还可以向类中添加 getters 和 setters，如下所示:

有了这些 getters 和 setters，我们可以编写下面的代码来获取`name`:

```
const person = new Person('joe');
console.log(person.name);
```

并设置`name`:

```
const person = new Person('joe');
person.name = 'jane';
console.log(person.name);
```

# 子或子类

我们也可以用关键字`extends`创建子类。例如，我们可以写:

我们创建了一个`Dog`类，它覆盖了`Animal`类的`speak`方法。

同样，我们调用`Dog`的`constructor`中带有`super`的父构造函数，调用`super.speak`来调用`Animal`的`speak`方法。

这比旧的继承构造函数语法要干净得多。

# 静态方法和属性

我们可以通过使用`static`关键字给一个类添加静态属性。

这样做将有助于我们向它添加一个由所有实例共享的属性。

例如，我们可以写:

```
class Dog {
  static type() {
    return 'dog';
  }
}
```

那么我们可以把它作为静态方法调用如下:

```
console.log(Dog.type())
```

为了创建静态属性，我们编写以下代码:

```
class Dog {}
Dog.type = 'dog';console.log(Dog.type);
```

我们将`type`属性设置为`'dog'`，它在所有实例中共享。

这种语法是有效的，因为类只是函数，函数和 JavaScript 中的其他对象一样。

# 私有字段

JavaScript 的未来版本也可以让我们定义私有类字段。在构造函数中没有类似的东西。

它们由字段名前的`#`符号表示。

# 结论

我们可以用类语法定义构造函数，使它们看起来比构造函数更容易理解。

这些类看起来类似于 Java 类，但本质上行为非常不同。在我们使用它之前，我们必须理解其潜在的行为。这意味着我们应该在使用 JavaScript 类之前理解原型继承。