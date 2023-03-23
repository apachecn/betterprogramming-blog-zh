# React 和 TypeScript:基本表单事件类型

> 原文：<https://betterprogramming.pub/react-and-typescript-basic-form-event-types-891459e16f12>

## 远离使用"`any"`

![](img/d24c24646105c042f115564801e974ff.png)

照片由[西格蒙德](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

如果您使用 React 和 Typescript 构建表单，您可能希望使用基本的 HTML 元素而不是库。如果您选择使用像`form`和`input`这样的基本元素，那么在监听它们的事件时使用正确的类型是个好主意。使用`any`很容易，毕竟`form`和`input`通常是人们在 web 开发中首先学习的东西。然而，尽可能少地使用`any`类型是一个好习惯。

下面是一些有用的类型，可以让你远离`any`的使用

**基本形式**

这里我有一个有两个功能的基本表单。一为表单`onSubmit`事件。另一个用于输入`OnChange`事件。

## 输入变化

从输入`onChange`事件中获取值是人们在使用 HTML 时首先要学习的事情之一。在 Typescript 环境中，很容易将`any`用作 onChange 事件的类型。可以用`React.ChangeEvent<HTMLInputElement>`代替`any`。

```
const onChange = (event: React.ChangeEvent<HTMLInputElement>) =>
```

这将使您能够访问输入 onChange 事件中包含的所有值。

如果您使用的是文本区域，并且需要区分事件，您可以改为使用:

```
const onChange = (event: React.ChangeEvent<*HTMLTextAreaElement>) =>*
```

## 选项选择

选项选择类似于文本输入。如果你需要用你的选择做一些定制逻辑，你可以使用`React.ChangeEvent<HTMLSelectElement>`。

```
const onChange = (event: React.ChangeEvent<HTMLSelectElement*>) =>*
```

## 表单提交

与`onChange`类似，大多数开发人员都知道，要阻止表单执行默认提交，需要调用`event.preventDefault()`。表格提交使用的类型是`React.FormEvent<HTMLFormElement>`

```
const onSubmit = (event: React.FormEvent<HTMLFormElement>) =>
```

## **最终视图**

避免在函数属性类型中使用`any`是一个很好的实践。希望这些例子能帮助你在自己的代码中摆脱一些容易犯的错误。