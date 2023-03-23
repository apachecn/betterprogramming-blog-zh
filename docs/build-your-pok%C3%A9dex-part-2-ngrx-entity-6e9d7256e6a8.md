# 构建您的 Pokédex:第 2 部分— @ngrx/entity

> 原文：<https://betterprogramming.pub/build-your-pok%C3%A9dex-part-2-ngrx-entity-6e9d7256e6a8>

## 角度教程— NgRx 分步指南

![](img/c1e6d1f27afb705aad46cb2b8a7b0766.png)

这篇文章是我描述如何从初学者到忍者使用 [NgRx](https://ngrx.io/) 构建你的 [Pokédex](https://www.pokemon.com/us/pokedex/) 的系列文章的一部分。如果您想阅读更多内容，可以在本文底部找到该系列其余部分的链接。

# 介绍

有必要阅读这篇文章的第一部分，这样你就能理解我们在构建什么。在这篇文章中，我们将使用`@ngrx/entity`包改进第一篇文章中开发的代码，这将简化创建缩减器和选择器的任务。

# @ngrx/entity

`@ngrx/entity`包是一个用于管理记录集合的适配器。这个包提供了一个 API 来操作和查询实体集合。

因此，它减少了创建管理模型集合的 reducer 的样板文件。它为管理实体集合提供了高性能的 CRUD 操作。它是一个可扩展的类型安全适配器，用于选择实体信息。

第一步是安装将提供所有这些优势的软件包。

```
npm i @ngrx/entity
```

# 实体状态

`EntityState`是给定实体集合的预定义通用接口，具有以下接口:

我们在上一篇文章中用这两个属性开发`PokemonState`的原因如下所示。通用属性如下:

*   `ids`。集合中所有主要 id 的数组。
*   `entities`。集合中由主 ID 索引的实体的字典。

如下图所示,`pokemon.state.ts`文件被替换为`pokemon.adapter.ts`。

之前:

之后:

在我们的新模型中，我们已经使用继承基于`EntityState`创建了别名`PokemonState`，尽管我们还没有为状态添加任何新的属性。

另一方面，`createEntityAdapter`方法负责为我们的`Pokemon`数据模型创建适配器。这个适配器为我们提供了修改状态和创建选择器的方法集合。

方法接受具有两个属性的对象进行配置:

*   `selectId`。为集合选择主 ID 的方法。当实体的主键为 ID 时，该选项是可选的。
*   `sortComparer`。用于对集合排序的比较函数。只有当集合需要在显示之前进行排序时，才需要比较功能。将它设置为 false 可以使集合不排序，这样在 CRUD 操作期间性能会更好。

在我们的例子中，我们没有使用任何配置属性，因为这是不必要的。

# 还原剂

返回的适配器对象提供了一组方法，您可以在 reducer 函数中使用这些方法来根据您提供的操作管理实体集合。

*   `getInitialState`。基于提供的类型返回实体状态的`initialState`。`initialState`是提供给你的减速器功能。在我们的例子中，这个函数被包装在`pokemonInitialState`中。

# 适配器收集方法

实体适配器还提供针对实体的操作方法。这些方法可以一次更改一个或多个记录。

如果进行了更改，每个方法都返回新修改的状态，如果没有进行更改，则返回相同的状态。

*   `addOne`:向集合中添加一个实体。
*   `addMany`:向集合中添加多个实体。
*   `addAll`:用提供的集合替换当前集合。
*   `removeOne`:从集合中删除一个实体。
*   `removeMany`:通过 ID 或谓词从集合中删除多个实体。
*   `removeAll`:清除实体集合。
*   `updateOne`:更新集合中的一个实体。
*   `updateMany`:更新集合中的多个实体。
*   `upsertOne`:添加或更新集合中的一个实体。
*   `upsertMany`:添加或更新集合中的多个实体。
*   `map`:通过定义一个映射函数来更新集合中的多个实体，类似于`Array.map`。

这组方法允许我们简化 reduce 函数，在这个函数中我们有复杂的嵌套对象来修改属性。

这样，请注意这个函数的 before 和 after，其中的代码被简化为调用一个方法。

之前:

之后:

# 选择器

由创建的实体适配器返回的`getSelectors`方法提供了从实体中选择信息的函数。

因此，最广泛使用的四种选择器是:

最后，`pokemon.selector.ts`文件被稍微修改了一下，因为我们不需要构建`selectAll`选择器，因为我们将使用适配器提供的选择器。

之前:

之后:

# 结论

在这篇文章中，我们使用`@ngrx/entity`包重构了一个 Pokédex 的小例子。

适配器的使用将减少应用程序状态管理中不必要的复杂性。

适配器允许我们根据需要轻松地扩展状态，并且在处理应用程序的状态时具有最常用的操作。

因此，在这篇文章中，我们讨论了以下主题:

*   分离可视组件的状态管理。
*   高效、轻松地创建状态管理元素。
*   创建关注相关性的组件:视图。
*   使用`@ngrx/entity`自动创建状态，因为这是非常重复的。

本系列中的其他帖子将涵盖有趣的主题，例如:

*   自动创建效果和动作，并使用`@ngrx/entity`简化 reduce 函数。
*   通过`@ngrx/data`包使用外观模式。
*   测试应用程序的状态。

重要的是概念，而不是使用的技术或库。因此，对于那些开始大型 [Angular](https://angular.io/) 应用并需要应用架构原则的人来说，这篇文章应该作为一个指南。

# 该系列的其他部分

1.  第一部分。构建你的 pokédex:NgR 简介
2.  [第二部分。构建您的 Pokédex: @ngrx/entity](https://medium.com/better-programming/build-your-pok%C3%A9dex-part-2-ngrx-entity-da176edffdfb)
3.  第三部分。构建您的 Pokédex:使用 create*函数改进 NgRx(即将推出)
4.  第四部分。构建您的 Pokédex: @ngrx/data(即将推出)
5.  第五部分。构建您的 Pokédex:测试 NgRx(即将推出)

# 资源

*   [GitHub 分支](https://github.com/Caballerog/ngrx-pokedex/tree/ngrx-part2)为本岗位。
*   [角度架构最佳实践](https://angular-academy.com/angular-architecture-best-practices/)
*   [棱角分明的建筑(官方文件)](https://angular.io/guide/architecture)
*   [NgRx](https://ngrx.io/)