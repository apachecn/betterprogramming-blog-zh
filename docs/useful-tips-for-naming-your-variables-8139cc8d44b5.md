# 命名变量的有用提示

> 原文：<https://betterprogramming.pub/useful-tips-for-naming-your-variables-8139cc8d44b5>

## 给变量命名很难，但是我们都可以做得更好

![](img/eb32b71f8594f448abde975f2e4908b6.png)

照片由[纳撒尼尔·舒曼](https://unsplash.com/@nshuman1291?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

**📕看看我的新书** [**干净代码原则和模式:软件从业者手册**](https://www.amazon.com/Clean-Code-Principles-Patterns-Practitioners-ebook/dp/B0BSDJKYQJ/ref=sr_1_1?crid=8M0CMV4CP6UQ&keywords=clean+code+principles&qid=1674980636&sprefix=clean+code+principle%2Caps%2C175&sr=8-1) ！

这篇文章包含了一组为不同类型的变量命名的规则。使用良好的变量命名约定的好处是，变量的名称不仅告诉您它的用途，还告诉您变量的类型。这在非类型化语言中非常重要，但在类型化语言中也非常有益，因为现代类型化语言使用自动类型推导，并且您不会总是看到变量的实际类型。

最好的情况是，用大量的变量(以及函数名)编写代码会使代码读起来像散文。

> *“计算机科学只有两个硬东西:缓存失效和* ***命名东西。”****——*[*菲尔·卡尔顿*](https://quotesondesign.com/phil-karlton/)

# 命名整数

有些值本质上是整数，比如`age`或`year`。每个人都能立即理解`age`或`year`变量的类型是一个数字，更具体地说，是一个整数。所以你不需要在变量名中添加任何东西。它已经告诉你它的类型了。

整数变量中最常用的一类是计数或数字。你可以在每一段软件代码中看到这些变量。我使用下面的约定来命名这些变量:`numberOf<something>`或者`<something>Count`。比如`numberOfFailures`或者`failureCount` *。有人可能会使用变量名`failures`。但是变量名的问题是它没有明确指定变量的类型。对我来说，一个名为`failures`的变量表明它是一个集合(比如一个数组或者一个失败列表)。*

如果变量的单位不是不言自明的，我总是在变量名的末尾添加一个单位描述。比如我不用`tooltipShowDelay`，而是用`tooltipShowDelayInMillis` 甚至更好的`tooltipShowDelayInMillisecs`。

# 命名浮点数

浮点数不像整数那样常见，但是时不时地，你也会需要它们。一些值本质上是浮点数，像大多数未舍入的度量值(例如，`price`、`height`、`width`或`weight`)。如果您认为需要存储一个度量值，那么使用浮点数类型的变量是一个安全的选择。

如果我需要存储一个非整数的量，我会使用一个名为`<something>Amount`的变量，比如`rainfallAmount` *或者* `moneyAmount` *。当我在代码中看到“amount of something”时，我认为它是一个浮点数。*

如果变量的单位不明确，我会在变量名的末尾加上一个单位描述，比如`widthInCentimeters`、`rainFallAmountInInchesPerHour`、`angleInDegrees`、`failurePercent`或者`failureRatio`。

# 命名布尔值

布尔变量只能有两个值之一:`true`或`false`。我用模式命名布尔变量:`isSomething`、`hasSomething`、`doesSomething`、`didSomething`、`shouldDoSomething`或`willDoSomething`。举几个例子:`isDisabled`、`hasErrors`、`allowsWhitespace`、`didUpdate`、`shouldUpdate`、`willUpdate`。一个正确的布尔变量名是一个问题，其答案是对/错或是/否。

# 命名字符串

字符串非常常见，许多实体本质上都是字符串，如`name`、`title`、`city`或`country`。

*代码变得更加明显和易于阅读。例如:*

```
*// Javascript
const year = parseInt(yearAsString, 10);* 
```

# *命名数组、列表和集合*

*当命名数组、列表或集合时，我总是使用名词的复数形式，比如`customers`、`errors`或`tasks`。例如，在大多数情况下，这就足够了，因为如果只是迭代集合，就不需要知道底层的实现。但是在有关系的情况下，我通常在变量的名称中指定实现。比如:`queueOfTasks`、`stackOfCards`，或者`orderedSetOfTimestamps`。我从不使用非复数形式的变量名，比如`customerList`或`handledOperationSet` *，*，因为这样会使变量名变长，而且在很多情况下会不必要地将某个实现与变量名联系起来。*

# *命名地图*

*通常通过请求某个键的值来访问地图。这就是为什么我总是用模式`keyToValueMap`来命名我的地图。假设我们有一个包含每个客户订单计数的映射。这将被命名为`customerToOrderCountMap` *。或者，如果我们有每个产品 ID 的供应商列表，映射变量将被命名为`productIdToSuppliersMap`。**

# *命名对和元组*

*我用模式`variable1AndVariable2`命名一对。例如:`heightAndWidth`。而对于元组，我使用的模式是`variable1Variable2` *…* `AndVariableN`。比如:`heightWidthAndDepth` *。**

# *命名对象*

*对象变量是指一个类的实例。类是首字母大写的名词，如`Person`、`Account`或`Task`。对象变量名符合相关的类名(如`Person`类的`person`对象、`Account`类的`account`对象等)。).你也可以随意用形容词修饰物体的名称。比如:`completedTask`。对我来说重要的是保留类名作为对象名的最后一部分。*

*很多时候，类和对象是单数形式的名词，这将它们与集合(数组、列表和集合)区分开来。但是有些情况下你想用复数形式来命名你的对象。例如，您可以将一个`options`对象作为函数的最后一个参数。或者在 React 中，我们调用包含组件属性的对象`props`。你如何判断一个复数形式的名词是一个物体还是一个集合？在集合中，项通常属于同一类型。但是在一个物体中，它们可以是不同的。`options`中的每个`option`可以有不同的类型，`props` 中的每个`prop`可以有不同的类型。*

*如果可能的话，我尽量避免使用可能与字符串或数字的变量名混淆的对象变量名。比如，我用`valueObject`或`nameObject`来代替`value`或`name`或*来命名对象。**

```
*// Typescript
interface Person { nameObject: NameObject, address: Address }
const { nameObject } = getPersonById(1234);
const fullName = `${nameObject.firstName} ${nameObject.lastName}`;*
```

# *命名可选变量*

*可选变量命名取决于编程语言和可选类型实现。在需要展开可选类型的语言中，被包装的可选变量应该有前缀，而被展开的可选变量名称应该没有前缀。*

*在 Java 中，我使用`Optional<T>`并将这种类型的变量命名为`possible<Something>`。然后我可以使用这样的结构:*

```
*// Java
possibleLoggedInUser.ifPresent(loggedInUser -> loggedInUser.logout());final User currentUser = possibleLoggedInUser.orElse(guestUser);*
```

*在 JavaScript + Flow 或 TypeScript 以及其他使用类型联合创建可选类型的语言中，可选变量通常不需要任何前缀:*

```
*// Typescript
function addTaxToAmount(amount: number, discount?: number): number {
 return 1.2 * (amount — ( discount ?? 0));
}*
```

# *命名类字段*

*我们可以把类字段/成员变量看作对象内部的变量。所以我对它们应用了和变量一样的规则。有些人使用前缀或后缀来表示字段/成员变量。我也在 C++中使用过，现在仍然在使用，因为从 1995 年开始这已经成为一种习惯。但在 Java 或 JavaScript 中，我不使用任何前缀或后缀。我也试图在 C++中摆脱它们，但是我很难改掉这个旧习惯。*

# *命名函数参数*

*函数参数的工作方式类似于函数内部的变量。有两个例外，我对函数参数使用了不同的命名约定:`constructor` 和`setter`方法。在它们中，我使用前缀或后缀作为变量名:*

*或者，在变量名末尾加下划线也可以。比起前缀，我更喜欢下划线后缀，因为它使变量更具可读性。*

# *命名静态不可变常数*

*静态不可变常量是一个特例，我用全部大写的 snake_case 来命名非变量。例如:`TOOLTIP_SHOW_DELAY_IN_MILLISECS`。如果静态常量是可变的，我使用普通的变量命名约定。这与[谷歌 Java 风格指南](https://google.github.io/styleguide/javaguide.html)中的命名约定相同。*

# *一般命名规则*

## *避免晦涩的缩写*

*如果可能的话，我尽量避免缩写。不过，有很多常用的缩写，比如`Str` 表示字符串，`Num`表示数字，`Prop`表示属性，或者`Val`表示值。大多数程序员使用这些，或者至少曾经使用过。我已经用过了，现在有时还会用。我特别避免使用不常用的缩写。在变量名过长的情况下，我使用缩写。*

## *避免名字太短和没有意义*

*太短的名字并不能真正表明变量的含义。我不使用像`i`或`k`这样的循环迭代器变量名。我更愿意使用变量名`index`或`<something>Index`T7。我也用了很多不需要循环变量的函数式编程。另外，像`num`、`val`或`tmp`这样的变量名不属于我的词汇表。我喜欢用更具描述性的方式来命名变量。*

***📕看看我的新书** [**干净代码原则和模式:软件从业者手册**](https://www.amazon.com/Clean-Code-Principles-Patterns-Practitioners-ebook/dp/B0BSDJKYQJ/ref=sr_1_1?crid=8M0CMV4CP6UQ&keywords=clean+code+principles&qid=1674980636&sprefix=clean+code+principle%2Caps%2C175&sr=8-1) ！*