# 构建 Xcode 项目的最佳方式

> 原文：<https://betterprogramming.pub/best-way-to-structure-your-xcode-project-23327999fdc5>

## 像真正的专家一样组织您的项目文件

![](img/bbf0b047aded451d4a45f65abd57d228.png)

在 [Unsplash](https://unsplash.com/s/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上 [Max Duzij](https://unsplash.com/@max_duz?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

当你开始一个全新的 Xcode 项目时，在创建项目之后，要做的第一件事是，开始将文件组织到组和文件夹中。这是因为您可以更轻松地继续您的项目，并且可以更轻松地找到您需要的文件。因此，生产率提高了，你节省了大量时间。

但是，组织它们的最佳方式是什么呢？

在组织项目中的文件时，有两种最常见的方法。按类型和功能组织代码文件。

# 按类型组织文件

按类型组织文件是我在项目中看到的最常见的组织方式，人们很喜欢它。老实说，这对于小项目来说是可以的，但对于大项目来说不是一个好的做法。

这样，你把所有的`Models`放在一个文件夹中，所有的`Views`放在另一个文件夹中，所有的`Controllers`放在第三个文件夹中，以此类推。

*类似这样的:*

```
• AppDelegate• View Controllers • Feature 1 VCs • Feature 2 VCs • Feature 3 VCs• Models • Feature 1 Models • Feature 2 Models • Feature 3 Models• Views • Feature 1 Views • Feature 2 Views • Feature 3 Views• Extensions • Feature 1 Extensions • Feature 2 Extensions • Feature 3 Extensions• Networking• Resources
```

但是为什么我不推荐这种组织文件的方式呢？

## 很难通过文件进行管理

想象一下，您有大量按类型组织的文件和文件夹，当您处理单个功能时，您必须打开所有的文件夹。这可能会让你感到困惑，当你滚动浏览文件时，你可能会迷路很多次。

## 用 Git 很难管理

这样，当多个开发人员在一个项目上工作，并且您为多个特性创建了多个分支时，当您合并或重定基础时，就会增加发生冲突的机会。这是因为对于一个单一的特性，你可能需要进入任何一个文件，而这个文件同时又是从其他开发者那里修改来的，这可能会产生冲突，需要你花更多的时间来解决。

# 按特征组织文件(意图)

根据特性(意图)组织代码是大项目和大团队的最佳实践。您将与该功能相关的所有内容都放在一个文件夹中，当您处理该功能时，您不必打开所有其他文件夹(组)。

因为通常公司会根据一个特征来划分团队。因此，团队致力于单个功能，在这种情况下，他们只关注单个文件夹或一组文件。他们不一定需要了解其他功能和文件。这很容易通过文件来管理，并且在使用 Git 时，可以避免通过文件产生许多冲突。

它看起来像这样:

```
• AppDelegate• Features • Feature 1 • View Controllers • Models • Views • Logic • Feature 2 • View Controllers • Models • Views • Logic• Networking • Models • Logic • Extensions• Resources
```

希望你学到了新东西。如果你想学习如何组织文件内部的代码，请查看这篇文章。

快乐编码。