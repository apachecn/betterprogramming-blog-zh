# 使用 Codable 解析 Swift 中的 JSON

> 原文：<https://betterprogramming.pub/use-codable-to-parse-json-in-swift-b937f59b63a4>

## 带有代码示例

![](img/9f547cf4505d4b8d80abf99883962484.png)

由 [bohemienne](https://pixabay.com/users/bohemienne-123410/) 在 [Pixabay](https://pixabay.com/illustrations/minimalism-simplify-downsizing-241876/) 上的插图

Swift 中的 JSON 解析是一件很常见的事情。几乎每个 app 都解码 JSON，以可视化的方式展现数据。解析 JSON 绝对是作为 iOS 开发人员应该学习的基础知识之一。

在 Swift 中解码 JSON 非常容易，不需要任何外部依赖。Swift 附带的基本 API 足以完成这项工作，所以让我们开始吧！

# JSON 解码的基础

最好从基础开始，让您了解 Swift 中的 JSON 解析是如何工作的。让我们以 SwiftLee 的博客帖子为例:

```
{
	"title": "Optionals in Swift explained: 5 things you should know",
	"url": "https://www.avanderlee.com/swift/optionals-in-swift-explained-5-things-you-should-know/",
	"category": "Swift",
	"views": 47093
}
```

通过使用`Decodable`协议，我们可以很容易地对此进行解码:

```
struct BlogPost: Decodable {
    enum Category: String, Decodable {
        case swift, combine, debugging, xcode
    }

    let title: String
    let url: URL
    let category: Category
    let views: Int
}
```

我们定义了一个符合`Decodable`协议的`Category`枚举。所有属性都与我们定义的 JSON 示例中的名称相匹配。每个符合`Decodable`协议的类型都会自动转换。这意味着你也可以使用你自己定制的`Decodable`类型作为属性。

通过使用`JSONDecoder`，我们可以让 JSON 解析变得非常简单:

```
let JSON = """
{
    "title": "Optionals in Swift explained: 5 things you should know",
    "url": "https://www.avanderlee.com/swift/optionals-in-swift-explained-5-things-you-should-know/",
    "category": "swift",
    "views": 47093
}
"""

let jsonData = JSON.data(using: .utf8)!
let blogPost: BlogPost = try! JSONDecoder().decode(BlogPost.self, from: jsonData)

print(blogPost.title) // Prints: "Optionals in Swift explained: 5 things you should know"
```

尽管这可能给人一种 JSON 解析非常简单的印象，但这都是边缘情况。幸运的是，Swift 也有足够的能力处理这些问题。

## 不需要定义每个属性

很高兴知道您不需要定义 JSON 附带的每个属性。这意味着下面的结构也可以工作:

```
struct BlogPost: Decodable {
    let title: String
}
```

这很好，因为这可能是在你已经发布了一个版本的应用程序之后，你正在添加新的密钥。如果它不这样工作，你可以很容易地打破旧版本。

## 选项和 JSON 解码

这可能是因为您不确定是否返回了一个 JSON 键或者是否设置了一个值。在这种情况下，您可以将一个 Swift 属性定义为可选的，然后`JSONDecoder`会处理剩下的事情。

```
struct BlogPost: Decodable {
    let title: String
    /// Define a key as optional if it can be returned as `nil` or if it does not always exist in the JSON.
    let subtitle: String?
}
```

# 在 Swift 中解码 JSON 数组

在 Swift 中解码一个 JSON 数组几乎和解码一个 JSON 对象一样简单。以下面的 JSON 为例:

```
[{
    "title": "Optionals in Swift explained: 5 things you should know",
    "url": "https://www.avanderlee.com/swift/optionals-in-swift-explained-5-things-you-should-know/"
},
{
    "title": "EXC_BAD_ACCESS crash error: Understanding and solving it",
    "url": "https://www.avanderlee.com/swift/exc-bad-access-crash/"
},
{
    "title": "Thread Sanitizer explained: Data Races in Swift",
    "url": "https://www.avanderlee.com/swift/thread-sanitizer-data-races/"
}]
```

我们可以通过将`Decodable`类型定义为`[BlogPost].self`来解析这个博客文章列表:

```
struct BlogPost: Decodable {
    let title: String
    let url: URL
}

let blogPosts: [BlogPost] = try! JSONDecoder().decode([BlogPost].self, from: jsonData)
print(blogPosts.count) // Prints: 3
```

# 将 JSON 键映射到自定义属性名

JSON 解析并不总是像将相同的键复制到一个结构中那样简单。在映射 JSON 时，希望定义不同的属性名是很常见的。

以之前的 JSON 为例，我们可能想在 JSON 模型中将`url`命名为`htmlLink`。我们可以通过定义一个定制的`CodingKeys`枚举来创建这个映射:

```
struct BlogPost: Decodable {
    enum Category: String, Decodable {
        case swift, combine, debugging, xcode
    }

    enum CodingKeys: String, CodingKey {
        case title, category, views
        // Map the JSON key "url" to the Swift property name "htmlLink"
        case htmlLink = "url"
    }

    let title: String
    let htmlLink: URL
    let category: Category
    let views: Int
}

let blogPost: BlogPost = try! JSONDecoder().decode(BlogPost.self, from: jsonData)
print(blogPost.htmlLink) // Prints: "https://www.avanderlee.com/swift/optionals-in-swift-explained-5-things-you-should-know/"
```

如您所见，我们定义了一个自定义映射，将 JSON 键`url`转换成 Swift 属性名`htmlLink`。

因为我们没有改变标题、类别或视图的名称，所以我们可以保持这种情况不变。我们必须包含这些键，因为`JSONDecoder`将切换到我们为所有定义的属性定义的映射。如果我们不这样做，我们会遇到以下错误:

> 类型“BlogPost”不符合协议“Decodable”

# 骆驼案和蛇案之间的转换

为键定义自定义映射的一个常见原因是，如果您使用的后端使用 snake case 来命名属性。在 Swift 中，我们大多使用 camel case，这意味着我们以小写字母开始，然后大写后续单词的第一个字母:`htmlLink`或`numberOfBlogPosts`。蛇案中的同字看起来是这样的:`html_link`和`number_of_blog_posts`。

幸运的是，我们不必为每个已定义的键定义自定义映射。以博客的 JSON 为例:

```
{
    "title": "A weekly Swift Blog on Xcode and iOS Development - SwiftLee",
    "url": "https://www.avanderlee.com",
    "total_visitors": 378483,
    "number_of_posts": 47093
}
```

我们可以通过将解码器的`keyEncodingStrategy`设置为`.convertToSnakeCase`来轻松解码 JSON:

```
struct Blog: Decodable {
    let title: String
    let url: URL
    let totalVisitors: Int
    let numberOfPosts: Int
}

let decoder = JSONDecoder()
decoder.keyDecodingStrategy = .convertFromSnakeCase

let blog: Blog = try! decoder.decode(Blog.self, from: jsonData)
print(blog.numberOfPosts) // Prints: 47093
```

这太简单了。这也适用于自定义键。因此，如果您想像我们之前做的那样将`url`映射到`htmlLink`，您可以按如下方式轻松完成:

```
struct Blog: Decodable {

    enum CodingKeys: String, CodingKey {
        case title, totalVisitors, numberOfPosts

        case htmlLink = "url"
    }

    let title: String
    let htmlLink: URL
    let totalVisitors: Int
    let numberOfPosts: Int
}
```

# 用定制格式解码 JSON 日期

JSON 中的日期被定义为字符串或时间间隔，并且需要一种转换策略。我们可以在我们的`JSONDecoder`上设置这样的策略，就像我们将骆驼案例转换为蛇案例一样。

以下面的 JSON 博客文章为例:

```
{
    "title": "Optionals in Swift explained: 5 things you should know",
    "date": "2019-10-21T09:15:00Z"
}
```

本例中的日期定义如下:`yyyy-MM-dd'T'HH:mm:ss`。我们需要用这种格式创建一个自定义的`DateFormatter`，并通过将`dateDecodingStrategy`设置为`formatted`将其应用于我们的解码器:

```
struct BlogPost: Decodable {
    let title: String
    let date: Date
}

let decoder = JSONDecoder()
let dateFormatter = DateFormatter()
dateFormatter.dateFormat = "yyyy-MM-dd'T'HH:mm:ssZ"
dateFormatter.locale = Locale(identifier: "en_US")
dateFormatter.timeZone = TimeZone(secondsFromGMT: 0)
decoder.dateDecodingStrategy = .formatted(dateFormatter)

let blogPost: BlogPost = try! decoder.decode(BlogPost.self, from: jsonData)
print(blogPost.date) // Prints: 2019-10-21 09:15:00 +0000
```

还有一些其他策略可供设置:

*   `deferredToDate`:使用苹果自己的数据格式，跟踪自 2001 年 1 月 1 日以来的秒数和毫秒数。这主要是有用的直接使用苹果的平台。
*   这种格式记录自 1970 年 1 月 1 日以来的秒数和毫秒数，使用起来更加普遍
*   `secondsSince1970`:跟踪自 1970 年 1 月 1 日以来的秒数
*   `iso8601`:将日期解码为 ISO-8601 格式的字符串(RFC 3339 格式)

根据您使用的 API 返回日期的方式，您可以在这些策略之间进行选择。

# 结论

Swift 使得解码 JSON 变得非常容易。没有必要为 JSON 解析使用定制库，因为默认 API 提供了我们需要的一切，从定制键映射到格式化日期。

感谢阅读！