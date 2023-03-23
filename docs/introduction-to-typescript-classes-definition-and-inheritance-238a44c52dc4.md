# 类型脚本类简介

> 原文：<https://betterprogramming.pub/introduction-to-typescript-classes-definition-and-inheritance-238a44c52dc4>

## 定义和继承

![](img/f834f584627f73b9f36053a00c761868.png)

里卡多·戈麦斯·安吉尔在 [Unsplash](https://unsplash.com/s/photos/patterns?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

与 JavaScript 一样，TypeScript 中的类是其原型继承模型的特殊语法，这与基于类的面向对象语言中的继承类似。类只是添加到 ES6 中的特殊函数，用来模仿其他语言中的关键字`class`。

在 JavaScript 中，我们可以有`class`声明和`class`表达式，因为它们只是函数。所以，像所有其他函数一样，有函数声明和函数表达式。TypeScript 也是如此。类充当创建新对象的模板。TypeScript 扩展了 JavaScript 类的语法，然后添加了自己的变体。

在本文中，我们将研究如何定义 TypeScript 类以及它们如何相互继承。除了我们可以向方法的成员和参数添加类型指定之外，这个主题与 JavaScript 基本相同。

# 定义类别

为了声明一个类，我们使用了`class`关键字。例如，要声明一个简单的类，我们可以写:

类声明没有被提升，所以在代码中定义之前不能使用它们 TypeScript 编译器不会自动将它们提升到顶部。另外，请注意，我们向成员添加了一些类型，以确保我们为每个成员分配了所需类型的值。因此，在代码中定义之前，上面的类不会工作，如下所示:

如果我们运行上面的代码，我们将得到一个`ReferenceError`。

我们也可以通过一个类表达式来定义一个类，这是另一种语法。它们可以是命名的，也可以是未命名的。我们也可以像处理函数一样，给变量分配一个类。如果我们这样做了，我们可以通过它的名字来引用这个类。例如，我们可以定义:

要获得上面未命名的类的名称，我们可以用`name`属性获得名称，如下所示:

```
console.log(Person.name);
```

我们也可以像这样定义一个命名类:

为了获得类名，我们可以再次使用`name`属性。

所以，如果我们写`console.log(Person.name)`，我们会记录`Person2`。

类体是用花括号定义的。我们在括号内定义类成员。类的主体在严格模式下执行。在严格模式下定义的一切都适用于类的定义，所以我们不能像`var`、`let`或`const`那样定义前面没有关键字的变量。当您定义一个类时，许多其他规则也适用。

TypeScript 中的类也有一个`constructor`方法，让我们在用`class`实例化对象时设置字段。每个类中只能有一个`constructor`方法。如果不止一个，就会抛出一个`SyntaxError`。如果类扩展了父类，那么`constructor`也可以调用`super`方法来调用`super`类的`constructor`。

未声明的方法`static`构成了该类的原型方法。它们在使用`new`关键字创建一个对象后被调用。例如，下面的类只有原型方法:

在上面的`Person`类中，`fullName`和`sayHi`是原型方法。它们被这样称呼:

```
const person = new Person('Jane', 'Smith');
person.fullName() // 'Jane Smith'
```

静态方法是不用使用`new`关键字从类中创建对象就可以调用的方法。例如，我们可以有这样的东西:

我们可以调用`personCount`函数而不使用`new`关键字来创建类的实例。所以，如果我们写`Person.personCount`，我们会得到`3`的返回。

原型方法中的`this`值将是对象的值。对于静态方法，`this`的值将静态方法所在的类作为值。

![](img/6c4c52023e8cbdd836ff2affca2d730b.png)

詹妮弗·哈奈特-亨德森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 类型脚本继承

在 TypeScript 中，我们可以创建一些类，这些类的属性可以包含在子类的属性中。它的工作方式与 JavaScript 相同。

因此，我们可以有一个包含所有子类共有的属性的高级类，并且子类可以有自己的特殊属性，这些属性不在任何其他类中。

例如，如果我们有一个具有公共属性和方法的`Animal`类，比如`name`和`eat`方法，那么`Bird`类可以继承`Animal`类中的公共属性。它们不必在`Bird`类中再次定义。

我们可以编写以下代码来实现 TypeScript 中的继承:

在上面的例子中，我们有父类`Animal`，它有`eat`方法，来自`Animal`的`extends`的所有类都会有，所以它们不必再定义`eat`。

我们有扩展了`Animal`类的`Bird`类。注意，在`Bird`类的`constructor`中，`super()`函数调用父类的构造函数，填充父类和子类的属性。

类不能扩展常规对象，常规对象不能用关键字`new`构造。如果我们想从一个常规对象继承，我们必须使用`Object.setPrototypeOf`函数来设置一个从常规对象继承的类。例如:

如果我们运行上面的代码，我们可以看到`Bob eats`和`Joe eats`被记录——因为我们已经从`Animal`对象继承了`eat`函数。正如您所看到的，我们必须放弃 TypeScript 的类型检查特性，通过一个类继承一个对象。如果我们决定采用 TypeScript，我们可能应该避免使用它。

类充当创建新对象的模板。TypeScript 扩展了 JavaScript 类的语法，然后添加了自己的变体。TypeScript 中定义类的方式与 JavaScript 相同。此外，TypeScript 类之间在如何相互继承或如何从类创建新对象方面没有区别。