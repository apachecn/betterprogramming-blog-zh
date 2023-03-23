# 如何用 TypeScript 或 JavaScript 编写异步类构造函数

> 原文：<https://betterprogramming.pub/how-to-write-an-async-class-constructor-in-typescript-javascript-7d7e8325c35e>

## 不使用常见的“init”或“builder”模式

![](img/602744d85e07165bc8af468cdd5eb7ea.png)

照片由[朱利安·迪克](https://unsplash.com/@juliandik?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](/s/photos/lisbon?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 创建类对象:通常建议的方法

`init`和`builder`是我最常看到的推荐用于创建需要异步进程的类对象的两种方法。

## 实例初始化()

```
class MyClass { constructor() {
    // set props normally
    // nothing async can go here
  } public async init() {
    // do your async steps here
  }
}
```

在`init`方法中，您定义了一个`async`实例方法，在对象实例化之后，作为附加步骤执行所有的`async`设置。

```
const myClassInstance = new MyClass()// need to remember to call this on the instance
await myClassInstance.init()
```

我对这种方法的问题是，您必须相信创建类的调用者记得在创建类的实例时调用`init()`方法。这显然是容易出错的，并且要求调用者有关于类的上下文。

# 类对象生成器

```
class MyClass { constructor() {
    // set props normally
    // nothing async can go here
  } public static async build(): Promise<MyClass> {
    // do your async stuff here
    // now instantiate and return a class
    return new MyClass()
  }
}
```

类对象构建器方法稍微好一点。这种方法在其他语言中也作为一种变通方法使用。它包括在类对象上编写一个静态方法，该方法在同步实例化类对象并返回它之前执行异步处理。

```
const myClassInstance = await MyClass.build()
```

与前一种方法相比，我更喜欢这种方法，因为它减轻了调用者记住调用额外的`init()`函数的负担。然而，调用者仍然需要有上下文来使用非标准的静态类方法来实例化类，而不是标准的`new MyClass()`语法。没有办法使构造函数私有，或者确保避免这种容易犯的错误。编辑:这对于 Javascript 仍然适用，但是 Typescript 2.x *确实为类构造函数方法提供了私有/受保护的 decorators，这将强制只有“build”方法可以实例化该类。将这一点添加到使用 Typescript 而不是 Javascript 的长长的理由列表中；)*

# 功能选项模式

我以前写过如何使用 functional options 模式来干净地构造类:

[](https://medium.com/swlh/using-a-golang-pattern-to-write-better-typescript-58044b56b26c) [## 使用 Golang 模式编写更好的打字稿

### 如何使用函数选项模式来构造更好的类定义

medium.com](https://medium.com/swlh/using-a-golang-pattern-to-write-better-typescript-58044b56b26c) 

我意识到，通过使用这种模式来构造类，您已经使构造在创建时需要异步处理的类变得简单了，而不需要任何额外的东西，比如在创建对象后记得调用一个`init()`函数。

## 异步选项构造函数

下面是我以前的`House`示例类的扩展，我用来通过函数选项演示构造。如您所见，异步创建所需要的只是创建一个异步选项构造函数:

允许类的函数选项构造的类定义

施工容易！

采用 functional options 模式来构造类还有[其他的好处](https://medium.com/swlh/using-a-golang-pattern-to-write-better-typescript-58044b56b26c)，但是特别地，它允许创建一个可能需要异步过程的类对象。异步函数调用可以直接添加到类实例化步骤中，不需要单独的`init()`调用，也不需要修改已经建立的类构造方法。调用者可以使用标准的`new House()`语法来实例化类，并使用您建立的静态类构造器模式来构建他们的对象实例，而不需要太多关于类实现的额外上下文。

编码快乐！