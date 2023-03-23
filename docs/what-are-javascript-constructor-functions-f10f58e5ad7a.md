# 什么是 JavaScript 构造函数？

> 原文：<https://betterprogramming.pub/what-are-javascript-constructor-functions-f10f58e5ad7a>

## 了解返回函数的函数

![](img/dc91da68bd9d69570aebf4df206c7d13.png)

[Kelly Sikkema](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在 JavaScript 中，函数可以用作创建其他对象的模板。这些函数称为构造函数，具有不同于常规函数的特殊属性。

# 定义构造函数

可以用关键字`new`调用它们，从构造函数中构造一个对象的新实例。

此外，它们不同于常规函数，因为它们的名称应该以大写字母开头。

构造函数的一个简单示例如下:

```
function Person(name, age) {
  this.name = name;
  this.age = age;
}
```

值得注意的是，我们使用了`function`关键字来定义构造函数。我们需要使用它，因为箭头函数不能用来定义构造函数。

`this`是一个对象，拥有由`new`关键字创建的对象实例的数据。

有了这个函数，我们可以通过编写以下内容来创建一个新的`Person`对象:

```
let person = new Person('Joe', 10);
```

在`Person`函数中，`this.name`和`this.age`是使用`new`操作符创建的对象实例的属性。

`person`对象的`this.name`的值为`'Joe'`，而`this.age`的值为 10。

如果我们通过编写……来创建另一个`Person`对象的实例

```
let jane = new Person('Jane', 11);
```

…那么`jane` 对象的`this.name`将具有值`'Jane'`，而`this.age`将具有值 11。

`Person`构造函数的每个实例都有自己的属性值。

# 构造函数中的方法

为了让用构造函数创建的对象实例做一些事情，我们可以向它添加方法。

我们可以通过编写以下代码在构造函数中定义方法:

```
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.greet = function(){
   return `Hi ${this.name}`;
  }
}
```

在上面的代码中，`this.greet`属性是一个`Person`实例的方法。所以如果我们用构造函数创建一个新的`Person`对象，并如下调用`greet`

```
let person = new Person('Joe', 10);
console.log(person.greet());
```

…然后我们从`console.log`输出中得到`'Hi Joe'`。

注意我们使用了`function`关键字来定义方法。我们必须用它来获取`Person`构造函数的实例作为`this`的值。

如果我们创建另一个`Person`对象的实例，如下…

```
let jane = new Person('Jane', 11);
```

…当我们如下调用`jane`上的`greet`方法时…

```
console.log(jane.greet());
```

…然后我们从`console.log`输出中得到`'Hi Jane’`。

# 构造器模式测试

不使用`new`关键字很容易错误地调用构造函数。我们可以通过使用`new.target`属性来检查我们是否使用了`new`关键字来调用构造函数。

例如，假设我们有`Person`构造函数…

```
function Person(name, age) {
  this.name = name;
  this.age = age;  
}
```

…然后我们可以记录`new.target`属性，如下所示:

```
function Person(name, age) {
  this.name = name;
  this.age = age;
  console.log(new.target);
}
```

然后，一旦我们用`new`操作符创建了一个新的`Person`，如下…

```
let person = new Person('Joe', 10);
```

…我们记录了函数的代码，如下所示:

```
ƒ Person(name, age) {
  this.name = name;
  this.age = age;
  console.log(new.target);
}
```

如果我们调用`Person`函数而不使用`new`运算符，如下…

```
Person('Joe', 10);
```

…我们得到从`console.log`返回的`undefined`。

因此，如果我们想检查构造函数是否被`new`操作符调用，我们可以检查`new.target`属性。

![](img/0ff78784c30906a0613a43174aa020ef.png)

亚历山大·席默克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 从构造函数返回

就像常规函数一样，我们可以在构造函数中有一个`return`语句。

然而，与常规函数不同，有一些规则控制构造函数中返回的内容。

如果用对象调用`return`，则返回对象而不是`this`。当我们用`new`操作符创建一个新的对象实例时，我们得到的是返回的对象，而不是`this`的值。

如果用原始对象调用`return`，那么它将被忽略。

在所有其他情况下，即使没有指定任何内容，也会返回`this`。

我们可以通过编写以下代码来返回一个除了`this`之外的对象:

```
function Person(name, age) {
  this.name = name;
  this.age = age;
  return {
    name: 'Mary'
  };
}
```

然后，当我们用关键字`new`创建一个`Person`的实例时，如下…

```
let person = new Person('Joe', 10);
console.log(person.name);
```

…我们让`'Mary'`登录。

# 省略括号

如果不需要传入任何参数，我们可以在调用构造函数时省略括号。例如，如果我们写…

```
function Employee() {
  this.name = "John";
}let employee = new Employee;
console.log(employee.name);
```

…我们从`console.log`输出得到`'John'`。

# 检查对象是否是构造函数的实例

我们可以通过使用`instanceof`操作符来检查一个对象是否是一个构造函数的实例。

例如，假设我们有一个`Person`构造函数和一个从它创建的`person`对象，如下…

```
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.greet = function(){
   return `Hi ${this.name}`;
  }
}let person = new Person('Joe', 10);
```

…我们可以使用如下的`instanceof`操作符来检查`person`是否是从`Person`构造函数创建的:

```
console.log(person instanceof Person);
```

上面的`console.log`应该显示`true`。如果一个对象不是从给定的构造函数中创建的，那么将会是`false`。

例如，如果我们有…

```
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.greet = function(){
   return `Hi ${this.name}`;
  }
}function Foo(){}let person = new Person('Joe', 10);
console.log(person instanceof Foo);
```

…然后`console.log`显示`false`，因为`person`不是从`Foo`构造函数创建的。

构造函数是创建对象的模板。我们可以用它来创建使用相同构造函数的不同对象，这些构造函数具有相同的实例方法和属性，但非方法属性的值不同。

我们可以使用`instanceof`操作符来检查一个对象是否是从构造函数中创建的。