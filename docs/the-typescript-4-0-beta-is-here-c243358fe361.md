# TypeScript 4.0 测试版在这里

> 原文：<https://betterprogramming.pub/the-typescript-4-0-beta-is-here-c243358fe361>

## 让我们探索一下 TypeScript 4.0 中令人兴奋的特性

![](img/e888dd422e64217d760d0499b6791de3.png)

照片由[凯勒·琼斯](https://unsplash.com/@gcalebjones?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在我之前的[文章](https://medium.com/better-programming/whats-new-in-typescript-3-9-70e3d2eabe26)中，我谈到了 TypeScript 版本 3.9 中的新特性。今天，我将窥视 2020 年 6 月 25 日发布的 TypeScript 4.0 测试版。在这个测试版中，我们第一次使用了 TypeScript4.0。虽然这带来了一个新的主要版本，但并没有比平常大得多的突破性变化。所以让我们来看看这个版本有什么新功能。

# 标记元组元素

这种语言特性改变了元组的定义方式。以前，元组定义如下:

```
function tuple(...args: [string, number]): void {
    // ...
}
```

在上面的例子中，第一个和第二个元素没有参数名。虽然这些对类型检查没有影响，但是元组位置上缺少标签会使它们更难使用——更难传达我们的意图。

作为对此的解决方案，TypeScrip 4.0 引入了带标签的元组。

```
type Segment = [length: number, count: number];
```

但是如果你要标记一个元素，你必须标记元组中的所有元素。如果没有，您将收到一个错误。

```
type Segment = [first: string, number];
//                         ~~~~~~
// error! Tuple members must all have names or all not have names.
```

# 从构造函数推断类属性

如果我们在`noImplicitAny`模式下配置 TypeScript，*TypeScript 4.0 可以使用控制流分析来确定类中属性的类型。*

*在以前版本的 TypeScript 中，上述代码将导致错误。但是 4.0 版本会编译，TypeScript 会推断出`x`的类型是`string` 还是`number`。*

# *短路赋值运算符*

*我相信您一定见过许多语言中的复合赋值运算符。复合赋值运算符将一个运算符应用于两个参数，然后将结果赋给左侧。以下是一些例子:*

*但是有三个明显的例外:逻辑*和* ( `&&`)、逻辑*或* ( `||`)以及无效合并(`??`)。TypeScript 准备在 4.0 版本中填补这些空白，他们有一个很有希望的提议，添加三个新的赋值操作符:`&&=`、`||=`和`??=`。*

```
*a ||= b;

// This will be equal to

a || (a = b);*
```

# *catch 子句绑定未知*

*从 TypeScript 开始，`catch`子句变量总是被类型化为`any`，这意味着 TypeScript 允许您对它们做任何您想做的事情。*

```
*try {
  throw 20;
} catch (err) {
  console.error(err.specialFunction()); 
}*
```

*在上面的例子中，错误的类型是`any`，因此它不是类型安全的。这就是为什么 TypeScript 4.0 现在让您将`catch`子句变量的类型指定为`unknown`。`unknown`比`any`更安全，因为它提醒我们在对值进行操作之前需要执行一些类型检查。*

# *定制 JSX 工厂*

*在 TypeScript 4.0 中，用户可以通过新的`jsxFragmentFactory`选项自定义片段工厂。例如，下面的`tsconfig.json`文件告诉 TypeScript 以与 React 兼容的方式转换 JSX，但是将每个调用切换到`h`而不是`React.createElement`，并使用`Fragment`而不是`React.Fragment`。*

# *使用`--noEmitOnError`提高 B `uild`模式下的速度*

*在以前的版本中，当使用`--noEmitOnError`标志时，在`--incremental`下有错误的前一次编译后编译程序会非常慢，因为上次编译的任何信息都不会基于`--noEmitOnError`标志缓存在`.tsbuildinfo`文件中。*

*在 TypeScript 4.0 中，这一点已经改变，现在您将在这些场景中获得巨大的速度提升。*

# *编辑器改进*

*TypeScript 编译器不仅增强了大多数主流编辑器中 TypeScript 本身的编辑体验，还增强了 Visual Studio 系列编辑器及其他编辑器中的 JavaScript 体验。你可以查看部分支持 TypeScript 的编辑器列表，了解你最喜欢的编辑器是否支持使用新版本。*

*此外，TypeScript 的编辑支持现在可以识别声明何时用`/** @deprecated *` JSDoc 注释标记。*

*启动时的部分编辑模式是 TypeScript 4.0 中包含的另一个实验性特性。他们说，这将是许多项目中加载时间缓慢的一个解决方案。这种新的编辑模式将提供部分体验，直到加载完整的语言服务体验。*

*目前唯一支持这种模式的编辑器是 [Visual Studio Code Insiders](http://code.visualstudio.com/insiders) 。您可以按照以下步骤进行尝试。*

1.  *安装 [Visual Studio 代码内部人员](http://code.visualstudio.com/insiders)。*
2.  *[配置 Visual Studio Code Insiders](https://code.visualstudio.com/docs/typescript/typescript-compiling#_using-newer-typescript-versions) 使用测试版或者为 Visual Studio Code Insiders 安装 [JavaScript 和 TypeScript Nightly Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-next) 。*
3.  *打开你的 JSON 设置视图:`> Preferences: Open Settings (JSON)`。*
4.  *添加以下几行:*

*`// The editor will say 'dynamic' is an unknown option, // but don't worry about it for now. It's still experimental. "typescript.tsserver.useSeparateSyntaxServer": "dynamic",`*

*上述功能只是我们在 TypeScript 4.0 中可以看到的大量更改中的一个子集。因为这是测试版，如果你愿意，TypeScript 邀请你用你的项目测试这个版本。如果这是你的计划，*

*   *你可以使用 npm `install typescript@beta`。*
*   *可以通过 NuGet 获得[。](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild)*

*所以试试吧，让他们知道你的反馈。*

*感谢阅读！*