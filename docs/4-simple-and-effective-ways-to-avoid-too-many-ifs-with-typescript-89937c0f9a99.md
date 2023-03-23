# 用 TypeScript 避免太多 if 的 4 种简单有效的方法

> 原文：<https://betterprogramming.pub/4-simple-and-effective-ways-to-avoid-too-many-ifs-with-typescript-89937c0f9a99>

## If…else 还不错——过度使用才是

![](img/795b8540eff3e2d4994aa0be1c30a1cc.png)

由[伊利亚·巴甫洛夫](https://unsplash.com/@ilyapavlov?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/coding-complexity?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

If…else 存在于所有编程语言中。如果使用得当，它们并不是坏习惯。这是一种简单、易于理解、灵活的逻辑控制结构。

但在现实中，它经常被过度使用。在下列情况下，它们可能是坏的:

*   嵌套 if-else 或多层嵌套(更糟)
*   太多的 if-else 会导致大量的条件分支
*   具有混合标志的复杂条件语句

if…else 的过度使用是代码气味。这使得你的代码库难以阅读和维护，因为要考虑更多的分支路径。

作为开发人员，我们总是期待通过重构来改进代码。在本文中，我们将在 TypeScript 的上下文中介绍四种不同的方法。

向前跳:

*   [守护和提前返回](#377c)
*   [表格驱动法](#87a7)
*   [提取](#0026)
*   [默认值和||运算符](http://283e)
*   [关注根本原因](#b519)

# 1.守卫和早归

**问题**:在这个嵌套的`if...else`代码片段中，跟随`status`值的变化可能是一个挑战。即使在这个简化的例子中，控制流也不是简单明了的。您可以想象，当添加更多的条件逻辑时，代码会迅速增长到难以维护的状态。

**解** : [守](https://refactoring.guru/replace-nested-conditional-with-guard-clauses)早退是我最喜欢的重构方法。它实施起来很简单，并能给你带来立竿见影的效果。

在下面的代码中，我们将边缘情况移到函数的开头。我们还将嵌套的 if 简化为带有三元运算符的平面语句。当满足边缘情况条件时，立即返回错误状态。

重构后，代码变成线性的。早期返回首先处理失败案例，使代码更容易测试，更不容易出错。结果是更好的可读性和更好的可维护性。

另一种类似的方式是使用包含验证逻辑的“数据保护”[装饰器](https://sunnysun-5694.medium.com/how-to-map-rest-api-data-using-decorator-pattern-in-angular-6-94eb49ba16b1)。main 函数只有在验证通过后才会被调用。

# 2.表格驱动方法

**问题**:下面的代码片段的意图是获取一个月的天数。显然，它容易出错，而且很难改变。例如，如果我们在闰年需要支持会发生什么？这将是一场维护噩梦。

**解决方案**:我们使用[表格驱动的方法](https://rads.stackoverflow.com/amzn/click/com/0735619670)来改进上面的代码片段。

> 表驱动方法是允许您在表中查找信息而不是使用逻辑语句(例如，case、if)的方案。—开发到

这里我们定义了一个数组`monthDays` ,它映射了一个月的可能值。结果更加简洁，可读性更好。请注意，当输入参数互斥时，这种方法更合适。

同样的方法可以应用于一组函数。

在上面的代码示例中，`performAction` 函数包含多个 if。当需要添加更多的动作时，很难扩展。我们使用`funcs` [记录类型](/typescripts-record-type-explained-691372b1a449)来映射`actionName` 匹配函数。这样我们就避免了多个 if，并且它可以很容易地扩展到附加的动作。

表驱动方法的应用不仅仅是一对一的键值映射。在下面的示例中，ifs 语句用于根据给定的分数范围进行评级。如何重构这个方法？

```
 getRating(score: number) {
    if (score > 12) {
      return 5;
    } else if (score > 9) {
      return 4;
    } else if (score > 6) {
      return 3;
    } else if (score > 3) {
      return 2;
    } else {
      return 1;
    }
  }
```

我们可以使用`Object.keys`应用表格驱动方法，如下所示:

```
 getRating(score: number) {
    const ratingScoreMap = {
      12: 5,
      9: 4,
      6: 3,
      3: 2,
      1: 1
    };
    const sortedRating = Object.keys(ratingScoreMap)
      .map(Number)
      .sort((a, b) => b - a);
    for (const threshold of sortedRating) {
      if (score > Number(threshold)) {
        return ratingScoreMap[threshold];
      }
    }
    return 1;
  }
```

通过使用查找表来存储映射关系，表驱动的方法允许在各种上下文中进行高效和灵活的值映射。

# 3.提取

**问题**:只要可读，复杂的条件不是问题。下面的例子包含一个嵌套的复杂的`if`条件语句。很难搞清楚是什么状况。

**解决方案**:extract 方法是一种将条件逻辑重构为更模块化形式的方法。在下面的代码片段中，我们使用 extract 方法来重构上面的示例。

嵌套 ifs 中的多重检查被包装到一个单独的函数中，并有一个有意义的名称。嵌套的 if 由一个带有简单条件函数的 if 代替。

改进后的版本更容易理解和更改。

# 4.默认值和`||`运算符

**问题**:下面的`if..else`用于执行空值检查，但是代码过于复杂，可读性不强。

**解决方案**:给参数一个默认值是我个人喜欢的另一种重构方法。

默认值结合`||`操作符是降低代码复杂度的一种简单有效的方法。

在这个例子中，我们给状态一个默认值，并使用`||`操作符来处理空检查。得益于 JavaScript 的灵活性，结果是代码重复更少，代码更可读。

# 关注根本原因

过度使用`if…else`可能是其他问题的征兆。当致力于重构时，我们应该关注根本原因，而不是仅仅修复症状。

一个函数由输入、内部状态和输出组成。该函数接受输入参数，执行内部状态突变，并返回输出结果。if 的过度使用..否则可能是以下原因造成的:

*   **内部状态逻辑过多**:以上四种方法都可以用来改进代码。
*   **带有混合标志的复杂输入参数**:在这种情况下，默认值方法可能会有所帮助。但根本原因可能是函数接口抽象。我们可能需要回顾和改进抽象。
*   **太多不同的返回路径和复杂的输出**:这是该函数可能正在做太多事情的迹象。重点应该是重构函数，而不是减少 if..else 语句。

为了做一个干净的重构，我们应该专注于制定一个清晰的接口，以便函数只做一件事情。([单一责任原则](https://en.wikipedia.org/wiki/Single-responsibility_principle#:~:text=The%20single%2Dresponsibility%20principle%20(SRP,it%20should%20encapsulate%20that%20part.&text=Hence%2C%20each%20module%20should%20be%20responsible%20for%20each%20role.))。

# 摘要

我们讨论了四种简单有效的方法来消除 if…else 的过度使用。应用这些方法将帮助您编写更清晰、更易读的代码。

还有其他方法来重构 if..否则，像使用[工厂模式](https://medium.com/codex/factory-pattern-type-script-implementation-with-type-map-ea422f38862)或责任链模式。本文没有涉及它们，因为我想把重点放在更简单、更实用的解决方案上。

没有放之四海而皆准的解决方案，因为每种方法都有自己的优缺点。为特定工作选择合适工具的能力将顶级开发人员与普通开发人员区分开来。

如果你喜欢这篇文章，你可能也喜欢阅读另一篇打字稿文章。

[](/apply-builder-pattern-to-generate-query-filter-in-typescript-651a6b13da38) [## 应用生成器模式在 TypeScript 中生成查询筛选器

### 实现一个不可变的强类型过滤器生成器

better 编程. pub](/apply-builder-pattern-to-generate-query-filter-in-typescript-651a6b13da38) 

编程快乐！