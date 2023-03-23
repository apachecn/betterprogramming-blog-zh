# 用 Elixir 解析大 Xml 时的流输出

> 原文：<https://betterprogramming.pub/stream-output-when-parsing-big-xml-with-elixir-92baff37e607>

## 嗯，这对我来说很难发现，因此，我想分享一下。

![](img/8c482e732dd22970b8453890c9136092.png)

# 工具

在 elixir 的 XML 解析生态系统中有两个大玩家:

*   `SweetXml`，它是传统的“解析器”,当你向它提供一些带有 XML 内容的字符串或流时，它会产生一些元素结构，其中每个元素都是`SweetXml`认为 XML 元素应该表示的。
*   `Saxy`，基于 SAX 解析。您为它提供一个模块和一些状态。该模块要知道在读取一些包含 xml 内容的字符串或流时，当发生 sax xml 相关事件(例如“start-document”、“start-element”等)时该做什么。该模块将根据这些事件按照提供给他的状态做事。

# 我想达到的目标

我想读取一个巨大的 XML 文件，其中有一些重复的元素，并想从中产生某种“迭代器”。大概是这样的:

给定 XML 文件:

```
<rss>
  <item>
    <title>I'm a title</title>
    <id>xx1</id>
  </item>
  <item>
    <title>second title</title>
    <id>xx2</id>
    <extra>extra content</extra>
    <whatever>
      <some>thing</some>
      <some>thing2</some>
    </whatever>
  </item>
  <item>
    <extra>thing</extra>
  </item>
</rss>
```

我想产生一些迭代器，当迭代时，产生这样的结果:

```
[
  {"title": "I'm a title", "id": "xx1"}, 
  {
    "title": "second title", 
    "id": "xx2", 
    "extra": "extra content", 
    "whatever": {
      "some": ["thing", "thing2"]
    }
  }, 
  {"extra": "thing"}
] 
```

# 我不需要的是

*   我不需要在内存中保存代表整个 xml 文件的结构。
*   我甚至不需要将整个条目列表保存在内存中，因为我可以使用某种“迭代器”,这意味着我每次只需要在内存中保存一个`item`

# 这些工具为我提供了什么“开箱即用”

`Saxy`是**难以置信的快和高性能**，但是它基于这样的概念，当你读取 XML 文件时，你“填充”一些状态对象(用你想要的任何东西，和你想要的数量，但是，不管怎样，你填充它)。

在这个场景中，我可以用项目列表“填充”状态。当然，这比在内存中保存整个 XML 结构要少得多。但它仍然在 XML 文件的大小和存储在内存中的列表的大小之间建立了一种关系，我不喜欢这种关系，因为这意味着如果我使用一个足够大的文件，我可能会消耗比我允许的更多的内存。

`SweetXml`提供了一个叫做`stream_tags`的功能，当你看到它做什么的时候，你会发现它很棒！！！因为它说这正是我所需要的:解析一个 xml，当它找到某些标签时，流传输它们的`SweetXml`表示，并且它没有在内存中构建任何表示 xml 的结构。所以这应该是我所需要的:

```
iex > list_iterator = File.stream("some_feed.xml") |> SweetXml.stream_tags!(:item, discard: [:item])
```

就这样了。`list_iterator`不是*整个列表*而是它的一个迭代器，这意味着*我不需要在内存中保存整个列表*。所以从理论上讲，`"some_feed.xml"`可以是我想要的大小，并且没有内存损失。但是…

不是这样的。我不知道确切的原因，但是有一些积累，这意味着有一些内存囤积，这意味着对于足够大的 xml 文件，我将超过我的内存限额。

# 我最终做的是:“欺骗”saxy

这个想法是，即使 saxy 在一些`state`中“积累”了一些东西，在解析期间，只要我需要，我就会“清理”这些`state`，这样`state`就不会消耗很多内存。

`Saxy`可以通过几次“部分”尝试来解析 XML 文件(尽管它累积到的`state`保持不变)，如下所示:

```
{:ok, partial} = Partial.new(MyEventHandler, initial_state)
{:cont, partial} = Partial.parse(partial, "<foo>")
{:cont, partial} = Partial.parse(partial, "<bar></bar>")
{:cont, partial} = Partial.parse(partial, "</foo>")
{:ok, state} = Partial.terminate(partial)
```

同样，诀窍是确保`initial_state`不时地被“清空”。

# 戈尔细节

## 处理程序:当发现 sax 事件时做什么

首先，我们必须构建一个模块，当在 XML 解析期间发现 XML 事件时，该模块知道如何处理`state`:

如你所见:

*   提供给解析器的初始状态是`%{current_element: nil, stack: nil, items: []}`，也就是说，初始状态包含一个空的项目列表
*   当模块解析器完成解析一个`<item>`元素时，一个新的`item`被添加到状态中
*   因此，在解析过程中的任何时候，state(在键`items`下)包含一个到目前为止已解析项目的列表。如果我们什么都不做，那么在解析了整个 xml 文件之后，我们可以获取从该文件中获得的整个解析项列表，这对于不太大的 XML 文件来说是可以的，但是，*如果 XML 文件足够大，那么累积的项列表也会很大，这就意味着要吃掉很多内存！。*

# 在解析 xml 文件时清理状态，这样它就不会变得太大

我们想要的输出是一个`Stream`，所以唯一的内存消耗是保存一个项目的内存。为了做到这一点，我们必须*流化* XML 解析，这要感谢`Saxy.Partial`模块。在我们这样做的同时，我们还负责*产生已经处理的项目，并将它们从状态中移除。通过这种方式，我们可以确定`state`永远不会增长太多。*

正如你所看到的，只要它解析 XML，它就生成一个流，其中每个元素都是一些条目的列表，当检索这些条目时(通过`fetch_items`函数)，我们也从`state`中删除这些条目，因此`state`永远不会保存太多条目，因此`state`永远不会占用太多内存！！！

哇！这很难解释清楚(我不得不说，我不确定我是否成功做到了)。无论如何，代码是真实的，我真的希望它能帮助别人。