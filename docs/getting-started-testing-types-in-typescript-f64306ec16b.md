# 开始在 TypeScript 中测试类型

> 原文：<https://betterprogramming.pub/getting-started-testing-types-in-typescript-f64306ec16b>

## 关于在 TypeScript 中测试高级类型(如泛型或条件类型)的指南

![](img/24831e3f2c5b032df49c301ebc9fa117.png)

[来源](https://unsplash.com/photos/DuHKoV44prg)

一旦你开始编写更高级的类型，比如 G [泛型](https://www.typescriptlang.org/docs/handbook/2/generics.html)或[条件类型](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html)在 [TypeScript](https://www.typescriptlang.org/) 中，那么检查你的类型是否如你所愿，这应该成为关键的一步。测试类型与编写普通的单元测试有一点不同，因为我们测试的是未经测试的代码。

测试类型有三个主要选项:使用类似 [dtslint](https://github.com/microsoft/DefinitelyTyped-tools/tree/master/packages/dtslint) 、 [tsd](https://github.com/SamVerschueren/tsd) 的工具，或者使用 [@ts-expect-error](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-9.html#-ts-expect-error-comments) 注释。我将介绍 [@ts-expect-erro](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-9.html#-ts-expect-error-comments) r，因为这是最简单的入门方式，并且不需要额外的工具。

我将使用两个注释`[@ts-expect-error](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-9.html#-ts-expect-error-comments)`和`@ts-expect-no-error`。
@ts-expect-no-error 纯粹是描述性的，因为它除了表明我们期望下一行**而不是**抛出错误之外没有其他功能。

本文中的所有代码都可以在这里找到:【https://github.com/nic-jennings/testing-types-in-typescript 

# 测试拨片

让我们从测试内置类型[开始，选择<类型，选择>键](https://www.typescriptlang.org/docs/handbook/utility-types.html#picktype-keys)。如果我们创建这个类型，我们会希望确保它返回我们选择的类型，或者当原始类型上不存在键时，从不返回。

我将使用以下类型编写本文中的所有测试:

```
type MainObject = {
   id: string;
   name: string;
   nested: {
        id: string;
        name: string;
   };
   nestedArray: MainObject["nested"][];
};
```

测试将以下列格式编写:

*   创建一个`let`变量，它被约束到我们的类型，并按照预期的结果命名。
*   在`@ts-expect-error`或`@ts-expect-no-error`下面，给变量赋值以确保我们的类型创建了我们期望的约束。

我们需要测试是否有可能基于我们从`MainObject`得到的`Pick`类型来约束一个变量。

```
let PickSelected: Pick<MainObject, "id">;// @ts-expect-no-error
PickSelected = { id: "someString" };// @ts-expect-error
PickSelected = { name: "someString" };
```

上面的测试阐明了将一个变量约束到所选择的类型是可能的，但是这又引出了一个问题:它将类型约束到一个对象类型了吗？

```
let PickObjectType: Pick<MainObject, "nested">;
// @ts-expect-no-error
PickObjectType = { 
    nested: { id: "someString", name: "something" } 
};
// @ts-expect-error
PickNestedObject = { id: "someString" };
// @ts-expect-error
PickObjectType = { nested: { id: "someString"} }
```

我们可以约束一种类型的对象，但它必须匹配整个对象。最后的测试是断言不可能`Pick`一个不在我们`MainObject`中的项目。一条简单的线证明了这一点:

```
// @ts-expect-error
let PickItemNotInObject: Pick<MainObject, "bla">;
```

上面的例子演示了一个相当简单的高级类型来测试，但是如果我们有更复杂的东西呢？

# 测试自定义类型

为了使文章的这一部分更加清晰，我想创建一个类型，它允许我创建其他类型的子集，同时显式地只返回两个类型的交集。为了实现这一点，我需要创建两种类型。

***请注意:*** *此类型为进行中的工作，但以下为当前设置:*

## **提取按键**

```
/** 
* Extract the intersecting Keys from two Objects. 
* Right Object is the source of truth. 
*/
export type ExtractKeys<T, Obj> = Extract<keyof T, keyof Obj>;
```

## **子集< T，Obj>**

```
/**
 * Returns a new Type where Two Objects recursively intersect.
 * Right Object is the source of truth.
 */
export type SubSet<T, Obj> = ExtractKeys<T, Obj> extends never
  ? never
  : {
      [Key in ExtractKeys<T, Obj>]: Obj[Key] extends object
        ? Obj[Key] extends Array<object>
          ? number extends keyof Obj[Key] & keyof T[Key]
            ? SubSet<T[Key][number], Obj[Key][number]>[]
            : never
          : SubSet<T[Key], Obj[Key]>
        : Obj[Key] | undefined extends T[Key]
        ? Obj[Key] | undefined
        : Obj[Key] extends T[Key]
        ? Obj[Key]
        : never;
    };
```

这将需要两个测试，一个是确保`ExtractKeys` infact 返回两种类型的交叉键，另一个是`SubSet`从两种类型的交集创建一个新类型，作为[泛型](https://www.typescriptlang.org/docs/handbook/2/generics.html)。

对于`ExtractKeys`，我编写了以下测试:

```
let NoKeys: ExtractKeys<{}, MainObject>;
// @ts-expect-error
NoKeys = "";let MainOnlyKey: ExtractKeys<{ id: string }, MainObject>;
// @ts-expect-no-error
MainOnlyKey = "id";
// @ts-expect-error
MainOnlyKey = "name";let KeyNotInMainObject: ExtractKeys<MainObject, MainObject>;
// @ts-expect-no-error
KeyNotInMainObject = "id";
// @ts-expect-error
KeyNotInMainObject = "bla";let RequestedKeyNotInMainObject: ExtractKeys<MainObject & { bla: string }, MainObject>;
// @ts-expect-no-error
RequestedKeyNotInMainObject = "id";
// @ts-expect-error
RequestedKeyNotInMainObject = "bla";
```

这允许我看到我只得到预期的相交键，所以我可以继续测试`SubSet`类型测试。

对于这个测试，我创建了下面的对象，它匹配原始类型，因为我发现我在测试中复制了非常相似的代码。

```
const MainObjectValue: MainObject = {
   id: "bla",
   name: "bla",
   nested: {
     id: "bla",
     name: "bla",
   },
   nestedArray: [
     {
       id: "bla",
       name: "bla",
     },
   ],
 };
```

从下面的测试中，我知道,`SubSet`类型返回两个`Types`的递归交集，并将变量约束为我们创建的新类型。

```
/**
 * Describe: SubSet
 */
let SubSetNoKeys: SubSet<{ bla: unknown }, MainObject>;
// [@ts](http://twitter.com/ts)-expect-error
SubSetNoKeys = {};
// [@ts](http://twitter.com/ts)-expect-error
SubSetNoKeys = { id: "bla" };let SubSetMainOnlyKey: SubSet<{ id: string }, MainObject>;
// [@ts](http://twitter.com/ts)-expect-no-error
SubSetMainOnlyKey = { id: "bla" };
// [@ts](http://twitter.com/ts)-expect-error
SubSetMainOnlyKey = { name: "bla" };let SubSetKeyOptional: SubSet<{ id?: string; name: string }, MainObject>;
// [@ts](http://twitter.com/ts)-expect-no-error
SubSetKeyOptional = { id: "bla", name: "bla" };
// [@ts](http://twitter.com/ts)-expect-no-error
SubSetKeyOptional = { id: undefined, name: "bla" };
// [@ts](http://twitter.com/ts)-expect-error
SubSetKeyOptional = { name: "bla" };let SubSetMisMatchType: SubSet<{ id: number }, { id: string }>;
// [@ts](http://twitter.com/ts)-expect-error
SubSetMisMatchType = { id: "bla" };
// [@ts](http://twitter.com/ts)-expect-error
SubSetMisMatchType = { id: 1 };let SubSetKeyNotInMainObject: SubSet<MainObject, MainObject>;
// [@ts](http://twitter.com/ts)-expect-no-error
SubSetKeyNotInMainObject = MainObjectValue;
// [@ts](http://twitter.com/ts)-expect-error
SubSetKeyNotInMainObject = { id: "bla" };
// [@ts](http://twitter.com/ts)-expect-error
SubSetKeyNotInMainObject = { bla: "bla" };let SubSetRequestedKeyNotInMainObject: SubSet<
  MainObject & { bla: string }, MainObject>;
// [@ts](http://twitter.com/ts)-expect-no-error
SubSetKeyNotInMainObject = MainObjectValue;
// [@ts](http://twitter.com/ts)-expect-error
SubSetRequestedKeyNotInMainObject = { id: "bla" };
// [@ts](http://twitter.com/ts)-expect-error
SubSetRequestedKeyNotInMainObject = { ...MainObjectValue, bla: "bla" };let NestedSubSetKeyInMainObject: SubSet<
  {
    nested: {
      id: string;
    };
  },  MainObject>;
// [@ts](http://twitter.com/ts)-expect-no-error
NestedSubSetKeyInMainObject = MainObjectValue;
// [@ts](http://twitter.com/ts)-expect-no-error
NestedSubSetKeyInMainObject = { nested: { id: "bla" } };
// [@ts](http://twitter.com/ts)-expect-error
NestedSubSetKeyInMainObject = { nested: { name: "bla" } };let NestedSubSetKeyNotInMainObject: SubSet<
  {
    nested: {
      bla: string;
    };
  },
  MainObject>;
// [@ts](http://twitter.com/ts)-expect-no-error
NestedSubSetKeyNotInMainObject = { nested: null as never };
// [@ts](http://twitter.com/ts)-expect-error
NestedSubSetKeyNotInMainObject = MainObjectValue;
// [@ts](http://twitter.com/ts)-expect-error
NestedSubSetKeyNotInMainObject = { nested: { id: "bla" } };
// [@ts](http://twitter.com/ts)-expect-error
NestedSubSetKeyNotInMainObject = { nested: { name: "bla" } };let NestedArraySubSetKeyNotInMainObject: SubSet<
  {
    nestedArray: {
      id: string;
    }[];
  },
  MainObject>;
// [@ts](http://twitter.com/ts)-expect-no-error
NestedArraySubSetKeyNotInMainObject = MainObjectValue;
// [@ts](http://twitter.com/ts)-expect-no-error
NestedArraySubSetKeyNotInMainObject = { nestedArray: [{ id: "bla" }] };
// [@ts](http://twitter.com/ts)-expect-error
NestedArraySubSetKeyNotInMainObject = { nestedArray: [{ name: "bla" }] };
```

这将是重构`SubSet`类型的时候，因为我们已经在测试中包装了我们需要的功能，所以我们可以确保当我们进行改进和更改时，我们的类型仍然像预期的那样工作。

# 结论

在我看来，这种测试方式的美妙之处在于它允许你使用 TDD(红/绿测试)来编写你的类型。它还将帮助您了解当您开始创建高级类型时实际发生了什么，最后它让其他软件工程师/开发人员清楚您的类型正在做什么，并确保它不会在没有适当的关注和考虑的情况下被功能性地更改。

我个人不建议为简单的类型定义编写类型测试，比如在一个 [React](https://reactjs.org/) 组件上的参数，但是一旦在读取一个类型时有任何歧义或者认知负荷，那么测试就是至关重要的。

感谢您花时间阅读我的文章。我希望你已经发现它的知识性和趣味性。我将围绕 Typescript、Node、React、Vue、GraphQL、Performance、Go 等等写更多的文章。

# 其他考虑

如果你使用的是 [eslint](https://eslint.org/) ，我相信我们大多数人都是这样，那么你可能需要禁用文件顶部的以下规则:

```
/* eslint-disable prefer-const */
/* eslint-disable @typescript-eslint/no-unused-vars */
/* eslint-disable @typescript-eslint/ban-ts-comment */
```