# 使用枚举和相关值解析 Swift 中的多态 JSON

> 原文：<https://betterprogramming.pub/parse-items-with-different-key-value-pairs-in-a-json-array-with-the-help-of-enums-and-associated-301ffa81179e>

## 解析多态项目

![](img/aea0891b1a4c8150ae564a4aa126a12f.png)

[觉吞](https://unsplash.com/@kyawthutun?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/pair?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

作为 iOS 开发人员，我们经常在应用程序中处理服务器响应和解析 JSON。用 Swift `Codable` ( `Decodable`和`Encodable`)解析 JSON 肯定变得更容易了，但是在某些情况下，解析 JSON 可能会很棘手。

在本文中，我们将研究这样一个案例，并实现一个解决方案。

# 了解我们的 JSON

```
{
    "items": [
        {
            "type": "square",
            "side_length": 12
        },
        {
            "type": "sphere",
            "diameter": 4
        },
        {
            "type": "cylinder",
            "diameter": 4,
            "height": 10
        },
        {
            "type": "lalala",
            "unknown_key": 0
        }
    ]
}
```

我们来看看上面的 JSON。它由几何对象类型的项目数组组成，如正方形、球体和圆柱体。对象的键值对互不相同，但是它们都有`key`类型。

我们如何解析这样的 JSON——列表中提供的条目可能是不同的类型。我们能提供什么样的模式？

## 实现模型

我们将借助带有关联值的枚举来实现它。让我们首先为每个形状创建一个模型，如下所示(我将从这些模型中排除 type，因为稍后会用到它，但是您仍然可以在这些模型中包含 type)。

```
struct Square: Decodable {
    let sideLength: Int

    enum CodingKeys: String, CodingKey {
        case sideLength = "side_length"
    }
}struct Sphere: Decodable {
    let diameter: Int
}struct Cylinder: Decodable {
    let diameter: Int
    let height: Int
}
```

现在，我们对 JSON 中提供的每个形状都有了一个模型。(`Square`已经实现了`CodingKeys`，因为`sideLength`的键在 JSON 中写成了`side_length`。)

我们需要确定给定对象的类型，然后才能正确解码它，因此让我们创建一个枚举，如下所示:

```
enum ObjectType: String, Decodable {
    case square
    case sphere
    case cylinder
    case unknown

    init(from decoder: Decoder) throws {
        let container = try decoder.singleValueContainer()
        let type = try container.decode(String.self)
        self = ObjectType(rawValue: type) ?? .unknown
    }
}
```

在这里的`ObjectType`枚举中，我们为每个条目准备了一个 case，另外还有一个`unknown` case，即使发送了一个`unknown`类型，它仍然可以解析 JSON。当在客户端更新之前在后端进行了更改时，这可能特别有用。

首先在单值容器的帮助下解码给定的字符串，然后用解码后的字符串的`rawValue`初始化`ObjectType`。如果给定的类型初始化失败，它被初始化为 unknown(如果该类型不是 enum 的情况之一)。

## 使用带有关联值的枚举分析不同的项类型

我们已经到了这首曲子的关键部分。现在我们将实现一个符合`Decodable`的名为`Object`的枚举，包括所有形状的情况和一个额外的未知情况。

```
enum Object: Decodable {
    case square(Square)
    case sphere(Sphere)
    case cylinder(Cylinder)
    case unknown

    enum CodingKeys: String, CodingKey {
        case type
    }

    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        guard let type = try? container.decode(ObjectType.self, forKey: .type) else {
            self = .unknown
            return
        }

        let objectContainer = try decoder.singleValueContainer()

        switch type {
        case .square:
            let square = try objectContainer.decode(Square.self)
            self = .square(square)
        case .sphere:
            let sphere = try objectContainer.decode(Sphere.self)
            self = .sphere(sphere)
        case .cylinder:
            let cylinder = try objectContainer.decode(Cylinder.self)
            self = .cylinder(cylinder)
        case .unknown:
            self = .unknown
        }
    }
}
```

只有一种`CodingKey`型。首先确定类型并解析为`ObjectType`。然后，根据类型使用单值容器解析实际形状。稍后，在初始化 self 时，这个解析的形状被用作关联值。

感谢`Object`枚举，我们将能够解析一个条目列表，其中每个条目可以有不同的键值。只要我们有一个匹配`key`类型的，在我们的例子中。)

让我们实现一个包装器模型`GeometricShapes`，这样我们就可以测试解码将如何与我们的 JSON 一起工作:

```
struct GeometricShapes: Decodable {
    let items: [Object]
}
```

`GeometricShapes`仅由对象数组组成。

现在我们将初始化`data`作为我们的 JSON:

```
let JSON = """
    {
        "items": [
            {
                "type": "square",
                "side_length": 12
            },
            {
                "type": "sphere",
                "diameter": 4
            },
            {
                "type": "cylinder",
                "diameter": 4,
                "height": 10
            },
            {
                "type": "lalala",
                "unknown_key": 0
            }
        ]
    }
""".data(using: .utf8)!
```

让我们解析它:

```
let decoder = JSONDecoder()
let geometricObjects = try decoder.decode(GeometricShapes.self, from: JSON)
let items = geometricObjects.items// Here we just parse and print some values of the models.
// This is how you can parse with a wrapper enum with associated value.
items.forEach({
    switch $0 {
    case .cylinder(let cylinder):
        print("Diameter of cylinder is: \(cylinder.diameter)")
        print("Height of cylinder is: \(cylinder.height)")
    case .sphere(let sphere):
        print("Diameter of sphere is: \(sphere.diameter)")
    case .square(let square):
        print("Side length of square is: \(square.sideLength)")
    case .unknown:
        print("We have an unknown type.")
    }
})
```

我们执行以下操作:

*   首先，我们得到一个`Decoder`并将我们的数据解析为一个`GeometricShape`模型
*   用模型的项目创建一个名为 items 的常数
*   通过切换来解析数组中的每一项
*   根据项目类型打印每个项目的属性

我只运行了一个`for each`循环来展示如何用枚举和相关值解析每个元素。您可以使用相同的方法进行解析，然后根据类型初始化一些 UI 组件，或者创建一些数据源。解析完 JSON 后做什么都行。

## 结论

在本文中，我们已经看到了在解析 JSON 时，带有关联值的枚举是如何派上用场的。重要的是要记住，即使每一项都不同，它们至少应该有一个相同的键，这样我们才能知道应该为 JSON 数组中的特定项初始化哪个模型。

我希望你喜欢这篇文章，它可能是有用的。你可以在这里找到操场要诀[。](https://gist.github.com/emrepun/0f2d76ffdedce26fc2dec95dfe037347)