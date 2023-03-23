# 如何正确地在 TypeScript 中声明对象文本

> 原文：<https://betterprogramming.pub/how-to-declare-object-literals-in-typescript-the-right-way-63783365aa85>

## 在本文中，您将看到在 TypeScript 中声明对象文本的不同方式

![](img/eefa9b4c5fafe6a5a1d1478d44dcca44.png)

伊利亚·巴甫洛夫在 [Unsplash](https://unsplash.com/s/photos/type?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# 什么是对象文字？

对象文字是逗号分隔的名称-值对列表。想想 JSON 对象。下面是包含用户信息的对象文字:

一个对象文字的例子

让我们看看在 TypeScript 中声明对象文字的不同方法，以及与它们相关的一些注意事项。

# 1.声明没有类型的对象文本

声明没有类型声明的对象文本

这是最简单的方法，因为您可以像在 JavaScript 中一样声明对象。但是，这也意味着您没有按照预期的方式使用 TypeScript。

在前两条语句中，我试图访问对象成员的名字和**电子邮件**，并且运行良好。当我试图访问一个不存在的成员**号**时，TypeScript 抛出了一个错误。我也不能添加任何新成员。

# 2.声明 any 类型的对象文本

用 any 类型声明对象文本

如果你使用类型`any`，当你试图访问一个不存在的成员时，你不会得到一个错误，你也可以在你的对象中添加新的成员。

所以这一定是正确的方法，对吗？不要！

当你使用类型`any`来声明时，它就像一个通配符。虽然使用`any`会‘修复’前一节的错误，但会让类型系统变得毫无价值。

# 3.用 Object 类型声明对象文本

用 Object 类型声明对象文本

如果你使用类型`object`，你的对象文字将不会像你期望的那样工作。在第一个控制台语句中，虽然我试图访问`user`**的一个成员，但是我得到了一个错误。尽管当您用类型错误声明对象文本时，TypeScript 不会引发错误，但是当您试图访问该类的成员时，它将无法找到这些成员。与前一种情况类似，您也可以添加任何新成员。**

# **4.使用类型记录**

**使用类型记录<string any="">声明对象文字</string>**

**这种情况类似于第二种情况，我们用类型`any`声明了对象文字。如果您试图访问一个不存在的成员，并且可以向您的对象添加新成员，您将会得到一个未定义的错误。但是，这比使用一种类型的`any`要好。**

**使用类型记录<string string="">声明对象文字</string>**

**不要用`Record<string,any>`，你可以用`Record<string,string>`或者`Record<string, string | number>`。**

**由于我在上面的例子中使用了`Record<string,string>`，当我试图添加一个有数字值的成员时，TypeScript 抛出一个错误。当您不知道对象文字的结构时，这是有用的，例如，来自 API 的响应对象。**

# **5.创建声明时使用自定义类型**

**用自定义类型声明来声明对象文本**

**如果知道对象文本的结构，就可以在声明时指定类型。当您尝试添加新成员或尝试访问不存在的成员时，TypeScript 将引发错误。虽然这是 TypeScript 的一个很好的用途，但是最好使用`Interfaces`让你的代码看起来更整洁。**

# **6.使用接口**

**用接口声明对象文本**

**这种情况将显示与第五种情况相同的行为，但是它使您的代码看起来更加整洁，并且您将能够重用`interface`来声明多个对象文字。您还可以从现有的`interface`扩展，创建多个接口的联合，等等。**

**在我的`interface`里，我还把年龄加为可选成员；因此，尽管`user1`没有成员年龄，但`user2`有，TypeScript 不会抛出错误。**

# **那么，什么才是正确的方法呢？**

**这是我的看法。**

**在大多数情况下，您应该尽可能使用接口。这可以确保您按照预期的方式使用 TypeScript，并且有助于类型检查和自动完成建议。您可以为所有类型声明创建一个单独的文件，只需导入接口来分隔接口和代码逻辑。**

**然而，在某些情况下，当对象文字的结构太大或未知时，我建议使用`Record<string, any>` 或者如果你可以更具体一些，根据你的用例使用`Record<string, string|number>`或类似的东西。**

**我建议不要使用类型`any` 或不要像案例 1 和案例 2 那样声明类型。这降低了 TypeScript 的功能。**

**仅此而已。感谢阅读。**

```
**Want to Connect With the Author?**This article was originally posted on [realpythonproject.com](https://www.realpythonproject.com/how-to-declare-object-literals-in-typescript-the-right-way/)
```