# 在 GitHub 中应该使用哪种代码合并方法？

> 原文：<https://betterprogramming.pub/which-code-merging-methods-to-use-in-github-c2a3adfa0d54>

## 我应该挤压并合并，重定基础并合并，还是只进行合并提交？

![](img/e472bb984ce90e53e6dda8d1e6deab54.png)

照片由[尼克·费因斯](https://unsplash.com/@jannerboy62?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/highway?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

*创建合并提交？*

*挤压合并？*

*Rebase 和 Merge？*

它们都是什么意思？什么时候应该使用一个而不是另一个？

如果你和我一样，你已经接触过 GitHub 中不同的库，开发者在那里做不同的事情。一些人非常坚持应该如何合并拉请求，而另一些人则不这么认为，只要工作被很好地记录。

我将分解 GitHub 中提供的每个可用的合并方法。希望我们能减少合并栅栏两边的混乱和冲突。

让我们调查一些利弊。

## 创建合并提交

这是 GitHub 在打开一个 pull 请求时的默认合并选项。该方法获取分支内的所有提交，并将它们合并，创建另一个新的提交，称为“合并提交”

该方法将在您要合并到的分支的提交历史中创建一个全新的提交。默认情况下，提交看起来相当普通，说了一些类似于`merging pull request <pr>`的话。不太好描述。

是的，您可以查看原始的 pull 请求来查看其中的提交，但是这需要额外的步骤。如果有什么东西是开发人员讨厌的，那就是额外的步骤。

如果你有一个非常活跃的分支，这个方法很有用。当处理高提交量时，能够将提交打包成单个提交是有优势的，但是它混淆了底层的更改。

这是该合并方法的命令行等效项:

```
git merge --no-ff
```

## 挤压和合并

这个方法将请求中的提交“压缩”成一个提交。您有机会编辑提交消息，并根据需要从压缩的提交中包含详细信息。

在 Git 历史中，这种方法比创建合并提交更清晰，但是它仍然依赖于单个合并提交被合并。当您有许多小的或任意的提交，可能不保证一个完整的提交消息时，挤压是有用的。当您在一个分支上有较大的特性提交，而您可能想要单独合并时，挤压就不那么有用了。

此合并方法的命令行等效项是:

```
git merge --ff-only
```

## 重设基础并合并

这就是你一直在等的人(可能)。使用`Rebase`和`merge` ，您可以将所有这些可爱的提交放入您的分支，并将它们逐个合并到目标分支中。没有合并提交，没有挤压——只需选择所有现有的提交，并将它们应用到分支。

当您想用 pull 请求中所有提交的完整细节维护一个更清晰的 git 历史时，这个方法很有用。如果您对一个分支的详细消息进行了重要的提交，并希望它们被很好地合并以保持细节，这是可以使用的方法。

要在命令行上执行这种类型的合并，您应该执行类似于以下的操作:

```
git checkout <feature_branch>
git rebase <target_branch>
```

## 结论

合并代码可能会令人困惑、复杂，有时压力很大。Git 不是世界上最简单的工具，有时当你不小心时，它会反咬你一口。GitHub 提供了不同的合并方法，以适应不同的用例。希望在检查完这些方法之后，您能够更好地确定最适合您的环境的方法。

如果你想了解每种合并方法的更多详细信息，GitHub 在其支持网站上提供了一个[的精彩概要](https://help.github.com/en/github/administering-a-repository/about-merge-methods-on-github)。此外，从管理角度在 GitHub 上实施这些方法也很容易。查看需要线性提交历史的[](https://help.github.com/en/github/administering-a-repository/requiring-a-linear-commit-history)*了解更多关于如何配置的信息。*

**感谢阅读！我希望我已经减轻了围绕这些合并方法的一些困惑，并且您对为自己的存储库选择一个合并方法更有信心。**