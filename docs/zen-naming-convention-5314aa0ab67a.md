# 禅宗命名惯例

> 原文：<https://betterprogramming.pub/zen-naming-convention-5314aa0ab67a>

## 一组灵活的命名建议，您可以根据需要进行修改

![](img/6b05826f1263596e1460e8d9bfddb03d.png)

照片由[aurélien——Unsplash](https://unsplash.com/@wildspot?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)[上的野生斑点](https://unsplash.com/search/photos/zen?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

关于正确的命名约定还没有定论，但是如果你想从一组灵活的建议开始，并根据你的需要进行修改，我也许可以帮助你。

本指南中的代码是用 JavaScript 编写的，但这些原则可以普遍适用。

# 1.别担心，开心点

```
const doSomething = stuff => {}
```

上面的功能是我的一种仪式。这些年来，我逐渐意识到在我真正需要命名一个函数之前，我需要一个函数，所以我经常从这里开始。

郑重声明，这是准确的。我得做点什么来填充。这个函数的名字告诉我这个函数是做什么的，同时也告诉我要尽快重命名它。

大多数情况下，我会在调用这个函数之前对它进行重命名，但有时，我发现自己在最后一刻解决了一个棘手的问题，我需要尽我所能保留所有的脑力。

我要说明的要点是，我从来不会因为我必须为一个变量选择正确的名称而失去思考的节奏，你也不应该这样。

当今大多数现代的 ide 都是为了在整个项目中安全地重命名变量而构建的，如果您想提高自己在命名约定方面的天赋，您应该尽早并经常使用该工具。

# 2.越平越好

```
*const* THEME_BORDER_COLOR = '#999'
*const* THEME_BORDER_STYLE = 'solid'
*const* THEME_BORDER_WIDTH = 1
*const* THEME_MARGIN_BOTTOM = 20
*const* THEME_MARGIN_TOP = 20
*const* THEME_PADDING = 40

*const* theme = {
  border: {
    color: '#999',
    style: 'solid',
    width: 1,
  },
  margin: {
    top: 20,
    bottom: 20,
  },
  padding: 20,
}
```

平面变量可以由您的 IDE 索引，这使得它们更容易在整个项目中使用。

另外，看一下上面的例子。如果我想从一个单独的`PADDING`变量变成`PADDING_TOP`、`PADDING_BOTTOM`等等，我可以通过简单地创建新的变量，用扁平结构很容易地做到这一点。

然而，在嵌套结构中，我需要将`theme.padding`从一个值改为一个嵌套更深的对象。随着项目的增长，以这种方式不断地重塑你的变量树肯定会给你带来麻烦，而扁平结构喜欢伸缩。

# 3.避免缩写

```
let i = 0  ->  **let index = 0** let j = 0  ->  **let index2 = 0**
```

上面例子的第一部分看起来足够无辜了吧？用`i`做索引变量有什么问题？

问题是，当您需要范围内的另一个索引变量时，您会倾向于使用`j`。

这是因为，尽管`j`是一个自然进程，但你现在错误地暗示这些变量是一个序列的一部分，而`i`实际上是`index`的简称，`j`代表`index2`。

选择稍微宽一点的代码，给自己起一个更具描述性的名字。如果您正确地命名了变量和函数，那么您需要的内联文档就会少得多，因为在某种程度上，代码会自己编写文档。

```
initAuth  ->  **initializeAuthentication** or **initiateAuthorization**
```

我真的很不喜欢前缀`init`，因为很难判断函数是启动了什么还是初始化了什么。我也避免使用缩写`auth`,因为它可能适用于认证或授权。

看上面的例子，如果您选择一个粗体名称，您不需要额外的文档来清楚地说明这些区别。

# 4.语法一致

在英语语法中，有四种句子结构:

*   声明性:“项目延期。”
*   命令式:“告诉我为什么。”
*   疑问:“你确定你想知道？”
*   感叹:“是啊！”

以下是代码中的这些结构:

*   声明性:`userIsAuthenticated`(变量)
*   命令式:`authenticateUser`(功能)
*   疑问:`isUserAuthenticated`(不要用)
*   感叹号:`AUTHENTICATION_ERROR`(常量)

陈述句陈述信息，所以对变量使用陈述句。

因为祈使句能促使行动，所以把它们用作功能。

感叹句实际上并不存在于代码中，但认为常量属于这一类。

最后，代码中的疑问结构可能会令人困惑，所以要避免使用它们。

# 5.名字的前缀/后缀

## **布尔型**

帮助您清理命名约定的最有效的技巧之一是为特定的变量组保留某些前缀和后缀。

例如，我喜欢给我所有的布尔值加一个前缀:`is`、`can`或`should`。

```
let active = false  ->  **let isActive = false** let load = false    ->  **let canLoad = false** let render = false  ->  **let shouldRender = false**
```

通过确保所有布尔变量都是前缀中表示的子组的一部分，我在调试时有了更多的信息来指导我。

## 数组

有一个关于命名数组的共同协议；我们似乎都同意数组应该是多元的。我发现这种方法有一个弱点。

看看这个例子:

```
*const* exchangeRates1 = {
  EUR: {
    CNY: 7.84,
    GBP: 0.91,
    INR: 79.42,
    USD: 1.11,
  },
  USD: {
    CNY: 7.06,
    EUR: 0.90,
    GBP: 0.82,
    INR: 71.51,
  },
}

*const* exchangeRates2 = {...}
*const* exchangeRates3 = {...}

*const* exchangeRates = [
  exchangeRates1,
  exchangeRates2,
  exchangeRates3,
]
```

数据表明名为`exchangeRates{n}`的对象是值的集合，这很好。但是，我们的集合数组被命名为`exchangeRates`，没有`n`值，当你只看名字的时候，这可能会令人困惑。

这个变量是否只代表一个汇率对象，也许是一个活动对象？它是 object 类型还是其他类型？我们应该如何暗示这是一个数组？

试试这个:

```
exchangeRates  ->  **exchangeRatesSet**
```

我使用后缀`Set`来标识数组，而不是复数。这使得代码稍微宽一点，但是你可以在上面看到它是如何解决收集复数对象的问题的。

## 功能

我用 React 和 Redux 做了很多我的专业工作，这个堆栈中我最喜欢的概念之一是[选择器](https://medium.com/@matthew.holman/what-is-a-redux-selector-a517acee1fe8)。

许多 Redux 程序员同意选择器应该以前缀`get`开头，就像在`getUserInfo`中一样，但没有深入研究。因此，不言而喻，非选择器函数名不应该以相同的前缀开头。

我想进一步阐述那个原则。

几乎在我的每个 React/Redux 项目中，都有一个包含五到十五个函数的文件夹，我称之为*助手*，它们不是选择器、thunks、动作创建者或缩减者……它们没有真正的家。

这些通常是纯函数，每个函数都执行一项任务。

功能包括:

```
isUserActive = user => {...}
canLoadItem = item => {...}makeFormattedDate = (date, format) => {...}
sortActiveUsers = users => {...}
validateEmailAddress = (address, validations) => {...}
```

我对这些辅助函数的存在没有意见，只要它们保持纯粹和简单。

然而，令我困扰的是，我不能一眼就识别出一个助手函数。

因此，我开始在我的所有助手函数前加上前缀`determine`或`prepare`，这取决于该函数返回的是布尔值还是数据。

```
isUserActive          ->  **determineIsUserActive**canLoadItem           ->  **determineCanLoadItem**makeFormattedDate     ->  **prepareFormattedDate**
sortActiveUsers       ->  **prepareSortedActiveUsers**
validateEmailAddress  ->  **prepareValidEmailAddress**
```

通过确保我的所有助手函数都被命名为带有特定前缀的动词短语，我消除了语法一致性的挑战，同时也为我的助手函数开发了一种品牌，将它们与我的选择器和 thunks 分开。

另外，请注意我们是如何开始混合命令式、声明式和疑问式函数名称，并以所有命令式名称结束的。

下面，我有两个 [Redux thunk](https://medium.com/@stowball/a-dummys-guide-to-redux-and-thunk-in-react-d8904a7005d3) 函数，一个调用另一个:

```
*const* **fetchUserDataIfNeeded** = userID => dispatch => {
  *const* fetchIsNeeded = {...} *if* (fetchIsNeeded) {
    dispatch(**fetchUserData**(userID))
  }
}

*const* **fetchUserData** = userID => dispatch => {...}
```

通常，当使用异步获取时，您需要调用一个函数来确定是否需要获取或重新获取数据，如果是，那么条件函数将调用获取函数。

在我的 Redux 项目中，我对这些情况使用后缀`IfNeeded`。现在，当我看到这个函数时，我知道我看到的是一个条件异步 fetch thunk，这是一个函数名中要传递的大量元数据。

# 结论

显然，我遗漏了太多东西，比如类应该如何成为名词，以及带有`children`的`parent`如何优于带有`items`的`list`等等……但我在六到八分钟的文章统计中茁壮成长。

所以，请，我鼓励你们所有人挑战你们在这里看到的一切。找到自己的公式，只要找到一致性。