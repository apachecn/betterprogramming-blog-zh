# 在 Swift 中使用 AsyncSequence 构建 CSV 解析器

> 原文：<https://betterprogramming.pub/simple-csv-parser-using-asyncsequence-7356fd7d800>

## 在您的 iOS 应用程序中轻松解析数据

![](img/b11007479aa6f7b7c6a5e7ddc70f54f0.png)

照片由[米卡·鲍梅斯特](https://unsplash.com/@mbaumi?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/photos/Wpnoqo2plFA?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我想解析一些来自 Zillow 的 CSV 文件中的住房数据。我认为这将是一个学习如何使用编写自定义序列的好方法。这个简短的教程将介绍如何编写一个简单的 CSV 解析器，它使用`AsyncSequence`将数据解析为一个值数组。

本文中的源代码可以在 [Github](https://github.com/scottandrew/CSVParser.git) 中。

# **什么是 CSV**

CSV(逗号分隔值)文件是以列/行格式保存数据的文本文件。每行是一组数据，每列有一个值。第一行有一个可选的标题。这些列通常由逗号(，)分隔。

一篇完整的维基百科文章可以在这里找到。

# **异步序列**

AsyncSequence 是通过 Swift async/await 引入的。它允许您异步枚举一组对象——就像在列表中一样。但是，当您循环一个项时，数据的这个数据序列不一定在内存中——从而允许您在每次迭代中等待取值。完整的文档见[苹果文档](https://developer.apple.com/documentation/swift/asyncsequence)。

# 我们想要解析的数据

我们将从 Zillow 解析一些住房数据。该数据是美国各地区的月平均房价。对于本教程，我们将使用家庭价值价格，下载链接可以在[这里](https://www.zillow.com/research/data/)找到。我们将使用的地铁&美国数据可以在这个[链接](https://files.zillowstatic.com/research/public_csvs/zhvi/Metro_zhvi_uc_sfrcondo_tier_0.33_0.67_sm_sa_month.csv?t=1659150579)中找到。

让我们看看前三行的前 6 列。`column 5`之后的列都是日期。

数据如下所示:

```
RegionID,SizeRank,RegionName,RegionType,StateName,2000–01–31,
102001,0,United States,Country,,128454.0
394913,1,”New York, NY”,Msa,NY,225516.0
```

从这些数据中我们可以看出:

*   各列用逗号分隔
*   逗号和数据之间没有空格
*   假设一切都是字符串。
*   缺失数据可以有空白栏。
*   我们需要引号之间的字符串。

# **跟踪线路**

对数据的每次迭代都将返回一个行结构，该行结构包含行号和数据在行中的顺序。

数据以可选字符串数组的形式返回。任何丢失的数据或两个逗号之间没有文本都被认为是数组中的`nil`。

```
struct Line {
  let lineNumber: Int
  let data: [String?]
}
```

CSV 在文档中可以有一个标题——通常在第一行，所以跟踪行号很重要。

# 清除解析器

`URL`类有一些新的属性——`url.lines`来异步迭代文本行。CSV 解析器将使用这个`**AsyncSequence**`来解析 URL 中的行。

```
typealias LineIterator = AsyncLineSequence<URL.AsyncBytes>.AsyncIterator

struct CSVParser: AsyncSequence, AsyncIteratorProtocol {   
  private let url: URL
  private var lineIterator: LineIterator
  private let seperator: Character
  private let quoteCharacter: Character = "\""
  private var lineNumber = 0

  init(url: URL, seperator: Character = ",") {
    self.url = url
    self.seperator = seperator
    self.lineIterator = url.lines.makeAsyncIterator()
  }
}
```

我们的解析器的基础相当简单。

*   我们有一个接受 URL 和数据分隔符的构造函数。它默认为逗号(，)。
*   `iineIterator`是异步迭代器的实例，我们将使用它从 URL 中获取文本行。我使用了一个`typealias`来简化类型的语法。
*   `lineNumber`我们当前以 0 为基数的行号。
*   属性来抓住我们的`url`和`delimeter.`

# 实现异步序列和异步解释器

我们需要实现我们的序列和迭代器。

## 异步序列

*   `Element`要返回的元素的类型，在我们的例子中是`Line`。

```
typealias Element = Line
```

*   `makeAysncIterator`函数创建一个新的迭代器用于生成行。在我们的例子中，那只是`self`。

```
func makeAsyncIterator() -> CSVParser {
  return self
}
```

## AsyncIterator

*   `next`检索序列中的下一个`Line`，并在返回行后增加行号。如果没有更多的行，则返回`nil`，停止枚举。

```
mutating func next() async throws -> Line? {
  if let string = try await lineIterator.next() {
    defer { lineNumber += 1 }
    return Line(
      lineNumber: lineNumber,
      data: split(line: string)
    )
  }

  return nil
}
```

# 分裂函数

在`split`函数中，我们逐个字符地遍历该行，并将该行拆分成一个可选字符串数组。

```
private func split(line: String) -> [String?] {
  var data = [String?]()
  var inQuote = false
  var currentString = ""

  for character in line {
    switch character {
    case quoteCharacter:
      inQuote = !inQuote
      continue

    case seperator:
      if !inQuote {
        data.append(currentString.isEmpty ? nil : currentString)
        currentString = ""
        continue
      }

    default:
      break
    }

    currentString.append(character)
  }

  data.append(currentString.isEmpty ? nil : currentString)

  return data
}
```

将每个角色排成一行。遵循以下规则:

*   如果我们打了一个引号，那么我们将`inQuote` flat 设置为 true 或 false。只要这是真的所有字符，直到我们设置`inQuote`为假将被添加到`currentString`。
*   当遇到一个分隔符，并且我们不在引号中时，将`currentString`放在`data`数组的末尾。如果`currentString`是空的，把`nil`放在`data`数组的末尾。我们加到数组后复位`currentString`。最后移到下一个字符。
*   默认情况下，行为是将当前字符添加到`currentString`
*   返回数组之前的最后一件事是将一直解析到行尾的数据添加到`data`数组中。

`1, 2, 3,,5, 6`会返回`["1", "2", "3", nil, "5", "6"]`。

# 把所有的放在一起

最终的解析器看起来像这样:

```
typealias LineIterator = AsyncLineSequence<URL.AsyncBytes>.AsyncIterator

struct CSVParser: AsyncSequence, AsyncIteratorProtocol {
  // this is our type we are going to return on next()
  typealias Element = Line

  private let url: URL
  private var lineIterator: LineIterator
  private let seperator: Character
  private let quoteCharacter: Character = "\""
  private var lineNumber = 0

  init(url: URL, seperator: Character = ",") {
    self.url = url
    self.seperator = seperator
    self.lineIterator = url.lines.makeAsyncIterator()
  }

    mutating func next() async throws -> Line? {
      if let string = try await lineIterator.next() {
        defer { lineNumber += 1 }
        return Line(
          lineNumber: lineNumber,
          data: split(line: string)
        )
      }

      return nil
    }

  func makeAsyncIterator() -> CSVParser {
    return self
  }

  private func split(line: String) -> [String?] {
    var data = [String?]()
    var inQuote = false
    var currentString = ""

    for character in line {
      switch character {
      case quoteCharacter:
        inQuote = !inQuote
        continue

      case seperator:
        if !inQuote {
          data.append(currentString.isEmpty ? nil : currentString)
          currentString = ""
          continue
        }

      default:
        break
      }

      currentString.append(character)
    }

    data.append(currentString.isEmpty ? nil : currentString)

    return data
  }
}
```

# 运行代码

移除`main.swift`类并创建一个`App`对象来运行代码。这将允许我们的应用程序有一个异步主入口点。`App.swift`文件应该如下所示:

```
import Foundation

@main
enum App {
  static func main() async throws {
    do {
      let parser = CSVParser(url: URL(string: "https://files.zillowstatic.com/research/public_csvs/zhvi/Metro_zhvi_uc_sfrcondo_tier_0.33_0.67_sm_sa_month.csv?t=1659150579")!)

      for try await line in parser {
        print("line: \(line.lineNumber)\ndata: \(line.data)")
      }
    } catch {
      print("Error: \(error)")
    }
  }
}
```

运行该程序将打印每一行对象。

感谢阅读。