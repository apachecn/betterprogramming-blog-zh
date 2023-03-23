# 如何编写干净的类型脚本代码

> 原文：<https://betterprogramming.pub/how-to-write-clean-typescript-code-eda1716eead1>

## 8 个知识点写出优雅的打字稿代码。

![](img/641cd809f0dc80d6e5eb061dcc82ac78.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上 [Olia Gozha](https://unsplash.com/@olia?utm_source=medium&utm_medium=referral) 拍摄的照片

# 1.用不可变类型标记

有些常量我们不希望以任何方式修改，虽然 const 关键字很好，但它并不限制引用类型，其值仍然可以修改，例如:

```
const obj = {
   elementId: '#dashboard',
}// Try modifying the properties of ElementID
obj.elementId = '';// output => ''
console.log(obj.elementId)
```

虽然可以通过[*object . freeze()*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)方法冻结对象，但是开发过程中没有任何错误提示，体验非常不好，所以如何通过 TypeScript 防止上述情况，就需要使用 TypeScript [*作为 const 关键字*](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes-func.html#readonly-and-const)

![](img/d2e68c7c3d57e41622ceedb524e93576.png)

TypeScript 作为 const 关键字

值得一提的是，无论你的对象层次结构有多深，任何在使用 [**作为 const 关键字**](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes-func.html#readonly-and-const) 之后修改一个值的操作都会导致错误，这是很棒的，因为我们有很多在开发过程中不希望被修改的常量。

同时，`as const`关键字也适用于**数组**，TypeScript 会在运行时自动阻止对数组有破坏性的方法，比如`**push, pop, splice, unshift**`方法，当你使用它们时会出错

`as const`其实相当于`readonly`

# 2.使用默认参数，而不是短路或条件

默认参数通常比短路更简洁。

不好:

```
function loadPages(count?: number) {
  const loadCount = count !== undefined ? count : 10;
  // ...
}
```

好:

```
function loadPages(count: number = 10) {
  // ...
}
```

# 3.使用 enum 记录意图

枚举可以帮助您记录代码的意图。例如，当我们关心的是不同的值，而不是这些值的精确值时。

不好:

```
const IMPORT_FILE_ERROR_MESSAGE: Record<ImportFileErrorType, string> = {  
   'LIMIT': 'import.file.limit',
   'UPLOAD_FAILED': 'import.file.upload.failed' 
}
```

好:

```
export enum ImportFileErrorType {
  LIMIT = 'LIMIT',
  UPLOAD_FAILED = 'UPLOAD_FAILED',
}const IMPORT_FILE_ERROR_MESSAGE: Record<ImportFileErrorType, string> = {
   [ImportFileErrorType.LIMIT]: 'import.file.limit', 
   [ImportFileErrorType.UPLOAD_FAILED]: 'import.file.upload.failed'
}
```

值得注意的是，枚举是不可变的，试图修改它们的值会导致错误。

一般来说，枚举通常用在条件判断、常量和类型语句中，使您的代码更加健壮和可读。

如何区分类型和接口？请[阅读我的另一篇](https://javascript.plainenglish.io/the-difference-between-typescript-interface-and-type-final-version-3e8801a16b2f)文章，其中详细解释了他们的差异。

# 4.抽象类

当使用类继承时，我们不希望基类被实例化，所以我们可以使用 TypeScript 的 [*抽象关键字*](https://www.typescriptlang.org/docs/handbook/2/classes.html#abstract-classes-and-members) 来修改一个类，以表示它是一个只能被实现而不能被实例化的抽象类

不好:

```
class Animal {
   a(): void {}
}class Dog extends Animal {}// No error, but the Animal is the base class,
// We don't want it to be instantiated
new Animal().a()
```

好:

```
abstract class Animal {
   a(): void {}
}class Dog extends Animal {
   a() {}
}// Error: Cannot create an instance of an abstract class.(2511)
new Animal().a()
```

# 5.高内聚低耦合

内聚定义了类成员之间相互关联的程度。理想情况下，每个方法都应该使用一个类中的所有字段。然后我们说这个类是*最大内聚*。然而，在实践中，这并不总是可能的，甚至也不可取。然而，您应该更喜欢内聚力高。

耦合指的是两个类之间的相关或依赖程度。如果其中一个类的变化不影响另一个，那么这个类就是低耦合的。

好的软件设计是高内聚低耦合的。

不好:

```
class UserManager {
  // Bad: each private variable is used by one or another group of methods.
  // It makes clear evidence that the class is holding more than a single responsibility.
  // If I need only to create the service to get the transactions for a user,
  // I'm still forced to pass and instance of `emailSender`.
  constructor(
    private readonly db: Database,
    private readonly emailSender: EmailSender) {
  } async getUser(id: number): Promise<User> {
    return await db.users.findOne({ id });
  } async getTransactions(userId: number): Promise<Transaction[]> {
    return await db.transactions.find({ userId });
  } async sendGreeting(): Promise<void> {
    await emailSender.send('Welcome!');
  } async sendNotification(text: string): Promise<void> {
    await emailSender.send(text);
  } async sendNewsletter(): Promise<void> {
    // ...
  }
}
```

好:

```
class UserService {
  constructor(private readonly db: Database) {} async getUser(id: number): Promise<User> {
    return await this.db.users.findOne({ id });
  } async getTransactions(userId: number): Promise<Transaction[]> {
    return await this.db.transactions.find({ userId });
  }
}class UserNotifier {
  constructor(private readonly emailSender: EmailSender) {} async sendGreeting(): Promise<void> {
    await this.emailSender.send('Welcome!');
  } async sendNotification(text: string): Promise<void> {
    await this.emailSender.send(text);
  } async sendNewsletter(): Promise<void> {
    // ...
  }
}
```

# 6.偏好组合而非继承

正如四人组在[设计模式](https://en.wikipedia.org/wiki/Design_Patterns)中所说的，在可能的情况下，你应该*更喜欢组合而不是继承*。有很多很好的理由使用继承，也有很多很好的理由使用组合。这条格言的主要观点是，如果你的大脑本能地去继承，试着想想构图是否能更好地模拟你的问题。在某些情况下，它可以。

你可能会想，“什么时候我应该使用继承？”这取决于你手头的问题，但是这是一个继承比组合更有意义的列表:

1.  您的继承代表“是-a”关系，而不是“有-a”关系(人类->动物对用户->用户详细信息)。
2.  您可以重用基类中的代码(人类可以像所有动物一样移动)。
3.  您希望通过更改基类来对派生类进行全局更改。(改变所有动物运动时的热量消耗)。

```
class Employee {
  constructor(
    private readonly name: string,
    private readonly email: string) {
  } // ...
}// Bad because Employees "have" tax data. 
// EmployeeTaxData is not a type of Employee
class EmployeeTaxData extends Employee {
  constructor(
    name: string,
    email: string,
    private readonly ssn: string,
    private readonly salary: number) {
    super(name, email);
  } // ...
}
```

好:

```
class Employee {
  private taxData: EmployeeTaxData; constructor(
    private readonly name: string,
    private readonly email: string) {
  } setTaxData(ssn: string, salary: number): Employee {
    this.taxData = new EmployeeTaxData(ssn, salary);
    return this;
  } // ...
}class EmployeeTaxData {
  constructor(
    public readonly ssn: string,
    public readonly salary: number) {
  } // ...
}
```

# 7.使类型更加耦合

当许多人编写类型脚本代码时，他们通常喜欢这样写:

```
const getUserNameById = (id: string) => {}
```

嗯，这很好，至少我们知道我们需要传入一个`string`参数，但是`getUserNameById`看起来像一组相关的函数，所以最好的办法是:

```
interface IUser {
   readonly id: string;
   name: string;
   email: string;
}const getUserNameById = (id: IUser['id']) => {}
```

这个看起来好多了。另一个有趣的情况是当你的函数返回`a Boolean type`时，如果条件合适，你最好这样写:

```
const isCurrentUser = (user: IUser): user is IUser => { 
   return user.name === 'Keris'
}
```

让类型充满耦合性无疑是最好的决定，在 React 中可以大量练习。