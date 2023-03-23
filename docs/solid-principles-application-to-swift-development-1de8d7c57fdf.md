# 适用于快速开发的坚实原则

> 原文：<https://betterprogramming.pub/solid-principles-application-to-swift-development-1de8d7c57fdf>

## 巩固我们的 Swift 代码

# 介绍

作为一名开发人员，你可能使用或听说过[坚实的原则](http://butunclebob.com/ArticleS.UncleBob.PrinciplesOfOod)。

这个首字母缩写词是由 Robert c . Martin(Bob 叔叔，也是 de '[Clean architecture](https://medium.com/@raulferrer/what-is-clean-architecture-188a65c7dd48)')在他的书[*Agile Software Development:Principles，Patterns，and Practices*](https://www.goodreads.com/book/%20show%20/%2084985.Agile_Software_Development_Principles_Patterns_and_Practices) 中介绍的，指的是面向对象编程的五个基本原则，它们与[设计模式](https://www.raulferrergarcia.com/en/design-patterns-in-software/)有关。

如果我们应用这五个原则:

*   我们将有灵活的代码，我们可以很容易地改变，这将是可重用和可维护的。
*   开发的软件将是健壮的、稳定的和可伸缩的(我们可以很容易地添加新的特性)。
*   结合设计模式的使用，它将允许我们创建高度内聚(也就是说，系统的元素紧密相关)和松散耦合(元素之间的依赖程度低)的软件。

## 缩写词 SOLID 的由来

缩写 SOLID 来自:

*   单一责任原则
*   **O** (OCP):开/关原理
*   **L** (LSP):利斯科夫替代原理
*   **I** (ISP):接口隔离原则
*   **D** (DIP):依存倒置原则

# 单一责任原则

根据这个原则，一个类应该有且只有一个改变的理由。也就是说，一个类应该只有一个责任。

让我们看一个例子:

```
class LoginUser {
    func login() {
        let data = authenticareUserViaAPI()
        let user = decodeUser(data: data)
        saveToDB(array: array)
    }

    private func authenticareUserViaAPI() -> Data {
        // Call server to authenticate and return user's info
    }

    private func decodeUser(data: Data) -> User {
        // Decode data (Codable protocol) into User object
    }

    private func saveUserInfoOnDatabase(user: User) {
        // Save User info onto database
    }
}
```

如上所述，该类提供了三个职责:*认证*、*解码信息*和*保存信息*。为了实现唯一责任的原则，我们将这些责任提取到其他更小的类中

```
class LoginUser {
    let oAuthHandler: OAuthHandler
    let decodeHandler: DecodeHandler
    let databaseHandler: DataBaseHandler

    init(oAuthHandler: OAuthHandler, decodeHandler: DecodeHandler, databaseHandler: DataBaseHandler) {
        self.oAuthHandler = oAuthHandler
        self.decodeHandler = decodeHandler
        self.databaseHandler = databaseHandler
    }

    func login() {
        let data = oAuthHandler.authenticareUserViaAPI()
        let user = decodeHandler.decodeUser(data: data)
        databaseHandler.saveUserInfoOnDatabase(user: user)
    }
}

class OAuthHandler {
    func authenticareUserViaAPI() -> Data {
        // Call server to authenticate and return user's info
    }
}

class DecodeHandler {
    func decodeUser(data: Data) -> User {
        // Decode data (Codable protocol) into User object
    }
}

class DataBaseHandler {
    func saveUserInfoOnDatabase(user: User) {
        // Save User info onto database
    }
}
```

# 开/关原则

根据这个原则，我们必须能够在不改变其行为的情况下扩展该类。这是通过抽象实现的。

```
class Scrapper {

    func scrapVehicles() {
        let cars = [
            Car(brand: "Ford"),
            Car(brand: "Peugeot"),
            Car(brand: "Toyota"),
        ]

        cars.forEach { car in
            print(car.getScrappingAddress())
        }

        let trucks = [
            Truck(brand: "Volvo"),
            Truck(brand: "Nissan"),
        ]

        trucks.forEach { truck in
            print(truck.getScrappingAddress())
        }
    }
}

class Car {
    let brand: String

    init(brand: String) {
        self.brand = brand
    }

    func getScrappingAddress() -> String {
        return "Cars scrapping address"
    }
}

class Truck {
    let brand: String

    init(brand: String) {
        self.brand = brand
    }

    func getScrappingAddress() -> String {
        return "Trucks scrapping address"
    }
}
```

每一款新车型都要重新实现`getScrapingAddress()`功能，打破了开/关的原则。

为了解决这一点，我们引入了包含`getScrappingAddress()`方法的`Scrappable`协议:

```
protocol Scrappable {
    func getScrapingAddress() -> String
}

class Scrapper {

    func getScrapingAddress() {
        let vehicles: [Scrappable] = [
            Car(brand: "Ford"),
            Car(brand: "Peugeot"),
            Car(brand: "Toyota"),
            Truck(brand: "Volvo"),
            Truck(brand: "Nissan"),
        ]

        vehicles.forEach { vehicle in
            print(vehicle.getScrapingAddress())
        }
    }
}

class Car: Scrappable {
    let brand: String

    init(brand: String) {
        self.brand = brand
    }

    func getScrapingAddress() -> String {
        return "Cars scrapping address"
    }
}

class Truck: Scrappable {
    let brand: String

    init(brand: String) {
        self.brand = brand
    }

    func getScrapingAddress() -> String {
        return "Trucks scrapping address"
    }
}
```

# 利斯科夫替代原理

Barbara Liskov 在 1987 年提出的这个原则指出，在一个程序中，任何类都应该能够被它的一个子类替换，而不会影响它的功能。

例如，假设我们有一个类，`UserService`，它负责联系用户(例如，发送电子邮件)。

如果我们改变业务逻辑，例如，我们只能向 17 岁以上的用户发送电子邮件，我们可以创建一个子类来添加新的业务逻辑:

```
class UserService {
    func contact(user: User) {
        // Retrieve user from database
    }
}

class ValidUserService: Handler {
    override func contact(user: User) {
        guard user.age > 17 else { return }

        super.contact(user: User)
    }
}
```

在这种情况下，Liskov 替换原则没有得到满足，因为子类增加了一个条件(用户的年龄超过 17 岁)，这是`UserService`类的客户所不期望的。

我们可以通过不创建子类，并添加前置条件到`UserService`(包括一个默认值)来解决这个问题:

```
class UserService {
    func contact(user: User, minAge: Int = 0) {
        guard user.age > minAge else { return }
        // Retrieve user from database
    }
}
```

# 界面分离原理

接口分离原则表明，拥有特定于每个客户端的不同接口(协议)比拥有一个通用接口更好。

此外，它表明客户端不必实现他们不使用的方法。

例如，我们可以为动物创建一个包含置换方法的界面:

```
protocol AnimalProtocol {
    func walk()
    func swimm()
    func fly()
}

struct Animal: AnimalProtocol {
    func walk() {}
    func swimm() {}
    func fly() {}
}

struct Wale: AnimalProtocol {
    func swimm() {
        // Walw only needs to implement this function
        // All the other functions are irrelavant
    }

    func walk() {}

    func fly() {}
}
```

然而，虽然`Wale`采用了该协议，但有两个方法它没有实现。解决方案是建立三个接口(协议)，每个方法一个接口:

```
protocol WalkProtocol {
    func walk()
}

protocol SwimmProtocol {
    func swimm()
}

protocol FlyProtocol {
    func fly()
}

struct Wale: SwimmProtocol {
    func swimm() {}
}

struct Crocodile: WalkProtocol, SwimmProtocol {
    func walk()
    func swimm() {}
}
```

# 从属倒置原则

根据依赖性倒置原则:

*   高级类不应该依赖低级类。两者都应该依赖于抽象。
*   抽象不应该依赖于细节。细节应该取决于抽象。

这个原则试图减少模块之间的依赖性，从而实现类之间的低耦合。

让我们看看下面的例子:

```
class User {
    var name: String

    init(name: String) {
        self.name = name
    }
}

class CoreData {
    func save(user: User) {
        // Save user on database
    }
}

class UserService {
    func save(user: User) {
        let database = CoreData()
        database.save(user: user)
    }
}
```

如果我们想使用[领域数据库](https://realm.io/products/realm-database/)而不是使用`CoreData`来保存数据，会发生什么？

我们在示例中完成的类的实例化生成了一个强耦合，所以如果我们想要使用另一个数据库，我们将不得不重做代码。

要解决这个问题，我们可以按照上一篇文章中的解释，在建立数据库层时进行。

```
protocol Storable { }

extension Object: Storable { } // Realm Database
extension NSManagedObject: Storable { } // Core Data Database
protocol StorageManager {
  /// Save Object into Realm database
  /// - Parameter object: Realm object (as Storable)
  func save(object: Storable)
}
```

现在我们有了采用`Storable`协议的`User`和采用`StorageManager`协议的`UserService`类，所以即使我们改变了数据库，我们也不需要改变整个实现代码:

```
class User: Storable {
    var name: String

    init(name: String) {
        self.name = name
    }
}

class UserService: StorageManager {
    func save(object: Storable) {
        // Saves user to database
    }
}
```

# 结论

这些原则将允许我们在代码中创建可重用、可维护和高质量的组件，从而减少代码的刚性和脆弱性。