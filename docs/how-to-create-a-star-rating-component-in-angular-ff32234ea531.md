# 如何在 Angular 中创建星级评定组件

> 原文：<https://betterprogramming.pub/how-to-create-a-star-rating-component-in-angular-ff32234ea531>

## 让你的用户留下评论

![](img/7262466ff754781b92592f68026e0c9d.png)

[乔丹·克劳福德](https://unsplash.com/@jcsf?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

在本文中，我们将创建一个简单的 Angular 组件。你上过电影或书籍推荐网站吗？你可能注意到他们使用流行的星级评定。给事物打零到五星的分数是很常见的。这对任何开发人员来说都是一个很好的练习。让我们以测试驱动的方式一起回顾一下。我们会很开心的。这是一个承诺！

![](img/a52aaa3c1bdfb4d58c6841a0f94b761e.png)

我们的星级评定部分

# 空荡荡的星星

如何开始创建这样一个组件？好吧，让我们从简单的开始。我们可以从创造五颗空恒星开始。这是问题解决的一部分，对吗？让我来描述一下你如何编写第一个测试。不要担心完整的代码示例，因为它们将包含在本文的末尾。

```
***it***('shows 5 empty stars', () => {
  ***expect***(getEmptyStars().length).toEqual(5);
});
```

创建这个组件很简单。我们只是复制粘贴一个空的星星五次。眼力好的人可能会注意到我们在这里使用了数据属性作为选择器。如果你对为什么这可能有用感兴趣，你可以在[以前的文章](https://medium.com/better-programming/decouple-tests-with-data-attributes-c920606c5f27)中阅读所有相关内容。

```
<span class="fa fa-star-o" data-jest="empty star"></span>
<span class="fa fa-star-o" data-jest="empty star"></span>
<span class="fa fa-star-o" data-jest="empty star"></span>
<span class="fa fa-star-o" data-jest="empty star"></span>
<span class="fa fa-star-o" data-jest="empty star"></span>
```

当然，我们可以做得更好。如果评分高于零，我们不想显示五颗空星。当评分为 1 时，我们希望看到四颗空星！当评分为二时，我们希望看到三颗空星。当评分为五时，我们根本看不到任何星星！最后一点是我们很容易测试的。让我们验证一下，当评级为 5 时，我们的组件没有显示星号。

```
***it***('shows 0 empty stars', () => {
  component.rating = 5;
  fixture.detectChanges(); ***expect***(getEmptyStars().length).toEqual(0);
});
```

由于我们使用的是`rating`输入字段，我们需要自己触发变更检测。否则，当我们检查组件内部的评级时，评级仍然是未定义的。这是你在角度测试中自动学到的东西。让我们继续在绿色国家工作。为了完整，必须提到它。您可以在本文中阅读更多关于变更检测[的内容。](https://dzone.com/articles/how-to-use-change-detection-in-angular)

就个人而言，添加这个简单的检查是正确的。它不是面向未来的，但它让测试变得更加环保。现在我们已经有了明确的界限，让我们添加一个更一般的测试。

下面的测试将迫使我们修改我们的代码，以适用于这三种情况以及中间的所有其他情况。原理很简单:我们总是显示五颗减去我们的评级的空星。所以如果我们有三颗星，我们会看到两颗空的星:

没有必要为介于两者之间的所有情况创建测试。让我们一起努力吧。我们需要的是一种 for 循环，向我们展示所有的空星。因此，空星的数量是用下面的代码片段计算的:

```
private get numberOfEmptyStars(): number {
  return this.MAX_NUMBER_OF_STARS - this.rating;
}
```

为了在模板中创建一个 for 循环，我们需要一些可以迭代的对象。为此，我们实例化一个长度与空星数量相同的数组:

```
get emptyStars(): any[] {
  return ***Array***(this.numberOfEmptyStars);
}
```

我们有东西要迭代。让我们用这个数组来迭代空的星星。从逻辑上讲，这是您所期望的那种实现。顺便说一下，你甚至不需要`ng-container`。您也可以将`ngFor`放在`span`本身上。但是，大家一致认为这是很有可读性的，对吧？

```
<ng-container *ngFor="let ***emptyStar*** of emptyStars">
  <span class="fa fa-star-o" data-jest="empty star"></span>
</ng-container>
```

我们工作的第一部分已经完成。我们对空恒星的实现已经完成。让我们进入下一步！

# 全明星

让我分享一些好消息:你也可以为全明星重复大部分工作。现在更容易了。所以我们继续吧。首先，我们将从五星评级开始:

```
***it***('shows five full stars when the rating is five', () => {
  component.rating = 5;
  fixture.detectChanges(); ***expect***(getFullStars().length).toEqual(5);
});
```

让我们重复同样的过程。现在应该更快了。我们添加了五星的实现。根据经验，您意识到您可以只添加五颗完整的星:

```
<span class="fa fa-star checked" data-jest="full star">
<span class="fa fa-star checked" data-jest="full star">
<span class="fa fa-star checked" data-jest="full star">
<span class="fa fa-star checked" data-jest="full star">
<span class="fa fa-star checked" data-jest="full star">
```

瞧，你现在可以正确显示五星评级了。很快，你应该意识到你的另一个边界案例被打破了。我们需要为我们的场景添加一个零星级的测试！就这么办吧。这是一个简单的测试:

```
***it***('shows zero full stars when the rating is zero', () => {
  component.rating = 0;
  fixture.detectChanges(); ***expect***(getFullStars().length).toEqual(0);
});
```

在实现空星的时候，我们已经发现我们可以为此写一个简单的`if`语句。让我们再来一次。让我们保持绿色测试，好吗？

现在我们已经为全明星做好了准备。我们的全面实施对于五星评级和零星评级都是正确的。但是介于两者之间的价值观呢？让我们马上解决这个问题。我们将为此添加一个更通用的测试:

```
***it***('shows "rating" full stars', () => {
  component.rating = rating;
  fixture.detectChanges(); ***expect***(getFullStars().length).toEqual(3);
});
```

一旦你添加了这个测试，你就会意识到，“哦，它对任何评级都有效。”对于零、等于评级的数量或五星，测试是相同的。因此，如果您的评级是三，应该会显示三颗完整的星。让我们来实现它:

```
<span *ngFor="let ***fullStar*** of fullStars" class="fa fa-star checked" data-jest="full star"></span>
```

仅此而已。我们也完成了全星的实现。现在我们有了一个功能齐全的组件。它会显示 0 到 5 颗星之间的任何评分。在这个阶段，您当然可以进行一些重构。

不过，你不应该觉得有必要扔掉测试。在这个阶段，空星和满星的两种边界情况都包括在内，在这两种情况之间有一个基本的测试。这很好，而且它们为未来的发展提供了一个很好的安全网。

# 结论

在 Angular 中创建星形组件是一件容易的事情。不过，有一件事确实困扰着我。你看到我们必须创建一个数组来多次做同样的事情了吗？这感觉不对，但我找不到一个简单的本土方法来做到这一点。

在下面的要点中，实施还包括有一半剩余评分的可能性(例如 3.5 或 4.5 星)。请随意检查或将其作为练习来实施。通过在下面留下建设性的批评，你可以帮助我在未来提供更好的内容。感谢您从头到尾的阅读。

书籍.评级.组件.规格(测试)

book.rating.component.ts(实现)