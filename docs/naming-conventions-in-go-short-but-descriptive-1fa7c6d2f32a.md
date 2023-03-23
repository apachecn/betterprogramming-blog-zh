# Go 中的命名约定:简短但有描述性

> 原文：<https://betterprogramming.pub/naming-conventions-in-go-short-but-descriptive-1fa7c6d2f32a>

## 成文和不成文的规则

![](img/15ffb974a74bf05ea8edd6d18ea4204f.png)

照片由 [chuttersnap](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

> "计算机科学中只有两个难题:缓存失效和事物命名."— [菲尔·卡尔顿](https://martinfowler.com/bliki/TwoHardThings.html)

这不是一个有趣的笑话。写作容易，读书痛苦。您是否曾经发现自己想知道某个变量指的是什么，或者某个包的目的是什么？这就是为什么我们需要规则和惯例。

但是，虽然习俗是为了让生活更容易，但它们可能被高估和滥用。为命名设定适当的惯例和规则是非常重要的，但是盲目地遵循它们是有害的。

在本文中，我将介绍 [Go](https://golang.org/) (成文和不成文的规则)中一些重要的变量命名约定，以及它们是如何被滥用的——尤其是在短变量命名的情况下。包和文件的命名以及项目结构不在本文的讨论范围之内，因为它们值得另写一篇文章。

# 围棋中的成文规则

像任何其他编程语言一样，Go 也有命名规则。此外，命名具有语义效果，它决定了包外的标识符的。

## 混合瓶盖

Go 中的约定是使用`MixedCaps`或`mixedCaps`(简单的 camelCase)而不是下划线来写多单词名称。如果标识符需要在包外可见，它的第一个字符应该是大写的。如果你不打算在另一个包中使用它，你可以放心地坚持使用`mixedCaps`。

```
package awesometype Awesomeness struct {

}// Do is an exported method and is accessible in other packages
func (a Awesomeness) Do() string {
 return a.doMagic("Awesome")
}// doMagic is where magic happens and only visible inside awesome 
func (a Awesomeness) doMagic(input string) string {
 return input
}
```

如果试图从外部调用`doMagic`，将会得到一个编译时错误。

## 接口名称

> 按照惯例，单方法接口由方法名加上一个-er 后缀或类似的修饰来命名，以构造一个代理名词:`Reader`、`Writer`、`Formatter`、`CloseNotifier`等— [Go 的官方文档](https://golang.org/doc/effective_go.html)

经验法则是`MethodName + er = InterfaceName`。这里棘手的部分是当你有一个不止一个方法的接口时。遵循惯例命名并不总是显而易见的。我应该用一个方法把接口分成多个接口吗？我觉得这是主观决定，要看案情。

## 吸气剂

在[官方文档](https://golang.org/doc/effective_go.html#Getters)中提到，Go 没有自动支持 setters 和 getters，但也没有禁止。它只是有一些规则:

> “自己提供 getter 和 setter 没有错，这样做通常是合适的，但是将`Get`放在 getter 的名称中既不习惯也没有必要。”

```
owner := obj.Owner()
if owner != user {
    obj.SetOwner(user)
}
```

但是值得一提的是，如果 setter 不执行任何特殊的逻辑，最好只导出字段，去掉 setter 和 getter 方法。如果你是 oop 的拥护者，这可能听起来很奇怪。但事实并非如此。

# 围棋中的潜规则

有些规则没有正式记录，但它们在社区中广泛传播。

## 较短的变量名

Go 社区提倡使用更短的描述性变量，但我认为这种特定的约定被误用了。不知何故，忘记它的描述部分是很常见的。一个描述性的名字可以让读者在看到它之前就明白它是关于什么的。

> "程序必须是为人们阅读而写的，并且只是附带地为机器执行而写的."哈罗德·艾贝尔森

*   单字母标识符:这特别用于范围有限的局部变量。我们都同意，我们不需要`index`或`idx`来理解它是一个增量。当仅限于循环范围时，使用单字母标识符是常见的，也是推荐的。

```
for i := 0; i < len(pods); i++ {
   //
}
...
for _, p := range pods {
  //
}
```

*   简写名称: **S** 如果可能的话，推荐使用简写名称，只要它们对于第一次阅读代码的人来说容易理解。使用范围越广，越需要描述性。

```
pid // Bad (does it refer to podID or personID or productID?)
spec // good (refers to Specification)
addr // good (refers to Address)
```

## 唯一的名称

这是关于缩写词如 API，HTTP，等。或者 ID、DB 之类的名字。按照惯例，我们保留这些词的原始形式:

*   `userID`而不是`userId`
*   `productAPI`而不是`productApi`

## 线长度

Go 中没有固定的行长度，但是总是建议避免长行。

# 结论

我试图总结 Go 中常见的命名规则，以及我们何时以及在何种程度上可以应用它们。我还解释了 Go 中较短命名背后的主要思想，即在简洁和描述性之间找到平衡。

习俗会指引你，而不是阻碍你。因此，只要感觉合适，并且仍然服务于一般目的，您就应该放心地打破它们。