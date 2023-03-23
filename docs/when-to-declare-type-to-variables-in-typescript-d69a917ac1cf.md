# 何时在 TypeScript 中声明变量的类型

> 原文：<https://betterprogramming.pub/when-to-declare-type-to-variables-in-typescript-d69a917ac1cf>

## 我们来谈谈类型推断

![](img/ff30c4019c4afd332cc87a03c16ab799.png)

劳拉·奥克尔在 [Unsplash](https://unsplash.com/s/photos/future?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

我觉得这是 TypeScript 初学者最常问的问题之一。当我看到每个人都为每个变量声明类型时，我发现自己也在问这个问题。这似乎不太对，所以我直接去了官方的打字稿文档来寻找答案。

typescript 中有一个称为类型推断的特性，它通过变量值来猜测类型。

例如，您为字符串、数字、对象、数组和函数手动键入类型注释，如下所示:

```
const animal: string = ‘cat’const count: number = 5count animalObj: {} = { name: ‘cat’, count: 1}const animals: string[] = [‘cat’, ‘dog’, ‘bird’]const logAnimal: (i: number) => void = (i: number) => console.log(‘dog’)
```

现在，尝试删除类型并将鼠标悬停在变量上。你会发现 TypeScript 还是能告诉你是什么类型的！

# 何时在 TypeScript 中向变量声明类型？

答案是当 TypeScript 能够进行类型猜测时。我们只是尽可能地依赖它。当我们不需要的时候，写不必要的字符是没有意义的。

但是在另外两种情况下，我们需要手动添加类型。

## 返回任意类型的函数

当 typescript 搞不清楚类型推断时，它将只使用任何类型。例如，因为`JSON.parse`可以返回任何类型，所以变量`result`可以是任何类型:

```
let result = JSON.parse(‘4’)result = JSON.parse(“{value: 123}”)console.log(result)
```

为了避免`result`上的任何类型，这可能会删除我们代码库中的错误，我们可以手动声明类型:

```
const result: { value: number } = JSON.parse(“{value: 123}”)
console.log(result.someOtherProp) // ts now throw error which is what we wanted
```

当我们使用不是用 typescript 编写的库中的方法时，经常会发生这种情况。

## 在多行中为变量赋值

当你只在一行中给一个变量赋值时，类型推断起作用。让我们尝试在一个变量上分配多行值:

```
const arr = [1,2,3]let twoExistedtwoExisted = true
```

试着悬停在上面的两个变量上。TypeScript 知道`arr`是数字`[]`，但是`twoExisted`是任意类型，尽管它已经被重新分配给`true`。我们必须这样做:

```
let twoExisted: boolean
twoExisted = true
```

当我们想要对来自外部源的数据使用临时变量时，比如来自 API 调用的数据，就会发生这种情况。