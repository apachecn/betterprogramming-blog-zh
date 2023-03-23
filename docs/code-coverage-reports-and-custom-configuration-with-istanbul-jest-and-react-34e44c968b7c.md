# 伊斯坦布尔、Jest 和 React 的代码覆盖报告和定制配置

> 原文：<https://betterprogramming.pub/code-coverage-reports-and-custom-configuration-with-istanbul-jest-and-react-34e44c968b7c>

![](img/ed3bb666468658101f706d76ac9d0203.png)

照片由[威廉·艾文](https://unsplash.com/@firmbee)在 [Unsplash](https://unsplash.com) 拍摄

几个月前，我遇到了独一无二的罗伯特“鲍勃叔叔”马丁(“干净的代码”、“干净的编码者”、“干净的架构”等)。人们向他提出的许多问题之一是，“我的应用程序中的哪些部分需要用单元测试来覆盖？”他的回答是，“嗯，只有你想工作的人。”

反响很大吧？

我写的代码越多，我就越相信 100%(或接近 100%)的行覆盖率阈值不是疯狂的，而是强制性的。好的覆盖率的最大好处之一是覆盖率报告对所有文件都是绿色的。

看到所有的绿色让人开心，他们倾向于保持绿色(即*)。*，添加新代码时添加新测试)。没有人想成为那种在文件变绿很长时间后又变红的人。

![](img/43655f968e84d5d83a86a930306cae47.png)

罗伯特·马丁和作者

所以从一开始就有代码覆盖报告和阈值是非常重要的。在本文中，我们在 React 应用程序上配置伊斯坦布尔，使用 Jest 作为测试运行程序。

# 我们开始吧

先说 React app。我们将使用脸书的 Create React App [模板](http://www.github.com/facebook/create-react-app)。

Create React App 不需要任何配置，只需一个命令即可创建一个非常简单的 React 应用程序:

```
npx create-react-app my-app-name
```

现在您有了 React 应用程序。更好的是:你在`src/App.test.js`有一个 Jest 测试。

这就是你尝试伊斯坦布尔的全部。如果您想要更多的数据，您可以添加更多的文件并为它们编写测试，或者您可以使用您现有的 React 项目。

# 建立伊斯坦布尔

好消息是伊斯坦布尔是一个笑话。这意味着配置由 Jest 直接处理。

这里的问题是，当您使用 Create React 应用程序模板创建应用程序时，默认情况下伊斯坦布尔不会运行。

当测试脚本被执行时，有两种方式运行伊斯坦布尔和 Jest。两者都很简单。

第一个是用一个`--coverage`标志运行您的测试。让我们添加一个名为`test:coverage`的新 npm 脚本，它将在运行测试后使用伊斯坦布尔生成覆盖报告:

```
//package.json
  {
    ...
    "scripts": {
      ... // test run   without coverage report      
      "test": "set CI=true && react-scripts test",

      // run tests in watch mode
      "test:watch": "react-scripts test",      // test run that generates coverage reports
      "test:coverage": "set CI=true 
                          && react-scripts test --coverage"  
    }
  }
```

这是向您的应用程序介绍伊斯坦布尔的最佳方式，因为您可以控制是否根据您使用的脚本生成报告。
您不希望总是创建报告的原因是性能——每次运行都生成报告可能会大大降低您的测试速度。
所以当你需要一个覆盖率报告的时候，你运行`npm run test:coverage`，但是当你不需要的时候，你使用`npm run test`，它有更快更便宜的执行。

打开伊斯坦布尔的第二种方法是使用默认为假的`collectCoverage`属性。

```
//package.json
  {
    ...
    "jest": {
      collectCoverage: true
    }
  }
```

这样做将使每个测试运行收集覆盖率并生成报告，这比使用`--coverage`标志要灵活得多，并且会对性能产生很大的影响。

我建议总是使用我们讨论的覆盖标志的第一种方式来设置伊斯坦布尔。这样，您将拥有一个完全灵活的配置，当您真正需要覆盖率报告时，它将允许您只支付收集覆盖率的开销。

# 自定义覆盖配置

伊斯坦布尔有许多属性，让您自定义您的覆盖报告。让我们来看看最有用或者最有趣的。

属性`collectCoverageFrom` 让你定义你想从哪些来源，更重要的是，不想从哪些来源收集报道。

你总是想排除`node_modules directory`，一些第三方代码，测试文件等等。使用`collectCoverageFrom`属性使配置变得非常简单。排除是用一个求反运算符完成的:`“!”`。

```
"jest": {
    "collectCoverageFrom": [
      "**/*.{js,jsx}",
      "!**/node_modules/**",
      "!**/coverage/**",
      "!**/serviceWorker.js",
      "!**/index.js"
    ],
}
```

属性`**coverageThreshold**` 将对覆盖率结果实施最小阈值。您可以对每个条目实施最小化伊斯坦布尔措施:分支、方法、行和语句。这里最酷的是，除了全局阈值之外，您可以为任何单独的目录甚至每个文件设置它们。

```
"jest": {
    "coverageThreshold": {
      "global": {  // global thresholds
        "branches": 80,
        "functions": 80,
        "lines": 80,
        "statements": 80
      },
      "./src/App.js": {  // file level thresholds
        "lines": 100
      }
    }
  }
```

还有很多 Jest 属性。你真的可以深入研究，做出最适合你的项目的配置。

# 结论

从长远来看，从一开始就拥有覆盖报告和阈值会有很大的不同。它将保持你的项目干净，持久，并对错误有弹性。

试图在项目生命周期的后期包含测试覆盖阈值、linters 和其他规则几乎总是以没有人尊重的失败而告终。

聪明点，一开始就做。