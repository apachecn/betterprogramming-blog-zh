# TypeScript 中的静态属性、抽象类和构造函数

> 原文：<https://betterprogramming.pub/introduction-to-typescript-classes-static-properties-abstract-classes-and-more-869f1eaa4835>

## 在 TypeScript 中改进您的类

![](img/8995773065518e1d2e0bc271e32f62f0.png)

照片由[克里斯蒂娜@ wocintechchat.com](https://unsplash.com/@wocintechchat?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/employee?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

与 JavaScript 一样，TypeScript 中的类是其原型继承模型的一种特殊语法，这是基于类的面向对象语言中的一种类似继承。类只是添加到 ES6 中的特殊函数，用来模仿其他语言中的关键字`class`。

在 JavaScript 中，我们可以有`class`声明和`class`表达式，因为它们只是函数。所以像所有其他函数一样，有函数声明和函数表达式。TypeScript 也是如此。类充当创建新对象的模板。TypeScript 扩展了 JavaScript 类的语法，然后添加了自己的特色。在本文中，我们将研究静态属性、抽象类和构造函数。

# 静态属性

使用 TypeScript，我们可以将成员指定为实例变量(前面没有关键字`static`)和静态成员(前面有关键字`static`)。

无需实例化类就可以访问静态成员。当然，这是为成员指定的访问修饰符。因此可以从类外部直接访问公共静态成员。私有静态成员只能在类中使用，受保护的成员可以由定义该成员的类访问，也可以由该类的子类访问。

字段和方法都可以使用关键字`static`。例如，我们可以在下面的代码中使用它:

```
class Person {
  static numPeople = 0;  
  constructor(name: string) {
    Person.numPeople++;
  }
  static getNumPeople() {
    return this.numPeople;
  }
}const john = new Person('John');
const jane = new Person('Jane');
console.log(Person.numPeople);
console.log(Person.getNumPeople());
```

在上面的代码中，我们每实例化一次`Person`类，静态属性`numPeople`就加 1。由于静态属性由类的所有实例共享，并且不属于任何一个实例，我们可以通过使用`Person.numPeople`直接访问`numPeople`。

同样，我们有一个静态方法`getNumPeople`，我们可以直接调用，而不需要实例化`Person`类。因此，当我们通过使用`Person.numPeople`获得`numPeople`并调用`Person.getNumPeople()`时，两者都返回值 2。

因为成员是静态的，所以值是类的一部分，而不是任何实例的一部分，所以即使实例被销毁，值也会被保留。这与实例变量不同，实例变量是从类内的`this`和类外的类实例变量中访问的。

# 抽象类

TypeScript 具有抽象类，这些类具有某个类的部分实现，并且可以从其中派生其他类。它们不能被直接实例化。

与接口不同，抽象类可以有其成员的实现细节。要声明一个抽象类，我们可以使用`abstract`关键字。我们还可以对方法使用`abstract`关键字来声明抽象方法，这些方法由从抽象类派生的类来实现。

抽象方法不包含方法的实现。由从抽象类派生的子类来实现列出的方法。它们还可以选择性地包括访问修饰符。

我们可以使用抽象类和方法，如下面的代码所示:

```
abstract class Person {
  name: string;
  age: number;
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  } abstract getName(): string;
  abstract getAge(): number;
}class Employee extends Person{
  constructor(name: string, age: number) {
    super(name, age);
  } getName() {
    return this.name;
  } getAge() {
    return this.age;
  }
}let employee = new Employee('Jane', 20);
console.log(employee.getName());
console.log(employee.getAge());
```

在上面的代码中，我们有抽象的`Person`类，它有抽象的方法`getName`和`getAge`。

正如我们所看到的，抽象方法中只有签名。方法的实际实现在`Employee`类中，它扩展了`Person`类。

我们在`Employee`类中实际实现了`getName`和`getAge`方法。

TypeScript 检查方法签名和类的返回类型，因此我们必须实现抽象类中概述的抽象方法。这意味着在上面的例子中，`getName`方法必须不带参数，并且必须返回一个字符串。

同样地，`getAge`方法必须不带参数，并且必须返回一个数字。抽象方法实现后，我们可以像调用其他方法一样正常调用它们。

![](img/d95e4a8cabf66250031421c17874bcba.png)

米切尔·奥尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 构造函数

当我们声明一个类型脚本时，我们同时声明了多个东西。我们声明了该类的一个实例，它是代码以关键字`class`开头的实体。例如，我们可以写:

```
class Employee{
  name: string;
  age: number;
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }}
let employee: Employee = new Employee('Jane', 20);
```

在上面代码片段的最后一行，我们使用`Employee`作为类`Employee`的实例类型。

此外，我们正在创建另一个值，我们称之为*构造函数。*这是在使用`new`关键字创建类的新实例时调用的函数。

如果我们将上面的类型脚本代码编译成 ES5 或更早的版本，我们可以看到生成了类似下面的代码:

```
"use strict";
var Employee = /** [@class](http://twitter.com/class) */ (function () {
    function Employee(name, age) {
        this.name = name;
        this.age = age;
    }
    return Employee;
}());
var employee = new Employee('Jane', 20);
```

在上面的代码中，构造函数是下面的代码:

```
function Employee(name, age) {
  this.name = name;
  this.age = age;
}
```

我们这样做是因为在 JavaScript 中，不管我们用的是什么版本，类只是构造函数的语法糖。TypeScript 的继承模型只是从 JavaScript 扩展而来。它没有改变 JavaScript 的继承模型，因为它应该与现有的 JavaScript 代码 100%兼容，所以可以采用现有的 JavaScript 代码来使用 TypeScript。

在 TypeScript 中，我们可以用`typeof`操作符获得构造函数的类型。它不同于它在 JavaScript 中的用法，因为它被扩展为获取类的构造函数的类型。如果我们运行下面的代码…

```
class Employee{
  name: string;
  age: number;
  static companyName: string = 'ABC Company';
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }}let employeeConstructor: typeof Employee = Employee;
console.log(Employee.companyName);
console.log(employeeConstructor.companyName);
```

…当我们获得两个`console.log`语句中记录的静态成员`companyName`的值时，这是有意义的。从编译后的输出可以看出…

```
"use strict";
var Employee = /** [@class](http://twitter.com/class) */ (function () {
    function Employee(name, age) {
        this.name = name;
        this.age = age;
    }
    Employee.companyName = 'ABC Company';
    return Employee;
}());
var employeeConstructor = Employee;
console.log(Employee.companyName);
console.log(employeeConstructor.companyName);
```

JavaScript 和 TypeScript 类最终只是函数。类语法使 JavaScript 的继承更容易使用，因为它看起来像是使用基于类的继承，但它实际上是原型继承模型之上的语法糖，自 JavaScript 首次问世以来就一直如此。

TypeScript 通过让我们定义抽象类使继承变得容易——其中一些实现由抽象类完成，其他实现在扩展抽象类的子类中完成。

我们也有子类可以实现的抽象方法。抽象方法只有签名和返回类型，没有实现细节。

静态成员让我们定义属于类的成员，而不是类的实例。

同样重要的是要知道，类语法最终是从 JavaScript 开始就存在的原型继承模型的语法糖。然而，现在我们可以把它放在一边，因为我们可以使用语法糖来使它更容易理解和实现继承。