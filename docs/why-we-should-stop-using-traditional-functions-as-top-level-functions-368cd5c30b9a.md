# 你应该停止使用传统函数作为 JavaScript 的顶层函数

> 原文：<https://betterprogramming.pub/why-we-should-stop-using-traditional-functions-as-top-level-functions-368cd5c30b9a>

## 函数应该是嵌套的和特定的

![](img/8a05efe03712c15c67a49991f1f7f74f.png)

[路易斯·汉瑟](https://unsplash.com/@louishansel?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

在 JavaScript 中，我们用关键字`function`定义函数。这是 ES6 之前提供的选项。

在本文中，我们将看看为什么我们应该停止使用它们作为顶级函数。

# 为什么我们使用传统的 JavaScript 函数作为顶层函数？

我们通常使用传统函数，因为在 ES6 之前我们别无选择，我们不知道箭头函数的存在，或者我们在某些情况下需要不同的`this`(比如当我们定义构造函数时)。

然而，传统的功能令人困惑，因为有些功能开发者很容易忽略。

## 提升

传统的 JavaScript 函数在被定义为函数声明时会被提升。

例如，如果我们写:

```
console.log(calcArea(1, 3));function calcArea(width, height) {
  return width * height;
}
```

在上面的代码中，我们在定义`calcArea`之前调用了它，因为函数声明被放在了顶部。

另一方面，如果我们写:

```
console.log(calcArea(1,3));const calcArea = function(width, height) {
  return width * height;
}
```

然后我们得到一个错误，因为`calcArea`没有被提升到顶部。用不同的方式定义做不同事情的顶级函数是非常令人困惑的。

另一方面，箭头函数从来不会被提升，因为我们不能用函数声明来定义它们。它们只有在赋给变量或常量时才会被命名。

例如，我们必须写:

```
const calcArea = (width, height) => {
  return width * height;
}console.log(calcArea(1,3));
```

来调用`calcArea`函数。没有别的称呼了。

# 混淆这个值和混淆构造函数

传统函数内部的`this`值不同于其外部的`this`值。

`this`的值是在传统函数内部的函数。这对于创建传统函数很有用。

例如，我们可以写:

然后我们从`console.log`得到‘简·史密斯’,因为我们创建了一个`Person`的实例，其中`'Jane'`设置为`this.firstName`并且`'Smith'`设置为`this.lastName`。

对于习惯于用其他面向对象语言开发的程序员来说，我们可以从中创建实例的函数总是令人困惑。

能够在一个函数内部分配函数就更令人困惑了。

为了补救这一点，我们可以用 JavaScript 编写类，这是构造函数的语法糖。

我们可以通过将`Person`构造函数重写为:

这要清楚得多，因为它包含了程序员在一个类中习惯使用的东西，这是一个传入参数来实例化该类的`constructor`。它还有`fullName`方法，这是一个传统的函数，就像我们上面提到的一样。只是现在我们可以用速记来写了。

我们知道`this`是`Person`实例，不像`Person`的构造函数。

此外，我们可以毫不费力地扩展`Person`类:

在上面的代码中，我们创建了扩展了`Person`类的`Employee`类，如`extends`关键字所示。

然后，我们可以通过使用`Person`构造函数的参数调用`super`来调用`Employee`类`constructor`中的`Person`构造函数。

这也比构造函数清晰得多。要扩展构造函数，我们必须将子构造函数的原型设置为父构造函数的原型。我们还必须记住设置父构造函数的实例变量。如果我们忘记了，JavaScript 解释器不会警告我们。

另一方面，如果我们忘记在子类中调用`super`，我们将得到一个错误。

为了使使用传统函数更加混乱，我们可以使用传统函数自带的`call`、`apply`和`bind`方法来改变`this`的值。这造成了更多的问题，因为我们可能将其设置为错误的值。

# 参数对象

传统函数有`arguments`对象，这是一个类似数组的对象，包含我们传递给传统函数的所有值。

它具有欺骗性，因为它是一个类似数组的对象，而不是一个数组。`arguments`具有`length`属性，可以用循环迭代，但是所有的数组方法都丢失了。

ES6 或更高版本有`rest`操作符来获取超过我们传入的参数数量的参数。

例如，我们可以如下使用`rest`操作符:

```
const getRestArgs = (a, b, ...rest) => rest;
console.log(getRestArgs(1, 2, 3, 4, 5, 6));
```

`getRestArgs(1, 2, 3, 4, 5, 6)`将记录`[3, 4, 5, 6]`，因为前两个参数被设置为参数`a`和`b`。用`...`表示的`rest`操作符将把剩余的参数放到操作符后面的数组中。在这种情况下，`rest`就是那个数组。

箭头函数不绑定到`arguments`，所以如果我们不关心`this`的值，这是一个很好的选择。

# 结论

是时候停止使用传统函数作为顶层函数了。它们令人困惑，因为如果它们被定义为函数声明，它们可能会被提升。

另外，`this`令人困惑——尤其是当我们不想定义构造函数的时候。

如果我们想定义构造函数，我们应该使用类。它们可以从其他类继承，我们可以使用它们内部的传统函数作为方法。

如果我们想定义不是构造函数的顶级函数，那么我们应该使用箭头函数，因为我们不必担心`this`的值。

最后，既然我们有了 rest 操作符，那么`arguments`对象基本上是无用的。

因此，我们应该创建类或箭头函数来代替顶层的传统函数。