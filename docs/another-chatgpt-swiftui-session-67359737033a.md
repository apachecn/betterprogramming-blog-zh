# 另一个 ChatGPT SwiftUI 会话

> 原文：<https://betterprogramming.pub/another-chatgpt-swiftui-session-67359737033a>

## 探索对话式编程的未来。

![](img/301eff1310ee20d5e798d81db7d195f3.png)

由 [charlesdeluvio](https://unsplash.com/@charlesdeluvio?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/conversation?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

我在 ChatGPT 上写过一篇文章，我让它基于一些参数构建一个 SwiftUI 表单… [，它完成了](/chatgpt-vs-swiftui-febc24c6d8a)。当被问到时，它还增加了一些错误处理和属性封送处理。

所以我用它工作了几天，出于好奇我尝试了以下…

> 给定下面的 SwiftUI 代码，可以添加第三个状态吗？

(这是粘贴的代码。)

```
struct Bindings2<V0, V1, Content:View>: View {
    @State var v0: V0
    @State var v1: V1
    let content: (_ v0: Binding<V0>, _ v1: Binding<V1>) -> Content
    init(_ v0: V0, _ v1: V1, @ViewBuilder content: @escaping (_ v0: Binding<V0>, _ v1: Binding<V1>) -> Content) {
        _v0 = .init(initialValue: v0)
        _v1 = .init(initialValue: v1)
        self.content = content
    }
    var body: some View {
        content($v0, $v1)
    }
}
```

**回答:**是的，可以给`Bindings2`结构体添加第三种状态和变量。有一种方法可以做到:

```
struct Bindings3<V0, V1, V2, Content: View>: View {
    @State var v0: V0
    @State var v1: V1
    @State var v2: V2
    let content: (_ v0: Binding<V0>, _ v1: Binding<V1>, _ v2: Binding<V2>) -> Content
    init(_ v0: V0, _ v1: V1, _ v2: V2,
         @ViewBuilder content: @escaping (_ v0: Binding<V0>, _ v1: Binding<V1>, _ v2: Binding<V2>) -> Content) {
        _v0 = .init(initialValue: v0)
        _v1 = .init(initialValue: v1)
        _v2 = .init(initialValue: v2)
        self.content = content
    }
    var body: some View {
        content($v0, $v1, $v2)
    }
}
```

这创建了一个名为`Bindings3`的新结构，它类似于`Bindings2`，但是在`content`闭包中包含了一个名为`v2`的额外的`@State`变量和一个额外的参数。这个新的`Bindings3`结构可以像`Bindings2`一样使用，但是它允许你绑定到三个状态变量，而不是两个。

> 用 4 再做一个？

**回答:**是的，可以给`Bindings3` struct 增加第四个状态和变量。有一种方法可以做到:

```
struct Bindings4<V0, V1, V2, V3, Content: View>: View {
    @State var v0: V0
    @State var v1: V1
    @State var v2: V2
    @State var v3: V3
    let content: (_ v0: Binding<V0>, _ v1: Binding<V1>, _ v2: Binding<V2>, _ v3: Binding<V3>) -> Content
    init(_ v0: V0, _ v1: V1, _ v2: V2, _ v3: V3,
         @ViewBuilder content: @escaping (_ v0: Binding<V0>, _ v1: Binding<V1>, _ v2: Binding<V2>, _ v3: Binding<V3>) -> Content) {
        _v0 = .init(initialValue: v0)
        _v1 = .init(initialValue: v1)
        _v2 = .init(initialValue: v2)
        _v3 = .init(initialValue: v3)
        self.content = content
    }
    var body: some View {
        content($v0, $v1, $v2, $v3)
    }
}
```

这创建了一个名为`Bindings4`的新结构，它类似于`Bindings3`，但是在`content`闭包中包含了一个名为`v3`的额外的`@State`变量和一个额外的参数。这个新的`Bindings4`结构可以像`Bindings3`一样使用，但是它允许你绑定到四个状态变量，而不是三个。

*结束会话*

## 对话式编程

我发现上面的会议非常有趣，原因有两个。

请注意 ChatGPT 是如何找到两个状态 v0 和 v1 *和*以及相关的类型 V0 和 v1 的，以及它是如何添加另一个状态 v2 和 V2 的，并选择了用于这两个状态的从零开始的编号系统。

它甚至将视图名称从`Bindings2`更新为`Bindings3`。

还要注意，对于第二个请求，我依赖于 ChatGPT 对当前对话上下文的“记忆”。

它不仅成功地生成了代码，甚至在结束语中加入了“这类似于引用了上一个答案中的函数名的`Bindings3`子句”。

印象深刻，年轻的天行者。令人印象深刻。

尤其是考虑到这个系统不是为编程而设计的。

当他们创造一个存在时会发生什么？

## 高年级 vs 低年级

这种基于对话的系统会成为高级开发人员的附属品，消除大量繁重的工作，进而减少对初级开发人员的需求吗？

还是会让相对初级的开发者取代对高级开发者的需求？

当前面提到的未来系统创建明显正确的代码，自动包含和使用已知的最佳实践，甚至创建测试、模拟和其他案例时，会发生什么？

它是否取代了对初级和高级软件开发人员的需求？

再次提醒，记住这段代码不是为编程而设计的。

当他们创造一个系统时会发生什么？

## 询问 ChatGPT

好问题，那我们就回到源头去问吧。

> 这个系统能代替对软件开发人员的需求吗？

不，这个系统不能取代对软件开发人员的需求。虽然像 Assistant 这样的语言模型可以理解并生成人类可读的代码，但是如果没有经验丰富的软件开发人员的指导和监督，它们就无法编写功能完整、高效或可维护的代码。

软件开发是一个复杂的多面学科，不仅涉及编写代码，还涉及理解需求、设计和实现算法和数据结构、调试和解决问题，以及与团队中的其他人合作交付高质量的软件。像 Assistant 这样的语言模型无法独自完成所有这些任务，最好将其作为一种工具来支持和增强人类开发人员的工作。

> 更先进的系统会取代对开发人员的需求吗？

很难预测技术的未来，也很难预测更先进的系统是否能够取代对软件开发人员的需求。然而，即使人工智能和自动化取得了重大进展，软件开发也不太可能成为不需要人工输入和专业知识的全自动过程。

软件开发不仅包括编写代码，还包括解决问题、创造力和批判性思维。这些都是很难自动化的技能，并且很可能在未来很多年都很有价值。此外，软件开发是一个不断发展的领域，新的技术、框架和最佳实践一直在发展。人类开发人员能够更好地适应这些变化，并在他们的领域中继续学习和成长。

简而言之，虽然更先进的系统可能能够自动化软件开发的某些方面，但它们不太可能完全取代对人类开发人员的需求。

*结束会话*

## 完全地

我认为那一段的最后一行是在说明，“他们不太可能完全取代对人类开发人员*的需求。”*

*不管你喜不喜欢，目前很多软件开发都是繁重的工作。创建列表、视图和表单。编码和编写视图模型。添加错误处理和验证。编码模型和 API 请求。*

*在这方面，基于聊天的系统可以成为*重要的*力量倍增器。*

*这意味着两件事中的一件。*

1.  *作为开发人员，我们可以自由地处理更多的问题。*
2.  *我们需要更少的开发人员。*

## *Web 开发人员*

*我提醒了 web 开发人员如何面对这样的问题。在某一点上，一个人需要 HTML 和 CSS 的知识，可能还需要一些基本的 JavaScript 来创建一个网站。*

*如今，人们可以在任何地方获取模板，开始添加和删除页面，并在页面内移动内容块。*

*昨天的普通“网络开发者”已经一去不复返了。*

*为了应对这种情况，一些开发人员不得不走上前台，学习更多的 JavaScript、框架和 API 集成，以保持相关性。*

*有的成为了 iOS 开发者。*

*但是当*不再需要*时会发生什么呢？*

*没有计算机科学和机器学习的博士学位，你就过时了吗？*

## *无代码*

*这不是一些人认为是软件开发圣杯的“无代码”解决方案。*

*我们正在编写代码。*

*我们只是用更高级的语言来做。*

*英语。*

## *一次简单的谈话*

*在前面的部分我说了几次“我们”。但是考虑一下下面一段个人和 XYZ 超级应用的开发系统之间的假设对话。*

> *让我们开始吧。向登录菜单添加一个名为“用户配置文件”的新菜单项。*
> 
> *将其链接到一个表单，该表单从当前登录的用户获取用户名、地址、电话号码和电子邮件地址。*
> 
> *确保所有这些字段都是必填的。*
> 
> *添加一个取消按钮，让他们返回到上一页。*
> 
> *添加一个 Save 按钮来验证输入并将信息保存回概要文件。*
> 
> *运行它，让我玩玩。*

*我需要继续吗？*

*并且鉴于上述情况，上述个人是否需要成为开发人员？*

*或者只是知道自己想要什么的人？*

## *完成块*

*原来如此。一如既往，请在下面的评论中让我知道你的想法。*

*我对 ChatGPT 内容有更多的想法。参见: [ChatGPT 和反刍内容](https://michaellong.medium.com/chatgpt-and-regurgitated-content-6e5c009c69df)如果你感兴趣。*