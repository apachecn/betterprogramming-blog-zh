# 让您的 TypeScript 代码经得起未来考验的 8 个最佳实践

> 原文：<https://betterprogramming.pub/8-best-practices-for-future-proofing-your-typescript-code-2600fb7d8063>

## 确保您的类型脚本代码经得起时间的考验

![](img/ae4f6a2bf7f98b3a82e9105d7999fdbb.png)

照片由[妮可·沃尔夫](https://unsplash.com/@joeel56?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

无论您是使用 JavaScript 构建库、前端还是后端应用程序，切换到 [TypeScript](https://www.typescriptlang.org/) 这一小小的努力已经是为您的代码提供未来保障的一大步。

如果您不同意，我建议您至少在一些经常调用的代码上尝试一下，并通过错误报告工具比较未捕获的异常前后的情况。

当您向代码库中添加类型时，您可能会注意到由于逻辑错误、对潜在可空变量的未经检查的访问、有太多问题(输入/输出参数)的函数等导致的死代码，这将立即有助于减少代码中的问题数量。

此外，它将使重构您的代码更加容易，有助于未来的开发并减少技术债务——因为现在，IDE(例如[VS](https://code.visualstudio.com/)Code)知道您的代码使用什么类型，它可以帮助重命名、拆分、移动代码，以及显示未使用的函数。

然而，有几个低成本的技巧可以实现，这将极大地帮助您使您的代码更加经得起未来考验，尤其是如果您计划在团队中处理您的代码，或者如果您的代码不仅仅是一个原型。

# 1.打开严格检查

默认情况下，TypeScript 不使用它的`strict`模式。如果您还不想完全提交给 TypeScript(例如，只将您的 JavaScript 文件的一部分迁移到它)，这可能很好，但这也意味着许多常见的 JavaScript 错误不会被 TypeScript 捕获。

发生这种情况是因为 TypeScript 不希望您为所有内容添加类型定义(而是将参数和变量标记为`any`，并允许您将`null`或`undefined`赋给任何类型，以及其他一些规则(查看[文档](https://www.typescriptlang.org/docs/handbook/compiler-options.html))。

只需打开项目的`tsconfig.json`中的`strict`标志，就可以大大提高代码的稳定性。必须显式地键入所有内容将会改进代码的文档，允许您更容易地推理代码的期望。

这将有助于 TypeScript 编译器帮助您，因为它可以指出由于缺少空检查或参数/变量的其他潜在值而导致的潜在异常。

许多不使用 TypeScript，或者不为任何事情添加类型的开发人员倾向于认为他们不需要冗长的类型，因为他们的代码已经足够好了。

但是，至少要考虑帮团队中的其他开发人员一个忙，让他们有可能使用 IDE 的更多自动化重构功能，而不是浪费时间在繁重的工作上。

尤其是因为您仍然可以配置 TypeScript 来发出 JavaScript 代码，而不会被项目中缺少的类型所阻止，并将缺少的类型视为技术债务，随着时间的推移将会消除。

# 2.用巴别塔代替 tsc

从版本 7 开始， [Babel](https://babeljs.io/) 增加了对 TypeScript 的支持，这意味着你不再需要使用 TypeScript 编译器`tsc`来构建你的项目，而是可以使用 Babel，它只是简单地从所有的 TypeScript 文件中剥离你的类型，然后以 JavaScript 的形式发出结果。

这不仅比`tsc`快得多，尤其是在更大的项目中，而且允许您在您的项目中使用整个巴别塔生态系统。

对于后端项目，这意味着您可以简化笨重的文件查看脚本，只需使用[巴别塔节点](https://babeljs.io/docs/en/babel-node)来查看更改:

```
nodemon — inspect=0.0.0.0:9229 — exec babel-node — extensions ‘.ts,.tsx’ src/index.ts
```

对于项目，例如在使用 [GraphQL](https://graphql.org/) 或其他非 TypeScript 特性时，这意味着您可以通过 Babel 轻松添加它们，并且您的 TypeScript 构建管道不会中断或需要更改:

```
{
  "plugins": ["import-graphql"]
}
```

当然，对于使用用 TypeScript 构建的命令行工具的项目，您将不再需要在运行之前先构建 [ts-node](https://github.com/TypeStrong/ts-node) 或步骤，而是直接通过 babel-node 运行您的 CLI。

负面影响很小。Babel 的 TypeScript 实现不支持 TypeScript 的 const enums(这听起来比实际情况更严重)，如果你真的想对所有文件进行检查，你仍然需要在 commit/CI 管道中使用`tsc`,因为 Babel 不解释 TypeScript。

# 3.锁定您的版本

如果您还没有，开始将您的版本固定在您的`package.json`中。即使您有一个锁文件，也要把您的`package.json`看作是您的项目所依赖的人类可读的事实。

对于 TypeScript 项目尤其如此。尽管一些 JavaScript 项目更新了它们的依赖项的补丁版本，但是只有在那时你才会注意到这个包的维护者并没有真正遵循 [SemVer](https://semver.org/) 或者引入了一个 bug，所有的 TypeScript 开发人员，在某个时候，都会因为更新了他们的`@types`类型定义的补丁版本，然后注意到许多新的 TypeScript 错误而遭受损失。

这通常是由于第三方依赖关系的许多第三方类型随着时间的推移而演变，有时会以不可预见的方式变得更加严格。

例如，仅允许参数上下文的 [Koa](https://koajs.com/) 定义或仅允许在上下文上使用用户对象的 Express 定义，因为这些键在它们的接口上被设置为`any`，在几十个路由中突然失败，因为已经添加了正确的类型。

尽管 TypeScript 及其生态系统已经成熟，并将继续成熟，但由于越来越多的开源库直接采用 TypeScript，升级`@types`仍然是一个痛苦。

对于 TypeScript 来说更是如此，就像您希望已经完成的其他依赖项升级一样，考虑使用诸如 [Greenkeeper.io](http://Greenkeeper.io) (或者[renewal](https://github.com/renovatebot/renovate)，或者您的 CI 提供的任何东西)之类的工具来自动升级一个单独分支中的单个依赖项，使您的项目更容易更新。

永远不要考虑在周五晚上更新这个小小的`@types/`包的补丁版本。糟糕的周末就是这样开始的。

# 4.不透明类型 FTW

在您的许多项目中，您将最终使用许多字符串类型的参数，而很少使用其他类型的参数，这使得 TypeScript 很难在您分配错误的变量时警告您:

```
const trackLogin = (currentDate: string, sessionId: string) => { someCode(); };
const sessionUuid: string = currentSession.getUuid();
const currentDate: string = (new Date()).toISOString();
trackLogin(sessionUuid, currentDate);
```

您可以很容易地防止这种情况，同时也使得将来重构代码更加容易。只需定义和使用所谓的不透明类型，它可以是项目所需的粒度:

```
type Opaque<K, T> = T & { __TYPE__: K };
type Uuid = Opaque<"Uuid", string>;
type DateISOString = Opaque<"DateISOString", string>;
```

实用函数`Opaque<K, T>`简单地定义了一个新类型，除了变量值之外，它还存储一个(唯一的)键，比如`Uuid`或`DateISOString`。

这就允许 TypeScript 区分不同的类型，即使所有类型仍然存储普通字符串并且不改变编译器的输出。

这意味着您现在可以添加尽可能多的具有唯一名称的字符串类型，它们仍将作为字符串值传递，但在顶部添加了类型验证:

```
const trackLogin = (currentDate: DateISOString, sessionId: Uuid) => { someCode(); }
const sessionUuid = currentSession.getUuid() as Uuid;
const currentDate = new Date().toISOString() as DateISOString;trackLogin(sessionUuid, currentDate);
// Your IDE / TypeScript will now understand this function call and show an error
```

# 5.使用实用程序类型

除了不透明类型之外，还要学习和使用其他实用程序类型——它们会让你的代码更容易理解。

TypeScript 已经内置了一些实用程序类型，例如`Partial<T>`，它使得`T`的所有属性都是可选的，或者`Readonly<T>`，它使得`T`是只读的。(还有其他第三方实用程序类型的 [npm](https://www.npmjs.com/) 包可用。)

当您开始使用它们时，您很快就会注意到，您的代码中冗余/重复的 TypeScript 类型会更少，定义更窄的类型会更多。

例如，如果你是一个 React 开发人员，对于一个 [Redux](https://redux.js.org/) reducer，你可以简单地将一个 reducer 的输入状态定义为`Readonly<T>`，以便更容易地查看你是否无意中覆盖了它，而不必添加另一个只读状态类型。

然而，它们不仅有助于减少类型重复；它们还可以帮助你不要导出太多的接口，保持代码清晰。

坚持 React 示例，您可能想要为 React 组件属性或动作创建者返回值定义导出的接口。

但是，如果您认为接口是样板文件，并且您的代码实际上是关于 React 组件的，那么您可以简单地使用`React.ComponentProps<T>`以一种直接的方式获得组件的属性(这适用于基于类和基于函数的组件)，或者使用`ReturnType<T>`获得动作创建者的类型。

顺便提一下，如果从代码领域的角度来看有意义，那么只尝试将接口或类型分解成更小的嵌套接口/类型。一旦它们被积极地分开，就很难看到结构，尤其是在使用代码完成时。

有时，将一个接口放在一起并通过方括号引用它要容易得多，例如，`IUser["parents"]`可以是引用一个存储另外两个`Users`的`parents`值对象的简单方式，而不是引入一个一次性的`IParents`，后者可能在一周后变得过于模糊。

总之，拥有尽可能多的接口并不是一种竞争，总是要考虑接口或类型在您的产品领域中意味着什么，并在必要时复制，但不要在您的 IDE 需要它时复制。

# 6.使用更漂亮的

在任何多人工作的代码库中，或者同一个人长时间工作的代码库中，编码风格都是不一致的。

即使你同意古老的“制表符对空格”之争，有些人可能对多行函数调用的缩进方式不同，而有些人编写 JSON 对象的方式不同，等等。

这不仅会导致浪费时间讨论哪种风格更好，或者哪种风格与过于模糊或过于复杂的公司风格指南相匹配；而且还会在合并请求中导致无用的差异，使查看实际更改的代码变得更加困难。

你可以简单地使用[更漂亮的](https://prettier.io/)来格式化你的代码。它非常固执己见并且(幸运的是！)没有提供很多浪费时间的配置选项，它可以完美地处理许多文件格式，例如 HTML 以及 JSON、CSS 和 TypeScript。

你可以设置你的 IDE(比如 VS 代码)在你保存文件时自动使用它，这实际上是更漂亮的最大好处——你将不再在缩进、行长度等方面花费宝贵的编码时间。相反，你可以不停地打字，漂亮会变魔术。

我强烈建议你使用现成的更漂亮的，不要试图改变它。你根本不需要修改你的编码风格，因为 Prettier 会自动格式化你的代码。

你可能需要做的就是调整你阅读代码的方式。坦率地说，这只需做一次，比通读一个函数并看到三个不同的人的五种不同的编码风格要容易得多。

*我允许的一个配置更改是使用 ES5 风格的尾随逗号:

```
{
  "trailingComma": "es5"
}
```

当您在对象末尾添加新元素时，这将减少合并请求中的差异，从而减少合并冲突。

# 7.使用对象作为函数的有效负载

对于某些函数，只传入一个对象而不是单独的输入变量可能非常有益，这将极大地简化以后对函数签名的更改。

传统上，应用该规则的函数是 Redux 动作创建者:

```
const sendMail = (type: string, subject: string, body: string, to: string, attachment?: File) => {
  return {
    payload: {
      attachment, body, subject, to
    },
    type,
  };
};
```

对比:

```
const sendMail = (type: string, payload: { subject: string, body: string, to: string, attachment?: File }) => {
  return {
    payload,
    type,
  };
};
```

当您现在必须添加一个新参数时，例如我们示例中的电子邮件收件人的`userId`，在第一种表达函数的方式中，您必须更改函数签名，并随之更改所有被调用的 React 组件/分派函数。

如果你只是传递一个对象，不仅在动作创建器中表达返回对象更容易，而且你也不必担心输入参数的顺序，还有一个可选的参数。

这也适用于 Express 应用程序或类似层中的控制器方法，在这种情况下，您可能最终会稍微更改您的域代码。

# 8.使用 ESLint 和 SonarJS

就像漂亮对于你的编码格式是多么重要一样，每个项目都应该使用 [ESLint](https://eslint.org/) 作为他们的编码标准，还有 ESLint 插件 [SonarJS](https://www.sonarsource.com/products/codeanalyzers/sonarjs.html) 。

( [TSLint](https://palantir.github.io/tslint/) 正被弃用，取而代之的是[typescript-eslint](https://github.com/typescript-eslint/typescript-eslint)；TSLint 插件 SonarTS 已经被采用，现在是 SonarJS 的一部分。

在推荐的设置中，ESLint 有许多规则可以提高你的代码质量，帮助你避免常见的错误。

即使对于高级程序员来说，提醒他们 getter 没有返回值，或者变量在当前作用域中没有声明也是有帮助的；我们都会犯错。

除了 ESLint 的特性之外，SonarJS 还为您的代码添加了一些复杂性检查，这有助于您先编写代码，然后将您的方法分成更小的部分

这在重构或向现有功能添加新需求时也是一个很大的帮助，并且不会意外地导致过于复杂的功能。