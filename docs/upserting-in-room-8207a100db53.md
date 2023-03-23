# 如何在室内使用向上插入

> 原文：<https://betterprogramming.pub/upserting-in-room-8207a100db53>

## 避免插入或替换的潜在危险

![](img/001b096263d0e191d0d8d440ebff9818.png)

照片由 [Jungwoo Hong](https://unsplash.com/@hjwinunsplsh?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

SQLite 在 3.24 版本中增加了“向上插入”功能。向上插入意味着更新已经存在的行，否则插入新行。不幸的是，SQLite 版本 3.24 直到 API 级别 30 才在 Android 上可用，所以它不被 Room 支持(尽管团队[已经表示计划](https://issuetracker.google.com/issues/208869468)在未来的更新中添加`@Upsert`)。但是，通过一个简单的 DAO 函数，我们可以在我们的 Android 应用程序中添加 upsert 功能。

在我们开始之前，让我们考虑一下为什么这个话题很重要。乍一看，这似乎是一个已经解决的问题。我们已经可以使用带有替换现有行的冲突策略的 insert。

```
@Insert(onConflict = OnConflictStrategy.*REPLACE*)
```

那么，为什么要担心增加插入呢？微妙的答案是，这些可以是稍微不同的问题的解决方案。当我们使用“替换”冲突策略进行插入时，实际上删除了旧行，并添加了新行。当我们向上插入时，我们实际上是在更新*现有的*行。这一点特别重要的一个场景是外键被配置为级联删除。这正是我所面临的情景，它引导我寻找 upsert 功能。

假设我们有一个表`Notebooks`和一个表`Notes`，其中两者之间有一对多的关系。每个笔记本都有一个惟一的 id 作为主键，每个便笺都有一个外键来指示它所属的笔记本。

现在假设用户编辑笔记本的标题。假设我们有一个单独的添加/编辑屏幕，从这里我们调用我们的标准 DAO 插入函数。

```
@Insert(onConflict = OnConflictStrategy.*REPLACE*)
fun insertNotebook(notebook: Notebook)
```

这将允许我们为这个笔记本插入一个新行(如果我们还没有新行的话)(如果这是一个“添加”)，或者替换现有的行(如果这是一个“编辑”)。但是你看到问题了吗？我没有。

当我们使用“替换”冲突策略进行插入时，实际上删除了旧行，并添加了新行。一旦旧行被删除，级联删除就会被触发，notes 表中带有这个外键的所有行都会被删除。在此操作之后，我们将在笔记本表中拥有完全相同的数据，但会从注释表中删除所有相应的注释。说一个调试噩梦。我们需要一个解决方案来更新行，而不是删除并插入它。我们需要升级。

理想情况下，Room 会为我们提供一个`@Upsert`注释来处理这个问题。因为我们没有，所以让我们看看如何手动增加插入。首先，我们需要移除`OnConflictStrategy`,这样插入冲突就会抛出异常。我们将在 upsert 函数中依赖于此，该函数将在`try/catch`块中包装一个`insert`。如果`insert`抛出一个`SQLiteConstraintException`，那么我们知道该笔记本已经有一行，我们可以调用`update`来代替。这是代码。

值得一提的一个细微差别是向上排序列表。房间支持在实体列表上使用`@Insert`或`@Update`。这既提供了便利，也提高了性能，因为 Room 将批量插入作为单个事务来执行，SQLite 带来了巨大的性能提升。

不幸的是，我们不能像这样向上插入一个列表，因为任何一行上的冲突都会导致整个事务回滚。这意味着要向上插入一个列表，我们需要遍历列表和每个单独的插入。

请注意，这可能会对大型列表的性能造成重大影响。如果您在一个清单上调用 upsert，而您预计该清单有 20 个项目，那么这种差异可以忽略不计。如果你在一个有 2000 个项目的列表上调用 upsert，你需要密切监视它，并可能采用不同的方法。

就是这样！向上，向上插。如果你觉得这有帮助，请告诉我。更好的是，顺便提一下我在打开的问题，让房间团队知道添加一个`@Upsert`注释会使这篇文章过时。