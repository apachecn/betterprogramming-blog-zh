# 关于 Swift 阵列您需要了解的一切

> 原文：<https://betterprogramming.pub/everything-you-need-to-know-about-swift-arrays-f6a97e7e5b4b>

## 数组是最常见的数据类型。确保学习如何在 Swift 中使用它们

![](img/d865a3f9e902294f4c8b5111bbe2cf7a.png)

Christopher Gower 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

*数组*是 Swift 中常见的数据类型。它们就像一个列表，你可以在其中存储元素(如姓名或号码)。你会非常需要数组。因此，确保你学会如何和他们一起工作是很好的。

在本指南中，我将向您展示开始使用数组所需的一切。

# 如何创建数组

让我们从创建第一个数组开始。

例如，您可以通过在方括号内用逗号分隔整数来创建一个`numbers`数组，如下所示:

```
**let** numbers = [10, 23, 102]
```

或者像这样的字符串数组:

```
**let** names = ["Sofie", "Oliver", "Jennifer"]
```

此外，您可以初始化一个空数组，以便稍后填充:

```
**var** lectureAttendees = []
```

# 计算数组的元素数

在处理数组的时候，你经常要计算有多少个元素。在 Swift 中，您可以使用数组的`count`方法来计算它拥有的元素数量。

例如，让我们统计学生人数:

```
**let** students = ["Sofie", "Oliver", "Jennifer"]
**let** numStudents = students.countprint(numStudents)
```

输出:

```
3
```

# 如何访问数组中的元素

当您将数据存储到数组中时，您显然希望能够访问列表中的元素。在 Swift 中，你可以用一个*指数*来做这件事。

## 数组索引

*记住:数组索引从*T3 开始。*这意味着第一个元素的索引为* `*0*` *！*

要访问数组中的某个元素，可以使用*索引操作符* ( `[]`)，给它一个索引参数:

```
**let** numbers = [1, 2, 3]**let** second_number = numbers[1] // get the 2nd number
**let** third_number = numbers[2]  // get the 3rd number
```

## 访问一系列元素

您还可以使用`...`操作符获得数组中的*个元素:*

```
**let** numbers = [1, 2, 3, 4, 5, 6, 7]**let** range = numbers[1...3] // returns the numbers [2, 3, 4]
```

# 修改数组的元素

为了能够修改数组元素，数组必须是*可变的*。这意味着需要使用`var`而不是`let`来创建数组。

通过使用*索引操作符* ( `[]`)访问一个元素并使用*赋值操作符* ( `=`)赋予一个新值来进行修改:

```
**var** numbers = [1, 2, 3]numbers[0] = 10   // Change the first number to 10
print(numbers)
```

输出:

```
[10, 2, 3]
```

# 如何添加到数组中

在 Swift 中，向数组添加元素有三种主要方式:

*   `append`
*   `+=`
*   `insert`

## 1.附加

您可以使用数组的`append`方法将元素*添加到数组*的末尾:

```
**var** nums = [1, 2, 3]nums.append(4)
print(nums)
```

输出:

```
[1, 2, 3, 4]
```

## 2.插入

您可以使用`insert`在数组中的特定索引处添加一个元素。例如，让我们在一个`numbers`数组的第一个位置添加一个数字:

```
**var** nums = [1,2,3]nums.insert(10, at: 0) // Insert 10 as the first element at pos 0.
print(nums)
```

输出:

```
[10, 1, 2, 3]
```

## 3.+=运算符

您可以使用`+=`操作符将*一个新数组添加到另一个数组的末尾。*例如，让我们合并两个整数数组:

```
**var** nums = [1, 2, 3]
nums += [10, 20, 30]print(nums)
```

输出:

```
[1, 2, 3, 10, 20, 30]
```

# 从数组中移除元素

到目前为止，您已经看到了如何向数组中添加元素以及如何修改它们。接下来，您将学习如何从数组中移除元素。

## 删除特定元素

您可以通过使用带有索引的`remove(at:)`方法来移除带有给定索引的数组元素。例如，让我们从数字数组中删除第二个元素(即索引`1`):

```
**var** nums = [13, 20, 7]nums.remove(at: 1)  // remove the 2nd number
print(nums)
```

输出:

```
[13, 7]
```

## 基于标准删除元素

您可以使用数组的`filter`方法来创建数组的新的更新版本，其中一些原始元素根据某个标准被省略。例如，让我们通过删除所有的`1`来更新一个数字列表:

```
**var** nums = [1, 1, 3, 4, 1]nums = nums.filter { $0 != 1 }print(nums)
```

输出:

```
[3, 4]
```

`filter`方法的工作原理是遍历数组的所有元素，并测试每个元素是否满足标准。在上面的例子中，标准`$0 != 1`被逐个应用于每个元素(`$0`仅仅代表正在讨论的数组元素)。

## 如何去掉数组中的最后一个元素

如果你想去掉数组的最后一个元素，使用数组的`removeLast`方法。例如:

```
**var** numbers = [10, 20, 30]numbers.removeLast()
print(numbers)
```

输出:

```
[10, 20]
```

## 删除数组的第一个元素

您还可以借助于`removeFirst`方法移除数组的第一个元素。例如:

```
**var** numbers = [10, 20, 30]numbers.removeFirst()
print(numbers)
```

输出:

```
[20, 30]
```

## 消灭整个阵列

如果你想删除一个数组的所有内容，使用`removeAll`方法。例如:

```
**var** numbers = [10, 20, 30]numbers.removeAll()
print(numbers)
```

输出:

```
[]
```

# 循环遍历数组

在使用数组时，最重要的事情可能是能够有效地对数组的每个元素执行一些操作。你肯定不想一个一个的操作元素吧？这就是*循环*派上用场的地方。

遍历数组的一个简单方法是使用`for-in`循环*。*例如，你可以打印一个`students`数组的所有名称:

```
**let** students = ["Lisa", "Jack", "Sofie"]**for** student **in** students {
    print(student)
}
```

输出:

```
Lisa
Jack
Sofie
```

# 反转数组

有时你想颠倒一个数组的顺序。为此，您可以使用数组的`reverse()`方法*。例如，让我们反转一组数字:*

```
**var** numbers = [1, 2, 3]numbers.reverse()
print(numbers)
```

输出:

```
[3, 2, 1]
```

# 对数组排序

很常见的情况是，你希望根据某种标准对数组进行排序。这个任务可以通过使用数组的`sort(by:)`方法来完成。例如，按升序对`numbers`数组进行排序:

```
**var** numbers = [3, 1, 2]numbers.sort(by: <)
print(numbers)
```

输出:

```
[1, 2, 3]
```

您可以使用相同的方法按字母顺序对字符串列表进行排序。例如:

```
**var** students = ["Cecilia", "Alfred", "Buckley"]students.sort(by: <)print(students)
```

输出:

```
["Alfred", "Buckley", "Cecilia"]
```

# 结论

在 Swift 中，数组是最常见的数据类型。数组用于存储不同类型的数据。在你的职业生涯中，你会非常需要数组，所以一定要尽早掌握它们！

在本演练中，您学习了使用数组的基础知识，包括创建数组、访问/更改数组的元素以及更改数组的顺序。

感谢阅读！我希望你觉得这很有用。

# 你可能会感兴趣

[](https://medium.com/codex/swift-upgrade-your-skills-with-these-8-tips-da612111668) [## swift——用这 8 个技巧提升你的技能

### 1.带有三元运算符的较短的 If…Else 语句](https://medium.com/codex/swift-upgrade-your-skills-with-these-8-tips-da612111668) [](https://medium.com/codex/xcodes-built-in-refactoring-tool-is-awesome-21492b236ab6) [## Xcode 的内置重构工具非常棒

### Xcode 重构工具可以节省很多时间。更好的是，重构特性非常容易学习，而且…

medium.com](https://medium.com/codex/xcodes-built-in-refactoring-tool-is-awesome-21492b236ab6) 

# 资源

 [## Swift.org

### 您可以将此页面上的资源用作 Swift 语言的文档。苹果公司拥有额外的资源…

swift.org](https://swift.org/documentation/)