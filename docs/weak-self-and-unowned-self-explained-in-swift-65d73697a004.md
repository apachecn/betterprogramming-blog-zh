# 斯威夫特解释了软弱的自我和无主的自我

> 原文：<https://betterprogramming.pub/weak-self-and-unowned-self-explained-in-swift-65d73697a004>

![](img/b4dd65136886d583b6a7e4596b28c27a.png)

由[大卫·艾姆里奇](https://unsplash.com/@otoriii)在 [Unsplash](https://unsplash.com/photos/TdeCF3XmDWg) 上拍摄的照片

斯威夫特中的弱小自我和无主自我，对于我们很多人来说，都是难以理解的。尽管自动引用计数(ARC)已经为我们解决了很多问题，但是当我们不使用值类型时，我们仍然需要管理引用。

# 什么是弧、保留和释放？

我们需要从基本面入手，才能充分理解弱自我和无主自我在做什么。通读 Swift 文档中的[自动引用计数可以最好地解释这些概念。这一切都归结于内存管理。](https://docs.swift.org/swift-book/LanguageGuide/AutomaticReferenceCounting.html)

在 ARC 之前，我们必须手动管理内存和引用。这导致了许多错误和令人头痛的问题。当一个新实例保留一个对象时，引用计数增加，而当一个引用被释放时，引用计数减少。一旦没有对对象的引用，也就是说不再需要该对象，就释放内存。

在 Swift 中，我们需要使用弱自我和无主自我来给 ARC 提供我们代码中关系之间所需的信息。它基本上告诉我们的代码是否需要某个强引用，并防止引用计数下降到零。

如果没有正确使用这些关键字，我们可能会保留内存，这可能会导致应用程序中出现内存警告。如果没有正确使用弱引用和无主引用，也会发生所谓的强引用循环或保留循环。

正如我在这篇博文中引用并解释的那样，很高兴知道:

> 引用计数仅适用于类的实例。结构和枚举是值类型，而不是引用类型，并且不通过引用来存储和传递。

# 何时使用弱自我

首先，弱引用总是被声明为可选变量，因为当它的引用被释放时，它们可以被 ARC 自动设置为`nil`。下面两个类将帮助解释何时使用弱引用。

```
class Blog {
    let name: String
    let url: URL
    var owner: Blogger?

    init(name: String, url: URL) { self.name = name; self.url = url }

    deinit {
        print("Blog \(name) is being deinitialized")
    }
}

class Blogger {
    let name: String
    var blog: Blog?

    init(name: String) { self.name = name }

    deinit {
        print("Blogger \(name) is being deinitialized")
    }
}
```

一旦释放了这些类中的任何一个，就会打印出一条消息。在下面的代码示例中，我们将两个实例定义为可选的，然后将它们设置为`nil`。虽然你们中的一些人可能期望两个打印语句，但这实际上并没有发生:

```
var blog: Blog? = Blog(name: "SwiftLee", url: URL(string: "www.avanderlee.com")!)
var blogger: Blogger? = Blogger(name: "Antoine van der Lee")

blog!.owner = blogger
blogger!.blog = blog

blog = nil
blogger = nil

// Nothing is printed
```

这是保留周期的结果。该博客对其所有者有强烈的引用，并且不愿意发布。同时，主人也不愿意免费开放它的博客。博客不发布它的所有者，谁保留它的博客，谁保留他自己…好吧，你明白了。这是一个无限循环:保留循环。

因此，我们需要引入一个弱引用。在本例中，只需要一个弱基准电压源，因为这已经打破了环路。例如，我们可以设置一个从博客到其所有者的弱引用:

```
class Blog {
    let name: String
    let url: URL
    weak var owner: Blogger?

    init(name: String, url: URL) { self.name = name; self.url = url }

    deinit {
        print("Blog \(name) is being deinitialized")
    }
}

class Blogger {
    let name: String
    var blog: Blog?

    init(name: String) { self.name = name }

    deinit {
        print("Blogger \(name) is being deinitialized")
    }
}

var blog: Blog? = Blog(name: "SwiftLee", url: URL(string: "www.avanderlee.com")!)
var blogger: Blogger? = Blogger(name: "Antoine van der Lee")

blog!.owner = blogger
blogger!.blog = blog

blog = nil
blogger = nil

// Blogger Antoine van der Lee is being deinitialized
// Blog SwiftLee is being deinitialized
```

# 但是弱小的自己呢？

我知道，这不是一个软弱的例子。不过，这的确解释了这个故事。

对于我们大多数人来说，最好的做法是始终使用弱结合的 self inside 闭包来避免保留循环。然而，这仅在 self 也保持闭合时需要。如果你总是默认为弱势，你可能比你需要的更多的使用期权

假设我们为博客文章引入了一种发布方法。请注意，在本例中，我们通过手动添加延迟来伪造网络请求。

```
struct Post {
    let title: String
    var isPublished: Bool = false

    init(title: String) { self.title = title }
}

class Blog {
    let name: String
    let url: URL
    weak var owner: Blogger?

    var publishedPosts: [Post] = []

    init(name: String, url: URL) { self.name = name; self.url = url }

    deinit {
        print("Blog \(name) is being deinitialized")
    }

    func publish(post: Post) {
        /// Faking a network request with this delay:
        DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
            self.publishedPosts.append(post)
            print("Published post count is now: \(self.publishedPosts.count)")
        }
    }
}

var blog: Blog? = Blog(name: "SwiftLee", url: URL(string: "www.avanderlee.com")!)
var blogger: Blogger? = Blogger(name: "Antoine van der Lee")

blog!.owner = blogger
blogger!.blog = blog

blog!.publish(post: Post(title: "Explaining weak and unowned self"))
blog = nil
blogger = nil
```

这将打印出以下内容:

```
// Blogger Antoine van der Lee is being deinitialized
// Published post count is now: 1
// Blog SwiftLee is being deinitialized
```

您可以看到请求在博客发布之前就已经完成了。强引用允许我们完成发布并将文章保存到我们发布的文章中。

如果我们要更改发布方法，改为包含弱引用:

```
func publish(post: Post) {
    /// Faking a network request with this delay:
    DispatchQueue.main.asyncAfter(deadline: .now() + 1) { [weak self] in
        self?.publishedPosts.append(post)
        print("Published post count is now: \(self?.publishedPosts.count)")
    }
}
```

我们将得到以下输出:

```
// Blogger Antoine van der Lee is being deinitialized
// Blog SwiftLee is being deinitialized
// Published post count is now: nil
```

由于博客在发布请求完成之前就已经发布了，我们将永远无法更新已发布帖子的本地状态。

因此，如果闭包一执行，引用实例就有工作要做，请确保不要使用 weak self。

# 弱引用和保留周期

一旦封闭保持自我，自我保持封闭，保持循环就发生了。如果我们有一个包含`onPublish`闭包的变量，这可能会发生:

```
class Blog {
    let name: String
    let url: URL
    weak var owner: Blogger?

    var publishedPosts: [Post] = []
    var onPublish: ((_ post: Post) -> Void)?

    init(name: String, url: URL) {
        self.name = name
        self.url = url

        // Adding a closure instead to handle published posts
        onPublish = { post in
            self.publishedPosts.append(post)
            print("Published post count is now: \(self.publishedPosts.count)")
        }
    }

    deinit {
        print("Blog \(name) is being deinitialized")
    }

    func publish(post: Post) {
        /// Faking a network request with this delay:
        DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
            self.onPublish?(post)
        }
    }
}

var blog: Blog? = Blog(name: "SwiftLee", url: URL(string: "www.avanderlee.com")!)
var blogger: Blogger? = Blogger(name: "Antoine van der Lee")

blog!.owner = blogger
blogger!.blog = blog

blog!.publish(post: Post(title: "Explaining weak and unowned self"))
blog = nil
blogger = nil
```

闭包保留着博客，而博客保留着闭包。这将导致打印以下内容:

```
// Blogger Antoine van der Lee is being deinitialized
// Published post count is now: 1
```

尽管计数为 1 时一切似乎都很好，但我们没有看到博客和发布者被去初始化。这是因为保留周期导致内存没有被释放。

在`onPublish`方法中添加对 blog 实例的弱引用解决了我们的保留周期:

```
onPublish = { [weak self] post in
    self?.publishedPosts.append(post)
    print("Published post count is now: \(self?.publishedPosts.count)")
}
```

并产生以下输出:

```
// Blogger Antoine van der Lee is being deinitialized
// Published post count is now: Optional(1)
// Blog SwiftLee is being deinitialized
```

数据保存在本地，所有实例都被释放。不再有保留周期。

最后，总结一下这一部分，很高兴知道当 ARC 对`nil`设置了一个弱引用时，没有调用属性观察者。

# 何时使用无主自我

与弱引用不同，在使用 unowned 时，引用不会变成可选的。尽管如此，无主的和弱的都不会创建强引用。

引自苹果文档:

> 只要弱引用在其生命周期的某个时刻变为零是有效的，就使用弱引用。相反，当您知道引用一旦在初始化过程中被设置就永远不会为零时，请使用无主引用。

一般来说，使用 unowned 时要非常小心。这可能是因为您正在访问一个不再存在的实例，从而导致崩溃。使用 unowned over weak 的唯一好处是您不必处理期权。因此，在这些情况下，使用 weak 总是更安全。

# 为什么我们不需要像 Structs 这样的值类型呢？

在 Swift 中，我们有[值类型和](https://developer.apple.com/swift/blog/?id=10)引用类型。

这已经让事情变得更清楚了，因为对于引用类型，你实际上需要处理一个引用。这意味着您需要将这种关系管理为强关系、弱关系或无主关系。

值类型保留其数据的唯一副本—唯一实例。这意味着在多线程环境中没有必要使用弱引用，因为没有引用，只有我们正在处理的唯一副本。

# 弱无主只和 self 一起用吗？

不，绝对不是。只要是引用类型，就可以将任何属性或变量声明为弱声明或无主声明。因此，这也可行:

```
download(imageURL, completion: { [weak imageViewController] result in
    // ...
})
```

您甚至可以引用多个实例，因为它基本上是一个数组:

```
download(imageURL, completion: { [weak imageViewController, weak imageFinalizer] result in
    // ...
})
```

# 结论

这是一个很难理解的话题。最好从阅读 [Swift 文档](https://docs.swift.org/swift-book/LanguageGuide/AutomaticReferenceCounting.html)开始，它会更深入。此外，如果你不确定，使用弱无主。它可以让你远离烦人的虫子。

谢谢！