# iOS 工程师对坚实原则的看法

> 原文：<https://betterprogramming.pub/an-ios-engineers-perspective-on-solid-principles-bf46ddc25d47>

## iOS 和 Swift 世界的可靠程度如何

![](img/28de51b4f5ec439fc19df3de8755ecc5.png)

图片来源:作者

SOLID 由五个设计原则组成，旨在使代码更易于理解、灵活和维护。根据维基百科，该原则最早出现在罗伯特·马丁 2000 年的一篇名为“设计原则和设计模式”的论文中，但缩写词 SOLID 是由迈克尔·费瑟斯引入的。

我希望当你读到这篇文章时，你真的想成为一名更好的工程师或者写出更好的代码。当工程师编写代码时，我们应该考虑代码的可伸缩性，以及代码是否可供不同的工程师阅读。如果我们不这样做，我们就有一个大问题——我们需要通过遵循坚实的原则来解决这个问题。

# 原则 1:单一责任

## 每个类应该只有一个责任

通过查看代码行，我们可能以某种方式测量出类中实际存在多少职责。因此，我从不同的来源选取了不同的例子，以更好地帮助我们衡量我们应该有多少行代码。

*   根据 Code Complete，1996 年的一项研究发现，例程越多的类缺陷越多。
*   根据 Ben Nadel 的“[对象健身操](http://www.bennadel.com/resources/uploads/2012/ObjectCalisthenics.pdf)”，超过 50 行的类通常做不止一件事，这使得它们更难理解和重用。50 行的类有一个额外的好处，就是可以在一个屏幕上看到，不需要滚动，这使得它们更容易快速掌握。
*   根据关于[栈交换](https://softwareengineering.stackexchange.com/questions/66523/how-many-lines-per-class-is-too-many-in-java)的一个讨论，200 线是一个很好的指导方针。
*   SwiftLint 是一个通过使用默认配置来帮助实施 Swift 风格的工具，它认为一个类的代码应该少于 500 行。(我知道您可以更改这些设置的配置。)

至于你的项目中应该有多少行代码，我将留给你自己的权限，因为我相信我们最了解我们自己的项目。我相信我们应该知道我们的代码有多长，但是我绝对喜欢将它保持在 200 行以内的想法。

根据我们拥有的代码行来判断，我们基本上可以测量我们在我们的类中有多少责任，并且可能将它们分成不同的类。

## 代码示例

让我们看看做这件事的不好的方法，然后看看我们可以做得更好。

```
// You have two responsiblities in this classstruct Person {let name: Stringlet age: Intfunc checkAge() -> String { if age < 8 { return "Underage" } else { return "Qualified" } }} let person = Person(name: "Kelvin", age: 27)person.checkAge()
```

我希望你明白这里划分责任的意义。是的，如果我们打算走这条路，我们可能会有很多文件，但让我们想想我们的代码将如何组织，随着时间的推移它将如何容易维护。

```
// One responsibility in one classstruct Person { let name: String let age: Int}// another responsibility in another classstruct AgeVerifier { func checkAge(age: Int) -> String { if age < 8 { return "Underage" } else { return "Qualified" } }}let person = Person(name: "Lexton", age: 1)let verifyAge = AgeVerifier()verifyAge.checkAge(age: person.age)
```

# 原则 2:开放-封闭

## *类应该为扩展而打开，为修改而关闭*

这个原则通过两个重要的特征促进了可维护代码的编写:

*   开放供扩展
*   关闭以进行修改

简而言之，你应该能够很容易地向你的类添加几乎任何新的特性，而不必重新构造你的整个类，因为我们都知道进行重新构造会导致额外的开发和测试时间。

我敢肯定，作为软件工程师，我们经常被提醒要构建一个以客户为中心的应用程序，这导致我们的产品经理在最后一刻推出大量新功能。作为一种预防措施，请始终编写可扩展的代码来实现一些附加功能。

让我们用一个非常简单的代码示例来说明这一点。想象一下，你有一个动物园，目前你的动物园里只有一只老虎。我们必须现实一点——只有一只老虎是赚不到钱的。你需要更多的动物，所以我们需要建造一个允许我们在任何时候添加任何动物的动物园。

## 代码示例

```
protocol AnimalProtocol {func makeSound() -> String}class Tiger: AnimalProtocol { func makeSound() -> String { return "Roar" }}struct Zoo {let animals: [AnimalProtocol] func animalNoise() -> [String] { return animals.map { $0.makeSound() } }}let tiger = Tiger()var zooAnimals = Zoo(animals: [tiger])zooAnimals.animalNoise() // [roar]
```

我们终于有了一只新的动物，我们想把它加入动物园。简单！只需创建一个新类，并将其添加到动物园中。我们准备好了。也许在将来，当我们有更多的动物时，我们可以做同样的事情。

请注意，我没有修改上面的大部分代码，但它仍然能够扩展功能。这就是所谓的*可扩展性。*

```
class Horse: AnimalProtocol { func makeSound() -> String { return “Neigh” }}let horse = Horse()zooAnimals = Zoo(animals: [tiger, horse])zooAnimals.animalNoise() // [roar, neigh]
```

# 原则 3:利斯科夫替代原则

## *子类不应该破坏父类的类型定义*

子类应该能够以不破坏基类功能的方式覆盖父类方法。这个原则的好处是，当代码可互换时，它变得更加可重用。

请注意，如果一个 override 方法什么都不做或者只是抛出一个异常，那么您就违反了 Liskov 替换原则。

## 代码示例

```
class Bird { func makeNoise() { print(“Chirp chirp”) }}class Eagle: Bird { override func makeNoise() { print(“Almight eagle roar”) }}// This is where it violates the Liskov Substitution as doing so will break the parent classclass Crow: Bird { override func makeNoise() { fatalError(“I forgot what my sound is”) }}
```

# 原则#4:界面分离

## *仅实施您需要的内容*

通过分离我们的接口，我们解决了 fat 接口的问题— *fat* 意味着当一个接口有太多的方法，包含了比我们真正需要使用的更多的信息。简单来说，我们有时候只是用了一些东西，而不是我们所拥有的一切。这里的教训应该是:只使用你需要的东西。

例如，您正在进行您的个人项目，并且您购买了一大堆工具来帮助您构建项目。但最终，你只使用了你拥有的五个工具中的一个或两个。嗯，有时候我们会不知不觉地这样做。

## 代码示例

看下面的代码，从逻辑上讲，我们知道 baby 不能真正去工作，但是因为我们符合`Action`协议，我们需要声明这个方法。这是一个我们没有使用的方法的例子。

```
protocol Action { func eat() func work()}class Adult: Action { func eat() { // adult eat } func work() { // adult work }}class Baby: Action { func eat() { // baby eat } func work() { // baby can’t work }}
```

上面的代码可以简单地重写如下。我们只使用我们需要的。

```
protocol EatAction { func eat()}protocol WorkAction { func work()}class Adult: EatAction, WorkAction { func eat() { // adut eat } func work() { // adult work }}class Baby: EatAction { func eat() { // baby eat }}
```

# 原则#5:依赖性倒置

## 依靠抽象，而不是具体

高级模块不应该依赖低级模块。根据这个理论，像视图控制器这样的高级模块不应该直接依赖于像网络组件这样的低级组件。相反，它们应该依赖于抽象，或者使用一个快捷的术语，*协议。*这里的要点是减少耦合。

如果您的类严重依赖于另一个类的实现，就会发生耦合。耦合可能是危险的，因为它使得将来很难改变代码库。

对于大多数 iOS 工程师来说，Firebase 是最喜欢使用的工具。比方说，Firebase 将在明天关闭，没有任何提前通知。我们应该能够快速切换到 Realm(例如)轻松部署我们的应用程序。这里的整个思想是通过避免紧密耦合来轻松替换我们的依赖性的能力。

## 代码示例

下面代码的问题是高级模块`DatabaseController`与低级模块`NetworkRequest`紧密耦合。

```
class DatabaseController { private let networkRequest: NetworkRequest init(network: NetworkRequest) { self.networkRequest = network } func connectDatabase() { networkRequest.connect() }}class NetworkRequest { func connect() { // connect to the database }}
```

解决这个问题的方法可能是使用`protocol`来避免紧耦合。

```
protocol Database { func connect()} class DatabaseController { private let database: Database init(db: Database) { self.database = db} func connectDatabase() { database.connect() }}class NetworkRequest: Database { func connect() { // Connect to the database }}
```

# 快速小结

在你成为一名更好的工程师的旅程中，请记住以下坚实的原则。

*   每个类应该只有一个责任
*   该类应该对扩展开放，但对修改关闭
*   子类不应该破坏父类的类型定义
*   仅实施您需要的内容
*   依靠抽象，而不是具体