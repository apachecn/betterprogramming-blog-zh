# 我在我的 React + TypeScript 风格指南中添加了 8 个项目

> 原文：<https://betterprogramming.pub/8-items-i-added-to-my-react-typescript-style-guides-4885043fd2bc>

## TSX 文件名、功能组件声明等等

![](img/cbddb90451ef1a0005b2965d7822d6ef.png)

在 React 应用程序中包含编程风格指南会对开发过程产生很大的影响。它促进了结构感，并为初级开发人员提供了一种遵循的格式。如果做得好，你可以把你想看的东西包括进来，从而节省复习时间。

我想分享一些我过去在自己的风格指南中用过的东西。其中一些只是个人偏好，而另一些对性能和文件结构有影响。

如果你自己写的话，希望这个列表能给你一些启发。

# TSX 文件名

TSX 文件名应该是帕斯卡大小写。最好有一个文件命名的标准，以保持文件夹易于阅读。

做:

```
MyFile.tsx
```

不要:

```
myFile.tsxmy-file.tsxmy_file.tsxMy_File.tsx
```

# 功能组件声明

我更喜欢 const 声明，因为它们简洁明了。始终包含`FunctionComponent` 类型，以像子对象一样访问属性

做:

```
const MyComponent: React.FunctionComponent = () => {}
```

不要:

```
const MyComponent = () => {}function MyComponent() {}
```

# 指定出口

当一个组件是一个组件库的一部分，或者是具有许多类似导出的任何其他配置的一部分时，应该使用命名导出。

```
export const MyComponent1: React.FunctionComponent = () => {}export const MyComponent2: React.FunctionComponent = () => {}
```

# 默认导出

当组件只使用一次时，或者如果文件有一个没有与目录的其余部分捆绑在一起的导出，则应该使用默认导出。

```
const MyComponent: React.FunctionComponent = () => {}export default MyComponent
```

你可以在这里了解更多关于命名和默认导出[的区别。](/understanding-the-difference-between-named-and-default-exports-in-react-2d253ca9fc22)

# 使用状态命名

useState 变量应该具有描述性名称，更新函数名称设置为+ {variable name}

做:

```
const [name, setName] = useState("")
```

不要:

```
const [name, updateName] = useState("")
```

# 等式运算符

比较两个值时，始终使用`===`。在 JavaScript 中，`==`执行类型强制，这会导致非常奇怪的错误。比如:`1 == “1”`会等同于真。

做:

```
if (var1 === var2) {}
```

不要:

```
if (var1 == var2) {}
```

# 组件属性接口

当定义组件属性的接口时，总是先定义变量，再定义函数，用空格隔开。

做:

```
interface IProps { var1: string var2: boolean  function1(): void}const MyComponent: React.FunctionComponent = (props: IProps) => {}
```

不要:

```
interface IProps {   var1: string function1(): void var2: boolean}const MyComponent: React.FunctionComponent = (props: IProps) => {}
```

# 条件渲染

当使用简化方法进行条件呈现时，请使用完整的三元运算符，而不要使用逻辑&&。这种情况很少见，但是当满足某些条件时，局部&&操作符可以在 jsx 中返回奇数值。

做:

```
const MyComponent: React.FunctionComponent = () => { const authenticated = true return ( <div> {authenticated ? <p> You're Logged In </p> : null} </div> )}
```

不要:

```
const MyComponent: React.FunctionComponent = () => { const authenticated = true return ( <div> {authenticated && <p> You're Logged In </p>} </div> )}
```

你可以在这里了解更多关于条件渲染[的信息。](https://javascript.plainenglish.io/3-techniques-for-conditional-rendering-in-react-19a23a33423d)

如果有你觉得有用的，请告诉我。