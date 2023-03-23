# 在 MacOS 的 SwiftUI 中创建 Google 字体浏览器

> 原文：<https://betterprogramming.pub/creating-a-google-fonts-browser-in-swiftui-49c94787d14a>

## 了解字体如何在较低层次上工作，并了解它如何与 SwiftUI 紧密结合

![](img/0d8c91960c059b72553366617059e681.png)

照片由[阿玛多·洛雷罗](https://unsplash.com/@amadorloureiro?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/fonts?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

[谷歌字体](https://fonts.google.com)是设计用户界面时使用免费字体的首选网站。本教程将展示如何编写一个简单的工具来预览这些字体，而不必在系统中注册每种字体。

该应用程序包含一个拆分视图，在左侧面板中有一个字体列表。右侧面板将显示字体样式选项的预览。

![](img/cea52e5ac81c906dbb93c39c768782d2.png)

谷歌字体预览应用

## 项目设置

1.  创建一个名为`GoogleFontPrevew`的新 Mac SwiftUI 项目
2.  在应用沙盒中启用传出连接(客户端)
3.  从[谷歌开发者控制台](https://console.cloud.google.com/)获取一个谷歌 API 密钥。

## 谷歌字体模型

谷歌字体 API 列出了在[谷歌字体](https://fonts.google.com)可用的所有字体。使用[谷歌字体 API](https://developers.google.com/fonts/docs/developer_api) 检索字体。响应包含所有样式和一个 URL 来访问这些样式的字体文件。缺少元数据，如创建者和描述。但是，我们有足够的内容来创建一个简单的预览，这是我们的目标。

```
{
  "kind": "webfonts#webfontList",
  "items": [
    {
      "family": "ABeeZee",
      "variants": [
        "regular",
        "italic"
      ],
      "subsets": [
        "latin",
        "latin-ext"
      ],
      "version": "v22",
      "lastModified": "2022-09-22",
      "files": {
        "regular": "http://fonts.gstatic.com/s/abeezee/v22/esDR31xSG-6AGleN6tKukbcHCpE.ttf",
        "italic": "http://fonts.gstatic.com/s/abeezee/v22/esDT31xSG-6AGleN2tCklZUCGpG-GQ.ttf"
      },
      "category": "sans-serif",
      "kind": "webfonts#webfont"
    }
  ]
}
```

**谷歌字体模型**

创建一个`GoogleFont.swift`文件。这个文件将包含我们将用作视图模型的数据结构。

创建 API 的 JSON 响应顶层的 1:1 映射。

```
struct GoogleResponse: Decodable {
  let kind: String
  let items: [GoogleFont]
}
```

创建 GoogleFont 结构来表示一个字体条目。这将是`ContentView`用来显示字体列表的内容。它主要是响应 JSON 中字体项的 1:1 映射。这些文件将从 JSON 中的字典映射到一个已排序的数组。

要可用，字体需要是`Decodable`、`Hashable`和`Identifiable`。由于族名对于每个条目都是唯一的，因此它可以用作对象的唯一标识符。

```
struct GoogleFont: Hashable, Identifiable, Decodable {
  var id : String { family }

  let family: String
  let files: [GoogleFontStyle]
  let version: String
  let category: String
}
```

`FontFile`将在详细信息显示中用于预览字体系列的样式。字体的样式和 url 将被保存以便于参考。为了能够在列表中使用该样式，它需要是`Hashable`和`Identifiable`。将生成一个 UUID 来唯一标识该字体文件。

```
struct FontFile: Hashable, Identifiable {
  let style: Style
  let id = UUID()
  let url: URL
}
```

创建一个`Style.swift`文件。这是定义`Style`枚举的地方。谷歌将权重定义为 100-900。如果重量有斜体变体，则在值后追加`italic`。但是对于 normal 和 normal-italic，Google 只是分别使用了`regular`和`italic`。

enum 有一个 helper 函数来从样式中获取一个友好的名称。友好名称将用作包装样式预览的分组框的标题。

```
enum Style: String, Hashable {
  case thin = "100"
  case thinItalic = "100italic"
  case extraLight = "200"
  case extraLightItalic = "200italic"
  case light = "300"
  case lightItalic = "300italic"
  case normal = "regular"
  case normalItalic = "italic"
  case medium = "500"
  case mediumItalic = "500italic"
  case semiBold = "600"
  case semiBoldItalic = "600italic"
  case bold = "700"
  case boldItalic = "700italic"
  case extraBold = "800"
  case extraBoldItalic = "800italic"
  case black = "900"
  case blackItalic = "900italic"

  var friendlyName: String {
    switch self {
    case .thin: return "Thin"
    case .thinItalic: return "Thin Italic"
    case .extraLight: return "Extra Light"
    case .extraLightItalic: return "Extra Light Italic"
    case .light: return "Light"
    case .lightItalic: return "Light Italic"
    case .normal: return "Normal"
    case .normalItalic: return "Italic"
    case .medium: return "Medium"
    case .mediumItalic: return "Medium Italic"
    case .semiBold: return "Semi Bold"
    case .semiBoldItalic: return "Semi Bold Italic"
    case .bold: return "Bold"
    case .boldItalic: return "Bold Italic"
    case .extraBold: return "Extra Bold"
    case .extraBoldItalic: return "Extra Bold Italic"
    case .black: return "Black"
    case .blackItalic: return "Black Italic"
    }
  }
}
```

现在，有了这些，定制解码就可以在`GoogleFont`中实现了。添加`Decodable's`所需的初始化器和编码键。Xcode 14 的好处是它会自动为你填充一些内容。

```
struct GoogleFont: Hashable, Decodable, Identifiable {
  var id: String { family }

  let family: String
  let files: [FontFile]
  let version: String
  let category: String

  enum CodingKeys: CodingKey {
    case family
    case files
    case version
    case category
  }

  init(from decoder: Decoder) throws {
    let container = try decoder.container(keyedBy: CodingKeys.self)
    self.family = try container.decode(String.self, forKey: .family)
    let files = try container.decode([String: String].self, forKey: .files)
    self.version = try container.decode(String.self, forKey: .version)
    self.category = try container.decode(String.self, forKey: .category)

    var keys = Array(files.keys)

    // lets do a bit of work to get our files that we want.
    keys.sort { item1, item2 in
      func itemFixer(_ value: String) -> String {
        if value == "regular" {
          return "400"
        } else if value == "italic" {
          return "400Italic"
        }

        return value
      }

      let fixedItem1 = itemFixer(item1)
      let fixedItem2 = itemFixer(item2)

      return fixedItem1 < fixedItem2
    }

    self.files = keys.compactMap { key in
      guard let style = Style(rawValue: key),
            let location = files[key]?.replacingOccurrences(of: "http", with: "https")
      else {
        return nil
      }

      return FontFile(style: style, url: URL(string: location)!)
    }
  }
}
```

1.  直接从字体容器中解码`family`、`version`、`category`。
2.  将来自 JSON 的文件映射解码成一个临时变量。
3.  获取密钥，并按照从最亮(100)到最暗(900)的顺序对它们进行排序。块中有一个小的局部函数，它取`regular`并将其映射到`400`，取`italic`并将其映射到`400italic`。这样做允许正常和斜体在中(500)之前和亮(300)之后排序。
4.  键排序后，使用`compactMap`映射创建排序后的`FontFile`数组。使用`compactMap`允许在创建文件数组时忽略`nil`条目。如果使用了`map`，一个`nil`条目将被插入到数组中。
5.  用 https 替换 http，这样应用程序就不会抱怨使用不安全的 web 调用。

## 谷歌字体服务

创建一个`GoogleFontService.swift`文件。该文件将有一个类，该类对 [Google Fonts API](https://developers.google.com/fonts/docs/developer_api) 进行网络调用，以检索应用程序中显示的字体列表。

```
class GoogleFontService {
  let apiKey = "** YOUR KEY HERE **"

  func syncFonts() async throws -> [GoogleFont] {
    var components = URLComponents()

    components.scheme = "https"
    components.host = "www.googleapis.com"
    components.path = "/webfonts/v1/webfonts"
    components.queryItems = [
      URLQueryItem(name: "key", value: apiKey),
      URLQueryItem(name: "sort", value: "alpha")
    ]

    let url = components.url!
    let request = URLRequest(url: url)

    let (data, _) = try await URLSession.shared.data(for: request)
    let googleResponse = try JSONDecoder()
      .decode(GoogleResponse.self, from: data)

    return googleResponse.items
  }
}
```

1.  用从 Google API 控制台获取的值替换`apiKey`。
2.  使用 URLComponents 构建 API 的 URL。这保证了 URL 的格式正确。
3.  请 API 按字母顺序对列表进行排序。
4.  调用[谷歌字体 API](https://developers.google.com/fonts/docs/developer_api)
5.  返回解码后的 GoogleFont 项。

如果没有返回值，请确保:

1.  Google API 密钥是正确的
2.  为应用程序启用传出连接。

## 创建可用的网络字体

为了创建一个可用的网络字体，核心图形和核心文本将混合使用。Core Graphics 是一个管理图形 2D 对象(包括文本)的底层框架。另一方面，核心文本是 SwiftUI 用来呈现文本和处理字体的低级文本呈现和布局引擎。

SwiftUI 的`Font`有一个构造函数，它接受一个`CTFont`(核心文本字体)并返回一个 SwiftUI `Font`。注册功能将下载字体文件并进入 SwiftUI `Font`。创建一个`Font+Register.swift`文件来存放扩展的代码。

```
extension Font {
  static func register(url: URL, size: CGFloat = 18) async throws -> Font? {
    do {
      let request = URLRequest(url: url)
      let (data, _) = try await URLSession.shared.data(for: request)

      guard let provider = CGDataProvider(data: data as CFData),
            let cgFont = CGFont(provider)
      else {
        print("Unsucessfully registered font")
        return nil
      }

      let ctFont = CTFontCreateWithGraphicsFont(cgFont, size, nil, nil)

      return Font(ctFont)
    } catch {
      print(error)
    }

    return nil
  }
}
```

1.  从 URL 获取字体数据
2.  从下载的数据创建核心图形数据提供程序。该代码利用了 Swift 的数据类可以桥接至 CoreFoundation 的 CFData。
3.  创建核心图形字体。
4.  创建核心文本字体。
5.  使用核心文本字体创建 SwiftUI 字体。
6.  如果有错误，打印错误并返回`nil`。

## 创建主视图

`ContentView.swift`文件包含应用程序的主视图。主视图将包含一个拆分视图，左边是列表，右边是详细信息。`ContentView`拥有要显示的字体列表以及当前的选择状态。

将调用`task`修改器加载字体并设置初始选择的字体。

```
struct ContentView: View {
  @State var fonts = [GoogleFont]()
  @State var font: GoogleFont?

  var body: some View {
    NavigationSplitView {
     FontList(selectedFont: $font, fonts: fonts)
    } detail: {
      if let font = font {
        DetailsView(font: font)
      } else {
        EmptyView()
      }
    }
    .task {
      do {
        fonts = try await GoogleFontService().syncFonts()

        await MainActor.run {
          font = fonts[0]
        }
      } catch {
        print (error)
      }
    }
  }
}
```

1.  创建一个状态变量来保存`GoogleFont`项的列表。
2.  创建一个状态变量来保存当前选择的`GoogleFont`
3.  创建包含两列的导航拆分视图。
4.  将`FontList`添加到第一个块(左侧)。注意，选择状态被绑定到`FontList's`选择。
5.  如果列表不为空，则将详细信息设置为一个`DetailsView`。否则，显示一个`EmptyView`
6.  添加一个`task`修饰符来检索字体并将当前选择设置为列表中的第一种字体。

## 字体列表

字体列表显示所有可用的字体，并更新当前选定的字体。创建一个`FontList.swift`文件。

```
struct FontList: View {
  @Binding var selectedFont: GoogleFont?
  var fonts: [GoogleFont]

  var body: some View {
    List(fonts, selection: $selectedFont) { font in
      NavigationLink(value: font) {
        VStack(alignment: .leading) {
          Text(font.family)
            .font(.system(size: 14, weight: .bold))
          Text("\(font.files.count) styles")
            .font(.system(size: 14))
        }
        .padding(8)
      }
    }
  }
}
```

1.  创建绑定以更新当前选定的字体。
2.  创建一个变量来保存要显示的字体。
3.  创建列表视图以显示字体并更新选择。
4.  单元格将是一个简单的垂直堆栈，显示字体系列的名称和样式的数量。
5.  添加少量填充以增加视觉吸引力。

## 详细视图

`DetailsView`为字体系列中的每种样式提供预览。样式将显示在一个缓慢的垂直堆栈中。创造一个`DetailView.swift`

```
struct DetailsView: View {
  var font: GoogleFont

  var body: some View {
    VStack {
      Text(font.family)
        .font(.system(size: 25, weight: .bold))
      Spacer()
      ScrollView {
        LazyVStack(alignment: .leading) {
          ForEach(font.files, id: \.id) { value in
            StyleCell(style: value)
          }
        }
      }
      .id(font.family)
    }
    .padding()
  }
}
```

1.  显示带有选定系列名称的标题。
2.  使用滚动的惰性垂直堆栈显示字体系列中的样式列表。
3.  给滚动视图一个唯一的 ID，以便它在创建时滚动到顶部。SwiftUI 似乎想重用滚动视图，这将导致滚动位置不是每个新选择的顶部。

## 风格细胞

`StyleCell`负责预览所选系列的单一字体样式。单元格将以给定的特定样式显示 A-Z 和 0–9。字体加载时，显示“尝试加载”。由于字体是临时的，它们只在显示时存在。

```
struct StyleCell: View {
  let style: FontFile
  @State var font: Font?

  var body: some View {
    GroupBox(label: Text(style.style.friendlyName)) {
      if let font = font {
        Text("ABCDEFGHJKLMNOPQRSTUVWXYZ\nabcdefghijklmnopqrstuvwxyz\n1234567890")
          .font(font)
          .frame(maxWidth: .infinity, alignment: .leading)
      }
      else {
        Text("trying to load")
          .font(.system(size: 20))
          .frame(maxWidth: .infinity, alignment: .leading)
      }
    }
    .task {
      font = try? await Font.register(url: style.url, size: 32)
    }
    .padding()
  }
}
```

1.  将预览包装在标题为该样式的`friendlyName`的分组框中。
2.  如果有已注册的字体，显示预览。否则，显示“正在尝试加载”。确保这些显示在水平允许的最大空间内。
3.  在`task`修改器中，当创建视图时注册样式的字体。

运行该项目现在允许用户选择一种字体，并显示其预览。

这是一个有趣的教程，可以学习更多关于字体如何在较低层次上工作的知识，并了解它如何与 SwiftUI 相结合。完整的项目可以在 [GitHub](https://github.com/scottandrew/GoogleFontPreview) 上找到。

感谢阅读。