# 如何提高 iOS 代码性能:减少数组搜索

> 原文：<https://betterprogramming.pub/how-to-boost-your-ios-code-performance-reduce-searching-an-array-55fbdfee2050>

## 优化快速和客观的 C 代码速度的技巧

![](img/b9eaad1f331f6602db9791158bd4a210.png)

照片由 [Aron 视觉效果](https://unsplash.com/@aronvisuals?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

迭代一个数组的时间复杂度是 O(n)。听起来可以接受吧？当我们在截止日期前有大量的工作要做时，我们可能会忽略它对代码性能的影响，而不会花更多的精力去考虑更好的解决方案。但是，一旦使用这种技能成为一种自然的反应，我们可能会意识不到我们甚至在代码中编写了嵌套的 for 循环或嵌套过滤器，这将时间复杂度增加到 O(n)，并且我们很可能在以后遭受性能问题。

尝试在非顺序集合类型中搜索，如`Set`和`Dictionary`，而不是`Array`，因为在`Set`和`dictionary`中搜索的时间复杂度是 O(1)，而在`Array`中搜索的时间复杂度是 O(n)。

在本文中，我将向您展示如何使用`Set`和`dictionary`优化速度。

# 示例 1:设置

在示例 1 中，有所有会员的数据和白金会员的 id。目标是用`platinumMemberIDs`从`members`中派生出一组白金会员。

```
let members: [Member]
let platinumMemberIDs:[Int]
```

## 优化前

`filter(_:)`的时间复杂度为 O(n)，数组实例方法`contains(_:)`的时间复杂度也为 O(n)。

下面的方法遍历`members`数组。在每次迭代中，它在`platinumMemberIDs`中搜索以检查`platinumMemberIDs`是否包含当前成员的 id。

该方法的时间复杂度为 O(n)。

```
var platinumMembers = members.filter {
    platinumMemberIDs.contains($0.id)
}
```

## 优化后

由于`platinumMemberIDs`的顺序无关紧要，我们可以将该数据保存在非顺序收集类型`Set`中。在 a `Set`中搜索的时间复杂度为 O(1)。总的时间复杂度降低到 O(n)。

```
let platinumMemberIDSet = Set(platinumMemberIDs)var platinumMembers = members.filter {
    platinumMemberIDSet.contains($0.id)
}
```

# 示例 2:字典

在示例 2 中，有两组来自不同来源的用户，我们希望找出他们的交集。只要一个用户的电子邮件与另一个组中的另一个用户的电子邮件相同，我们就认为他们是同一个用户。

```
let starUsers: [User]
let recentUsers: [User]
```

## 优化前

最简单的方法是编写嵌套的 for-loops 来找出重复的用户，但是时间复杂度会是 O(n)，不是很理想。

```
var duplicatedUsers = [User]()for star in starUsers {
  for recent in recentUsers {
    if star.email == recent.email {
      duplicatedUsers.append(star)
    }
  }
}
```

## 优化后

为了降低时间复杂度，我们可以简单地从`recentUsers`数组创建一个字典，其中每个用户的电子邮件作为键，用户对象作为值。

```
let recentUserMap = Dictionary(uniqueKeysWithValues: recentUsers.map { ($0.email, $0) })
```

然后我们搜索`starUsers`数组，但是在每次迭代中，我们不需要迭代`recentUsers`数组来找出优化后的重复用户。相反，我们直接从`recentUserMap`访问一个最近用户的用户对象，该用户的电子邮件与当前的明星用户相同。时间复杂度降低到 O(n)。

```
let duplicatedUsers = starUsers.filter {
  guard let _ = recentUserMap[$0.email] else { return false }
  return true
}
```

如果你喜欢这篇文章，这里有另一篇你可能喜欢的文章:

# 如何通过减少集合类型初始化来提高 iOS 代码性能

[](/how-to-boost-your-ios-code-performance-by-reducing-collection-type-initialisation-564bffee94a3) [## 如何通过减少集合类型初始化来提高 iOS 代码性能

### 优化快速和客观的 C 代码速度的技巧

better 编程. pub](/how-to-boost-your-ios-code-performance-by-reducing-collection-type-initialisation-564bffee94a3)