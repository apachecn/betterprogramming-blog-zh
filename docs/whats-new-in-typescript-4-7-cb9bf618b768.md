# TypeScript 4.7 有什么新功能？

> 原文：<https://betterprogramming.pub/whats-new-in-typescript-4-7-cb9bf618b768>

## NodeJs ESM 支持终于来了

![](img/cd4f66eee67e52f29bd1ae73912f2647.png)

作者图片

打字稿`4.7`版本计划于 5 月 24 日发布🎉。在一个不那么令人兴奋的 TypeScript 4.6 版本之后，我们要享受一下了。

众所周知，该团队专注于 NodeJs 集成。它对 Node 上的`ES Modules`的支持是一个期待已久的特性。它是打着实验的旗号在`4.5`版本上发布的。为什么？许多零碎的东西需要擦亮。秘密发布是开始收集反馈和问题的绝佳方式。

最后，在这个新版本中，NodeJs 集成作为一个稳定的特性发布。这不是我唯一感到兴奋的特性。由于它的`moduleSuffixes`特性，如果你是 React 原生开发者，这个版本是非常棒的。

在本文中，我将重点介绍最相关的新特性。一如既往，你可以在这里玩新版本。

# Node.js 中的 ECMAScript 模块支持

Node.js 的模块基础一直是 CommonJS。随着向多态应用程序的转变，支持 ECMAScript 模块的呼声越来越高。在过去的几年里，Node.js 一直致力于支持这些。从 Node 12 版本开始，对 ES 模块的支持已广泛提供。

现在有两个新的`module`配置设置:`nodenext`和`node12`。

```
{
  "compilerOptions": {
    "module": "nodenext"
  }
}
```

## package.json 中的“类型”属性

通过`package.json`文件，我们现在可以配置节点将用于文件的模块格式。

```
{
    ...
    "name": "my package",
    "type": "module",
    ...
}
```

有哪些选择？我们可以在用于`ES Modules`的`module`或用于传统 CommonJS 模块的`commonjs`之间进行选择。

这种行为适用于所有不带扩展名的常规 JavaScript 文件。TypeScript 将通过它的`.ts`文件包含相同的系统行为。

它在实践中是如何工作的？当编译器找到一个`.ts/.tsx/.js/.jsx`时，它会查看最近的`package.json`文件来确定它的模块风格。

## 通过文件扩展名进行配置

我们已经看到了默认行为。单个文件如何确定自己的模块系统？只要改变它的文件扩展名。

让我们看看新的约定:

*   `.cjs/.cjx`:不管最近的父`"type"`配置如何，文件将以 CommonJS 格式导入。
*   `.cts/.ctx/.d.cts`:文件将以 CommonJS 格式导入，不考虑父`"type"`规格。当输出文件时，它会输出相应的`.`，让我们看看现在可以使用什么扩展名:`mjs`、`.mjx`或带有`.d.cts` 扩展名的声明。
*   `.mjs/.mts/.mtx/d.mts`:文件将以 ECMAScript 模块格式导入，不考虑父`"type"`规范。当发出文件时，它将输出其对应的`.mjs`、`.mjx`或扩展名为`.d.mts`的声明。

## 互用性

假设现在您可以通过调优它的文件系统来导入不同的模块类型，那么它们需要协同工作。ES 模块很简单，因为它只是一个翻译的问题。对于要导入 CommonJS 的 ES 模块，它会将它们视为默认导出。

这里有一个例子:

## package.json 中的“exports”属性

我们之前已经看到了如何在`package.json`文件中添加一个`type`来配置模块默认值。同样，还有一个新的属性可以导出包'`exports`。

有了这个属性，我们就可以微调暴露文件的方式。代码如下:

在上图中，我们可以看到:

*   我们如何定义`ESM`或`CommonJS`的特定导入
*   我们如何在这些定义中定义特定于类型脚本的定义
*   我们如何为旧版本定义回退

## ES 模块集成的收获

es 模块带来了哪些特性？需要注意什么？让我们来看一个摘要列表:

*   功能:使用`import/export`语句语法
*   特性:顶层等待可用`nodenext`
*   互操作:一些像`require`这样的全局关键字在 ES 模块上不起作用

# 控制模块检测

在 JS 生态系统中，文件是一个模块还是一个脚本总是有点模糊。出于这个原因，引入了一个新的编译器选项:`moduleDetection.moduleDetection`。它让我们配置如何解析模块。

它可以取这三个值中的一个:

## 1.汽车

这是来自`4.7`的新默认值。TypeScript 将查找`imports`和`exports`语句，但也会检查:

*   在`package.json`中归档的`type`
*   如果文件在`--jsx react-jsx`下运行

## 2.遗产

该值的行为与以前的 TS 版本相同。它查看`import`和`exports`语句来检查文件是否是模块。

## 3.力

这种模式将强制每个文件被视为一个模块。对于`module`、`moduleResolution`和`jsx`的配置，所有非申报文件将被单独处理。

当只针对浏览器时，最好有`moduleDetection: force`和`module: esnext`。在这种情况下，我们不需要`commonJs`模块。

# 使用`moduleSuffixes`进行分辨率定制

使用 React-Native 多态应用程序，我们可以通过更改文件扩展名来选择在每个平台上加载哪个文件。

*   `index.native.ts`:将加载到 RN 捆扎机中
*   `index.ts`:将由卷筒纸捆扎机使用

然而，在使用 TypeScript 时有一个警告。TypeScript 编译器不会知道`.native.ts`的存在。现在可以通过使用`moduleSuffixes`编译选项来实现。

```
{
    "compilerOptions": {
        "moduleSuffixes": [".ios", ".native", ""]
    }
}
```

注意`""`需要查看默认的`ts`文件。

所有的`React-Native`开发者肯定会为这个新特性欢呼雀跃，并争相升级。

# *实例化表达式*

有时我们想做一些简单的事情，比如从一个通用函数创建一个更具体的函数。目前，这可以通过函数包装器来实现。然而，在这个新的`4.7`版本中，我们可以很容易地从通用函数中创建一个别名。这里有一个例子:

现在，您可以像下面这样设置酷类型别名:

这是一个简单而整洁的功能，它将为许多新类型的模式打开大门。

# 计算属性的控制流分析

此版本中包含了对计算属性的新改进。TypeScript 现在将在更广泛的情况下考虑类型保护。

这里有一个例子:

另一个伟大的事情是`-strictPropertyInitialization`将能够包括那些场景检查。

# 包裹

综上所述，这是一个伟大的。在`Node`中对`ESM`模块的支持是一个棘手的问题，很高兴看到它的出现。这并不意味着这个版本没有更多值得喜欢的地方。

还有一些不错的特性，如`groups-aware`、组织导入或对象方法片段完成。

干杯！

## 想多读点？

[](/7-typescript-common-mistakes-to-avoid-581c30e514d6) [## 要避免的 7 个打字稿常见错误

### 通过避免这些错误来改进您的 TypeScript 的代码库

better 编程. pub](/7-typescript-common-mistakes-to-avoid-581c30e514d6)