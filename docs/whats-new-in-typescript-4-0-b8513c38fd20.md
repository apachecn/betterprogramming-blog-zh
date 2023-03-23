# TypeScript 4.0 中的新增功能

> 原文：<https://betterprogramming.pub/whats-new-in-typescript-4-0-b8513c38fd20>

## 让我们探索一下最新版本的 TypeScript

![](img/29766f5f853fc25df8aeb4334fd799cb.png)

[安德鲁·尼尔](https://unsplash.com/@andrewtneel?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

TypeScript 今天(2020 年 8 月 20 日)宣布了它的最新版本，我想快速回顾一下它的特性和变化。在这个版本中，TypeScript 带来了他们最新更新的最终稳定版本。虽然这带来了一个新的主要版本，但并没有比通常更大的突破性变化。所以让我们来看看这个版本有什么新功能。

# 标记元组元素

这种语言特性改变了元组的定义方式。以前，元组定义如下:

```
function tuple(...args: [string, number]): void {
    // ...
}
```

在上面的例子中，第一个和第二个元素没有参数名。虽然这些对类型检查没有影响，但是元组位置上缺少标签会使它们更难使用——更难传达我们的意图。(来源:[devblogs.microsoft.com](https://devblogs.microsoft.com/typescript/announcing-typescript-4-0/#labeled-tuple-elements))

作为对此的解决方案，TypeScrip 4.0 引入了带标签的元组。

```
type Segment = [length: number, count: number];
```

但是如果你要标记一个元素，你必须标记元组中的所有元素。否则，您将收到一条错误消息。

```
type Segment = [first: string, number];
//                         ~~~~~~
// error! Tuple members must all have names or all not have names.
```

# 从构造函数推断类属性

如果我们在`noImplicitAny`模式下配置 TypeScript，*TypeScript 4.0 可以使用控制流分析来确定类中属性的类型。*

```
*class Test {    
   x;    
   constructor(b: boolean){      
     if(b){        
       this.x = 'hello'      
     } else {        
       this.x = 42;      
     }    
   }
}*
```

*在以前版本的 TypeScript 中，上述代码将导致错误。但是 4.0 版本会编译，TypeScript 会推断出`x`的类型是`string` 还是`number`。*

# *短路赋值运算符*

*我相信您一定见过许多语言中的复合赋值运算符。复合赋值运算符将一个运算符应用于两个参数，然后将结果赋给左侧。以下是一些例子:*

*根据 Typescript 团队说法，*

> *但是有三个明显的例外:逻辑*和* ( `&&`)、逻辑*或* ( `||`)以及无效合并(`??`)。TypeScript 准备在 4.0 版本中通过引入三个新的赋值操作符来填补这些空白:`&&=`、`||=`和`??=`。(来源:[devblogs.microsoft.com](https://devblogs.microsoft.com/typescript/announcing-typescript-4-0/#short-circuiting-assignment-operators))*

```
*a ||= b;// This will be equal toa || (a = b);*
```

# *“catch”子句绑定上的“unknown”*

*从 TypeScript 开始，`catch`子句变量总是被类型化为`any`，这意味着 TypeScript 允许您对它们做任何您想做的事情。*

```
*try {
  throw 20;
} catch (err) {
  console.error(err.specialFunction()); 
}*
```

*在上面的例子中，错误的类型是`any`，因此它不是类型安全的。Typescript 4.0 对此有一个解决方案，*

> *TypeScript 4.0 现在允许您将`catch`子句变量的类型指定为`unknown`。`unknown`比`any`更安全，因为它提醒我们在对值进行操作之前需要执行一些类型检查。(来源:[devblogs.microsoft.com](https://devblogs.microsoft.com/typescript/announcing-typescript-4-0/#unknown-on-catch))*

# *使用'- noEmitOnError '提高了构建模式的速度*

*在以前的版本中，当使用`--noEmitOnError`标志时，在前一次编译后编译一个在`--incremental`下有错误的程序会非常慢，因为上次编译的任何信息都不会基于`--noEmitOnError`标志缓存在`.tsbuildinfo`文件中。*

*在 TypeScript 4.0 中，这一点已经改变，现在您将在这些场景中获得巨大的速度提升。*

# *- incremental '带'- noEmit '*

*TypeScript 4.0 允许在仍然利用`--incremental`编译时使用`--noEmit`标志。这在以前是不允许的，因为`--incremental`需要发出一个`.tsbuildinfo`文件。*

# *编辑器改进*

*Typescript 已经成为许多编辑的好伙伴，随着新版本的发布，他们已经拓宽了他们的限制。*

> *TypeScript 编译器不仅增强了大多数主流编辑器中 TypeScript 本身的编辑体验，还增强了 Visual Studio 系列编辑器及其他编辑器中的 JavaScript 体验。(来源:[devblogs.microsoft.com](https://devblogs.microsoft.com/typescript/announcing-typescript-4-0/#editor-improvements))*

*您可以查看部分支持 TypeScript 的编辑器列表，了解您最喜欢的编辑器是否支持使用新版本。*

*此外，TypeScript 的编辑支持现在可以识别声明何时用`/** @deprecated *` JSDoc 注释标记。*

*启动时的部分语义模式是 TypeScript 4.0 中已经包含的另一个实验性特性。他们说，这将是许多项目中加载时间缓慢的一个解决方案。这种新的编辑模式将提供部分体验，直到加载完整的语言服务体验。通常情况下，一个项目需要 20 到 60 秒，直到 TypeScript 完全响应，而这种新模式会将时间减少到几秒钟。*

*目前，唯一支持这种模式的编辑器是[Visual Studio Code Insiders](http://code.visualstudio.com/insiders)。您可以按照以下步骤进行尝试。*

1.  *安装 [Visual Studio 代码内部人员](http://code.visualstudio.com/insiders)。*
2.  *[配置 Visual Studio Code Insiders](https://code.visualstudio.com/docs/typescript/typescript-compiling#_using-newer-typescript-versions) 使用测试版或者为 Visual Studio Code Insiders 安装 [JavaScript 和 TypeScript Nightly Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-next) 。*
3.  *打开你的 JSON 设置视图:`> Preferences: Open Settings (JSON)`。*
4.  *添加以下几行:*

*`// The editor will say 'dynamic' is an unknown option, // but don't worry about it for now. It's still experimental. "typescript.tsserver.useSeparateSyntaxServer": "dynamic",`*

*除了上述特性，TypeScript 4.0 还包含许多新特性，如自定义 JSX 工厂、Varadic 元组元素等，你也可以在官方网站上找到它们。另外，如果您想在项目中测试这个最新版本:*

*   *可以用`npm install -D typescript`。*
*   *你可以通过 [NuGet](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild) 得到它。*