# 用代码示例解释 Swift 中的自定义下标

> 原文：<https://betterprogramming.pub/custom-subscripts-in-swift-explained-with-code-examples-113ca73f1d90>

## 了解关于静态下标、默认下标等的所有信息

![](img/b2267735c9a31cde533dde446ca1dd4a.png)

由[戴夫·弗朗西斯](https://unsplash.com/@davefrancis101?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/fetch?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Swift 中的自定义下标允许您编写集合或序列中元素的快捷方式，并且可以在类、结构和枚举中定义。您可以使用下标来设置和检索值，而无需公开某个实例的内部细节。

一个实例可以定义多个下标，一个下标可以有多个输入参数。例如，您可能已经使用下标从数组或字典中获取元素。然而，你不太可能还定义了你自己的定制下标，尽管这些也非常有用。

# 什么是下标？

下标定义集合、列表或序列的元素的快捷方式。它可以在类、结构和枚举中定义，以允许快速访问某种类型的元素。

在整个标准 Swift 库中也经常使用它，例如，访问数组中的元素:

```
var array = ["Antoine", "Jaap", "Lady"]
print(array[0]) // Prints: Antoine
```

或者从字典中访问元素:

```
var agesDictionary = ["Antoine": 29, "Lady": 2, "Jaap": 1]
print(agesDictionary["Antoine"]) // Prints: 29
```

相同的下标也可用于添加新值:

```
array[0] = "Henk"
print(array[0]) // Prints: Henk

agesDictionary["Antoine"] = 30
print(agesDictionary["Antoine"]) // Prints: 30
```

如果没有这些下标，您可能会通过数组或字典上定义的方法来访问相同的内容。这些方法很容易让你的代码看起来不那么干净:

```
array.set("Henk", forIndex: 0)
agesDictionary.set(30, forKey: "Antoine")
```

请注意，这些方法实际上并不存在，因为我们现在在 Swift 中有下标来做同样的事情。

把上面的例子作为一个改进，也是你可以看看你自己的代码的方式。每当您发现自己在编写类似的方法时，您可以考虑编写自己的自定义下标。

# 创建自定义下标

可以定义自定义下标，就像在类、结构或枚举中定义方法一样。在我们深入这个逻辑之前，我想介绍一个图像缓存实例，我们将用下标来改进它。

```
final class ImageCache {
    static let shared = ImageCache()

    private var imageStore: [URL: UIImage] = [:]

    func image(for url: URL) -> UIImage? {
        imageStore[url]
    }

    func store(_ image: UIImage, for url: URL) {
        imageStore[url] = image
    }
}
```

这个`ImageCache`类负责缓存给定 URL 的图像。我们有一个向缓存中添加图像的方法和一个从缓存中检索图像的方法。

对于本例，我们将存储 SwiftLee 徽标，以便稍后可以用下标检索它。如果您正在编写这些代码，您可能希望对自己的图像做同样的事情。

```
let swiftLeeLogo = UIImage(named: "logo_256x256.jpg")!
let swiftLeeURL = URL(string: "https://www.avanderlee.com/logo.png")!
ImageCache.shared.store(swiftLeeLogo, for: swiftLeeURL)
```

*在这个例子中，我们静态缓存一个自定义 URL 的图像。通常，您会基于使用* `URLSession` *处理的实际图像请求来实现这样的缓存，并保存响应图像。*

现在我们已经设置了我们的图像缓存，我们可以开始编写我们的第一个自定义下标。

```
extension ImageCache {
    subscript(url: URL) -> UIImage? {
        imageStore[url]
    }
}
```

代码看起来非常简单:

*   它通过使用`subscript`方法名来定义自定义下标。
*   下标接受一个 URL 作为输入。
*   结果将是一个图像，如果它存在的话。

我们基本上利用了之前定义的图像存储。这个图像存储是一个字典，它将 URL 作为键，将图像作为值。这提供了一种基于 URL 获取图像的简单方法。

我们可以如下使用这个下标:

```
let swiftLeeURL = URL(string: "https://www.avanderlee.com/logo.png")!
let image = ImageCache.shared[swiftLeeURL]
```

# 做一个读写下标

上面的下标示例只允许您检索值，并且可以被视为只读下标。通过添加`get`和`set`闭包，我们可以很容易地使这个下标可写:

```
extension ImageCache {
    subscript(url: URL) -> UIImage? {
        get {
            imageStore[url]
        }
        set {
            imageStore[url] = newValue
        }
    }
}
```

这导致我们现在可以通过使用下标来存储新图像:

```
let avatarImage = UIImage(named: "avatar.jpg")!
let avatarURL = URL(string: "https://www.avanderlee.com/avatar.png")!

ImageCache.shared[avatarURL] = avatarImage
```

# 定义静态下标

静态下标允许您通过在静态类型上使用下标来编写快捷方式。这允许您公开更少的实例。

在我们的图像缓存示例中，我们通过定义`shared`属性定义了一个共享实例。如果没有静态下标，我们必须总是通过共享属性来访问下标:

```
ImageCache.shared[swiftLeeURL]
```

这也要求我们公开`shared`属性，尽管这可能不是一开始的本意。

我们可以通过定义一个静态下标来解决这个问题，在这个下标中我们访问共享实例:

```
extension ImageCache {
    static subscript(url: URL) -> UIImage? {
        get {
            shared.imageStore[url]
        }
        set {
            shared.imageStore[url] = newValue
        }
    }
}
```

代码几乎是一样的。事实上，我们唯一改变的是添加了 static 关键字，并通过`shared`属性访问了`imageStore`。

我们现在可以将`shared`属性设为私有，并通过静态下标检索 URL 的图像:

```
// Without the static subscript:
let image = ImageCache.shared[swiftLeeURL]
ImageCache.shared[swiftLeeURL] = newImage

// With the static subscript:
let image = ImageCache[swiftLeeURL]
ImageCache[swiftLeeURL] = newImage
```

这很好，并且允许我们尽可能少地暴露实现细节。

# 向下标添加可选参数

下标中的可选参数允许您添加额外的功能。例如，我们可以只根据基本 URL 获取图像:

```
extension ImageCache {
    subscript(url: URL, useBaseURL: Bool = false) -> UIImage? {
        if useBaseURL {
            return imageStore.first { (storedImage) -> Bool in
                storedImage.key.baseURL == url.baseURL
            }?.value
        } else {
            return imageStore[url]
        }
    }
}
```

*请注意，我们将此下标设为只读，因为我们不想让图像缓存的用户只为基本 URL 保存图像。最好为特定的 URL 保存一个图像。*

我们现在可以仅使用基本 URL 检索相同的已存储 SwiftLee 徽标:

```
let image = ImageCache.shared[URL(string: "https://www.avanderlee.com")!, true]

// Passing false will try to find an image for the exact URL, which fails as we did not store an image for that URL.
let nilImage = ImageCache.shared[URL(string: "https://www.avanderlee.com")!, false]
```

这是向自定义下标添加额外功能的好方法。

# 结论

自定义下标是一种很好的方式，可以让您的代码更具可读性，同时披露更多的实现细节。下标可用于整个 Swift 库，例如数组和集合，并为您提供设置和检索值的快捷方式。您可以在结构、类和枚举上定义自定义下标。

谢谢！