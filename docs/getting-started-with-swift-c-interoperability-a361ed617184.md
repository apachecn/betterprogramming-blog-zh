# Swift-c++互操作性入门

> 原文：<https://betterprogramming.pub/getting-started-with-swift-c-interoperability-a361ed617184>

## 试验性的 Swift-C++互操作

![](img/0bebde09fa615d1b74157f6c54c34304.png)

巴勃罗·加西亚·萨尔达尼亚在 [Unsplash](https://unsplash.com/s/photos/two-way?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Swift 是一种非常舒适的语言。它有一些怪癖和一个学习曲线，但最终你可以用它非常快地发布生产就绪的代码。
然而，有时您会遇到性能关键的部分，而 Swift 无法满足您的需求。在这种情况下，一个流行的选择是使用 C++。

问题就来了“我如何从 Swift 调用这个 C++ `func`”?

通常，你必须写一个 Objective-C 包装器作为你的 C++代码的公共接口。Swift toolchain 可以将 Objective-C 声明导入 Swift。主要的限制是不能在 Objective-C 中使用 C++类，只能使用简单的 POD 结构。

我们将用 C++和 Swift 编写一个厄拉多塞算法的[筛子。然后学习如何启用 C++互操作，从 Swift 调用 C++代码，比较实现性能。](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes)

请记住，该功能是实验性的，可能会有变化。本出版物在 Xcode 版本 14.2 上编译

# 算法

厄拉多塞的筛子能找出所有小于或等于 n 的质数。质数是只能被自身和 1 整除的整数。该算法创建一个布尔数组来指示每个数字是否是质数。并逐步迭代它们，将所有倍数标记为非素数。

下面是 Swift 的实现。

```
// primes.swift

func primes(n: Int) -> [Int] {
    var isPrime = [Bool](repeating: true, count: n + 1)

    for value in stride(from: 2, to: n + 1, by: 1) where isPrime[value] {
        if value * value > n { break }

        for multiple in stride(from: value * 2, to: n + 1, by: value) {
            isPrime[multiple] = false
        }
    }

    var result = [Int]()

    for value in stride(from: 2, to: n + 1, by: 1) where isPrime[value] {
        result.append(value)
    }

    return result
}
```

对于 C++我们需要一个头文件和一个源文件。注意，我们`typedef`有一个更清晰的名字来指代`std::vector<long>`。

```
// primes.hpp

#include <vector>
typedef std::vector<long> VectorLong;
VectorLong primes(const long &n);
```

```
// primes.cpp
#include <algorithm>

#include "primes.hpp"
VectorLong primes(const long &n) {
    std::vector<char> isPrime(n + 1); // faster than std::vector<bool>
    std::fill(isPrime.begin(), isPrime.end(), true);

    for (long value = 2; value * value <= n; ++value) {
        if (!isPrime[value]) { continue; }
        for (long multiple = value * 2; multiple <= n; multiple += value) {
            isPrime[multiple] = false;
        }
    }

    VectorLong result;

    for (long value = 2; value <= n; ++value) {
        if (!isPrime[value]) { continue; }
        result.push_back(value);
    }

    return result;
}
```

# 项目结构

我们将使用两个单独的目标来实现 Swift 包，以保存我们的 Swift 和 C++代码。要从 Swift 导入 C++代码，我们需要一个 modulemap。

```
// module.modulemap

module CXX {
    header "CXX.hpp"
    requires cplusplus
}

// CXX.hpp

#include "primes.hpp"
```

并且不要忘记将`-enable-experimental-cxx-interop`传给`Package.swift`中的迅捷目标。

```
// swift-tools-version: 5.7

import PackageDescription

let package = Package(
    name: "SwiftCXXInteropExample",
    platforms: [
        .macOS(.v12),
    ],
    products: [
        .library(name: "CXX", targets: ["CXX"]),
        .executable(name: "CLI", targets: ["CLI"])
    ],
    dependencies: [],
    targets: [
        .target(name: "CXX"),
        .executableTarget(
            name: "CLI",
            dependencies: ["CXX"],
            swiftSettings: [.unsafeFlags(["-enable-experimental-cxx-interop"])]
        )
    ]
)
```

更多关于如何启用 C++互操作的信息请参见 Apple 的文档。

# 炼脂

使用我们 Swift 的`VectorLong`与`RandomAccessCollection`保持一致要容易得多，令人高兴的是，这真的很容易做到。

```
import CXX

extension VectorLong: RandomAccessCollection {
    public var startIndex: Int { 0 }
    public var endIndex: Int { size() }
}
```

现在我们可以从 Swift 调用我们的 C++函数，并将结果打印到控制台。

```
let cxxVector = primes(100)
let swiftArray = [Int](cxxVector)
print(swiftArray)
```

让我们看看我们的 C++实现是否真的执行得更快。

```
let signposter = OSSignposter()

let count = 100
let n = 10_000_000

for _ in 0..<count {
    let state = signposter.beginInterval("C++")
    let _ = primes(n)
    signposter.endInterval("C++", state)
}

for _ in 0..<count {
    let state = signposter.beginInterval("Swift")
    let _ = primes(n: n)
    signposter.endInterval("Swift", state)
}
```

稍快，平均持续时间为 26 毫秒，而 Swift 为 28 毫秒。

# 最后的想法

我们能够在 Swift 中直接使用`std::vector`。我个人还没有找到雨燕`Map`和`std::map`、`Set`和`std::set`之间往返的便捷方式。尽管如此，C++ interop 正在迅速发展，前途似乎一片光明。

`[CppInteroperability](https://github.com/apple/swift/tree/main/docs/CppInteroperability)`Swift repo 中的文件夹包含有关互操作功能、限制和计划的更多信息。

看完整代码[这里](https://github.com/ksemianov/SwiftCXXInteropExample)。

*原载于*[*https://ksemianov.github.io/articles/cpp-interop/*](https://ksemianov.github.io/articles/cpp-interop/)