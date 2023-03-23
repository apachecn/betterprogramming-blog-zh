# 将 TypeScript 与 React 一起使用:分步指南

> 原文：<https://betterprogramming.pub/using-typescript-with-react-a-step-by-step-guide-64f58cbbc004>

## 了解 TypeScript 中有关接口、枚举、类型别名、泛型、属性类型和函数的所有信息

![](img/07b7e9b6613bb112967a6fc8fa97c6cf.png)

科林·阿姆斯壮在 [Unsplash](https://unsplash.com/s/photos/bouncer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

我将使用带有 React 的 TypeScript 开始一项新工作。我以前从来没有这样做过，但是我有单独使用两者的经验。我认为学习新东西的最好方法是把它教给其他人，所以这就是我在这里要尝试做的。

我们将构建一个夜总会客人名单应用程序，灵感来自赫伯特·沃尔弗森的 [*手把手*的一个例子。该应用程序将显示夜总会的所有参与者，每个人的名字都根据他们是否被允许进入、拒绝、禁止或添加到“特殊”客人列表中而着色。我们还会有一个添加新客人的表格。](https://www.amazon.co.uk/dp/B09BK8Q6GY/ref=dp-kindle-redirect?_encoding=UTF8&btkr=1)

TypeScript 非常庞大，涵盖所有概念需要很长时间，所以我在这里的目的是坚持基础知识；提供一个基础，从这里你可以建立更先进的知识。我们将涉及的主题是接口、枚举、类型别名、泛型、属性类型和函数。

## 你需要知道的是

你必须知道反应；我不会解释的。你不需要知道任何打字稿。

你也可以[在这里](https://github.com/skepticspectresceptre/react-typescript-nightclub)找到完成的代码。

## 创建新的 TypeScript 和 React 应用程序

使用 *Create-React App* 进行设置很简单。在你的终端上运行这个。

```
npx create-react-app — template typescript nightclub
```

然后，运行:

```
npm start
```

## 领域建模

当我开始一件新的事情时，我通常从领域建模开始。实际上，这意味着首先要定义应用程序的数据结构。

这是设计软件的好方法，因为它迫使你在编码前仔细考虑它的用法。有了预定义的类型，我们确切地知道我们需要做什么来支持它们，我们就不会陷入编写无用代码的陷阱(如果它与数据没有任何关系，为什么它会在那里？)，它帮助我们命名我们的组件和功能(因为我们知道领域)，并且它对其他开发人员来说是很好的文档。

在`src/`文件夹中创建一个`types/`文件夹，并在其中添加一个名为`index.ts`的文件。`.ts`扩展名是我们用于普通类型脚本文件的扩展名。我们用`.tsx`来表示反应。

## **创建与会者界面**

我们从我们领域中最重要的部分开始:参与者。我们需要确切地知道关于他们的三件事:他们的名字、他们的年龄和他们所拥有的权限级别(允许、拒绝、禁止或特殊客人)。

在 TpeScript 中，捕捉这些信息的最佳方式是使用一个*接口*:一个封装了许多字段的结构，每个字段都有自己的类型。我们的与会者界面如下所示:

```
interface Attendee {
    name: string,
    age: number,
    permission: string
}
```

这类似于 javascript 对象，但是有了 TypeScript 接口，我们获得了更大的能力。例如，我们现在可以告诉一个函数，我们希望它接受一个 Attendee 类型的参数。这是它的样子。

```
const printAttendee = (attendee: Attendee) => {
    console.log(attendee)
}
```

现在，如果您试图用一个不同类型的参数(一个看起来不像上面的结构)调用它，您将得到一个错误消息。

```
printAttendee({ name: 'John' });
```

错误:

```
Argument of type '{ name: string; }' is not assignable to parameter of type 'Attendee'.
  Type '{ name: string; }' is missing the following properties from type 'Attendee': age, permission
```

这仍然会运行。TypeScript 不同于 Rust 或 Go，它仍然可以执行程序。但是只要注意它给你带来的错误，你就可以确信你正在按照最初的意图使用你的函数。

顺便说一下，这也适用于返回类型。

```
const changeAttendeeToBryan = (attendee: Attendee): Attendee => {
    const newAttendee = { ...attendee, name: 'Bryan' }
    return newAttendee
}
```

上面的函数有返回一个`Attendee` 对象否则 TypeScript 会报错。

在`src/types/index.ts`中创建您的`Attendee`接口，并确保导出它。

```
export interface Attendee {
    name: string,
    age: number,
    permission: string
}
```

**创建 AttendeePermission 枚举**

现在，`Attendee`的`permission`字段是一个字符串，但是我们知道它只能保存一组特定的值:允许、拒绝、禁止或特殊来宾。我们可以继续我们所拥有的，并且只希望我们只将这些值中的一个赋值给`permission`属性。或者我们可以显式地使用枚举。

TypeScript 中的枚举如下所示。

```
enum AttendeePermission {
    Admitted,
    Refused,
    Banned,
    SpecialGuest
}
```

枚举是一种特殊类型，它只能包含一组特定的*变量*(值)。这意味着如果某个东西属于类型`AttendeePermission`，它只能被指定为我们指定的四个变量中的一个。

现在我们可以更改 Attendee 接口的 permission 属性，如果我们使用不当，TypeScript 会警告我们。

```
export interface Attendee {
    name: string,
    age: number,
    permission: AttendeePermission
}
```

如果我们试图不正确地使用它，像这样:

```
const attendee: Attendee = {
    name: "john",
    age: 2000,
    permission: 'Hello'
}
```

然后我们会得到一个错误:

```
Type 'string' is not assignable to type 'AttendeePermission'.ts(2322)
```

以下是正确的做法:

```
const attendee: Attendee = {
    name: "john",
    age: 2000,
    permission: AttendeePermission.Admitted
}
```

就是这样。我们已经模拟了我们的领域。您的`src/types/index.ts`文件应该是这样的。

现在我们可以转到应用程序了。

# React 应用程序

夜总会应用程序将有一个页面。它将显示所有的与会者，加上一个表单来添加一个新的。一个真实的来宾名单应用程序可能会获取一些初始数据:预先预订或已知的特殊来宾。我们将用一个定制的钩子来模拟这种情况，该钩子处理返回与会者并添加一个新的与会者。这也是我们第一次使用我们刚刚定义的一些类型。

## **用户定制挂钩**

其中很多都是普通的 React 代码，希望大家熟悉。

在第 4 行，我们已经创建了一个初始参与者列表，所以我们从一些数据开始。为了告诉 TypeScript 我们想要使用某种类型的数组，我们必须编写`type[]`，这就是我们在这里为与会者列表所做的。现在，如果我们试图向列表中添加任何内容，TypeScript 将会抱怨。

在第 17 行，我们为我们的`addAttendee` 函数创建一个*类型*别名并导出它。我将更详细地解释那件事。

## 键入别名

一个*类型别名*只是你给一个现有类型的名字。类似于给朋友起外号。它们可以用来缩短原始类型或使您的代码更具可读性。例如，如果我正在创建一个电话簿应用程序，并且除了电话号码之外，我对号码类型没有其他用途，我可能会为基本的*号码*类型创建一个别名，名为*电话号码*。

```
type PhoneNumber = number;
```

这对编译器来说没什么区别，但是该应用程序的贡献者可以在他们可能使用`number` *、*的任何地方使用`PhoneNumber` ，这将使代码的意图更加清晰。

在我们的例子中，我为函数签名创建了一个类型别名。我们知道`addAttendees` 函数的签名是`(attendee: Attendee) => void`——也就是说，它接收一个 attendee 对象，并且不返回任何内容——当我们稍后想要将该函数作为一个道具传递时，我们需要能够告诉接收组件它应该期待什么函数签名。

如果我们不这样做，我们就不会得到类型检查，也不会得到编译器提示。但是我们不希望每次需要使用它的时候都要写`attendee: Attendee) => void`，因为它又丑又烦人。通过别名化，我们可以使用`AddAttendeeFunction`作为道具的类型。希望这是有意义的，但是如果没有意义，一秒钟后就会有意义，因为我们在下一行使用它。

回到我们的代码片段，在第 19 行，我们给`useAttendees`钩子添加了一个返回类型。这是一个有两个索引的数组。第一个索引是与会者的列表，所以我们用`Attendee[]`指定了它。第二个索引将是我们的`addAttendee` 函数，正如我们刚刚讨论的，它有一个签名`attendee: Attendee) => void`。但是我们已经给它起了别名，所以我们不需要把整个事情写出来。我们可以只使用`AddAttendeeFunction`。

在第 20 行，我们使用了一个名为 [*泛型*](https://www.tutorialsteacher.com/typescript/typescript-generic) 的类型脚本特性。*泛型*是一种编写函数(和其他东西)的方式，能够接收和处理所有不同类型的参数。这是一个复杂的话题，但我会给出一个非常基本的例子。

## 类型脚本泛型

```
const printType = <T>(type: T): T => {
    console.log(typeof type);
    return type;
}
```

`<T>`部分是使这个函数通用的部分。当我们调用它时，我们可以为 T 指定一个类型，它将被用作参数的类型和返回类型。为 T 指定一个类型如下所示。

```
printType<string>("Hello")
```

当你写的时候，你可以想象这个函数是这样的。

```
const printType = <string>(type: string): string => {
    console.log(typeof type);
    return type;
}
```

如果您指定数字作为类型，它将看起来像这样。

```
const printType = <number>(type: number): number => {
    console.log(typeof type);
    return type;
}
```

您可以任意多次调用通用函数*并每次指定不同的类型。只要你遵守规则，它就会起作用。不是遵守规则长这样。*

```
printType<string>(1)
```

您也可以创建只接受一组特定类型的函数。这是首选。越清楚应该如何使用一个函数越好。下面是将一个函数约束为字符串类型和数字类型的情况。

```
const printType = <T extends string | number>(type: T): T => {
    console.log(typeof type);
    return type;
}
```

回到`useAttendees` 钩子，我们可以看到我们将第 20 行的`useState` 调用约束为类型`Attendees[]`。我们实际上并不需要这样做，但是正如我所说的，这使得我们的意图更加清晰，并且为我们提供了方便的编译器提示。

我们代码片段的最后一部分是第 22 行，在这里我们确保将一个`Attendee`类型传递给`addAttendee`函数。

**app . tsx 组件**

App 组件很简单，没有显式的类型脚本。我们还没有创建`AttendeeList`组件，但是我们现在会这样做。

**attendee list . tsx 组件**

希望这很容易理解。我们已经看过大部分这些东西了。

在第 7 行，我们为我们的道具定义了一个接口。我们不会将它出口到任何其他地方；每个组件都有自己的 props 接口。我们还可以看到我们的`AddAttendeeFunction`类型别名也在使用中。

在第 12 行，我们告诉 React，传递给这个组件的道具必须符合`AttendeeListProps` 接口。我们还返回了一个`ReactElement`:React 用于其 jsx 结构的类型。

这就是这个组件的全部内容。如果您返回到`App.tsx`并试图向`AttendeeList`组件传递一个没有在 *AttendeeListProps* 中定义的道具，您应该会得到一个错误。如果你漏掉了其中一个，你也会得到一个错误。也试着给`addAttendee` 传递一个不符合别名的函数:应该不行。

接下来我们将创建`AttendeeInformation`组件，最后创建`CreateAttendee`组件。

【The AttendeeInformation.tsx 组件

`AttendeeInformation`组件只做两件事:显示与会者的详细信息，并根据他们的许可给每个人分配一种颜色。

在第 8 行，`getBackgroundColor`函数使用一个 switch 语句来计算传递给它的 permission 属性。这很好，我认为这很清楚发生了什么，但有一个替代方案值得一提。

在 TypeScript 枚举定义中，可以为每个变量赋值，如下所示。

```
export enum AttendeePermission {
    Admitted = "green",
    Refused = "red",
    Banned = "grey",
    SpecialGuest = "gold"
}
```

这意味着您可以直接将 permission 的值赋给 *background* 属性，而不是切换 permission，就像这样。

```
<div style={{ backgroundColor: *props*.attendee.permission }}>
```

这是可行的，这意味着你可以去掉整个`getBackgroundColor`函数。然而，这也意味着你必须在你的`types`文件和你的`AttendeeInformation`组件之间来回跳转，以确定什么是什么，并且在一个更复杂的应用程序中，你的`AttendeePermission`枚举可能有更多的用途，而不仅仅是设置一个背景颜色，因此将它与颜色如此紧密地联系在一起可能是不合适的。做出这些选择取决于您，但是我在这里展示它是为了演示您可以做什么。

无论你决定走哪条路，我们现在应该有一个这样的应用程序。

![](img/e7cfa88713994c367409d3ce51eab7c4.png)

现在是最后一个部分。

【The CreateAttendee.tsx 组件

这是迄今为止最大的组件，但主要是因为 jsx。

在第 9 行，我们创建了一个`PermissionsOptions`记录。一个`Record`就像一个 JavaScript 对象，但是我们可以使用泛型来约束类型。在这个例子中，我说过我想要一个键是字符串，值是`AttendeePermission`类型的记录。

该记录的存在是因为我们需要创建一个 select 元素来为每个新参与者选择权限，并且我们需要一种方法来将 select 字段值映射到一个实际的`AttendeePermission`变量。这里的`Record`也很好，因为它使选择选项和它们对应的值紧密耦合，所以只要我们只在选择元素中引用这个记录，我们就可以确保我们不会试图引用不存在的变量，比如`TooDrunkToEnter`或其他什么。

这里的其他东西你都已经看过了。如果你通读一遍，应该就很清楚了。

完成后，你应该有一个工作的应用程序，你可以添加新的与会者到客人名单，他们会根据他们的权限级别分配不同的背景颜色。

# **包装完毕**

这个应用不会赢得任何设计奖项，而且你可以用 TypeScript 做这么多事情，我甚至不会说它很棒。但这是一个*好的*起点。你在这里学到的概念——*接口、枚举、类型别名、泛型、属性类型、函数*——已经可以提高你的应用程序的安全性，消除错误，为你和你的团队提供更好的开发体验。不过，你应该继续调查。

TypeScript 很有趣，而且还有更多的东西可以玩。