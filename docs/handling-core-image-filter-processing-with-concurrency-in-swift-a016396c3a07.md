# 在 Swift 中并行处理核心图像过滤处理

> 原文：<https://betterprogramming.pub/handling-core-image-filter-processing-with-concurrency-in-swift-a016396c3a07>

## 利用并发能力，将 iOS 中的图像处理速度提高 6 倍

![](img/f1af9cf0708f08cd609e6f9402d9cb71.png)

由[朱利安·霍格桑](https://unsplash.com/@julianhochgesang?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在过去的一个月里，我发表了几篇关于并发编码的文章。这是《WWDC 2021》的一个核心主题，我相信我们也会在《WWDC 2022》中回归。

在这篇文章中，我想探究并发编码的一个完美应用领域——苹果在去年夏天悄悄推出的一个框架，Core Image。

更新的是图像过滤器，现在只需两行就可以调用。你可以在这个[链接](https://developer.apple.com/library/archive/documentation/GraphicsImaging/Reference/CoreImageFilterReference/index.html#//apple_ref/doc/uid/TP30000136-SW29)下找到一个很好的参考。一个链接，带您到一些优秀的文档，展示了如何配置每个过滤器的例子看起来像什么。虽然我反对，但你需要建立一个缺失的环节来理解每个过滤器的真正作用。这段代码是缺失的一环。

它对构成图像的数据产生一系列的`UnsafeMutableBufferPointers`。然后，我们可以进行数值分析，以更好地理解滤波器对所述图像做了什么——当然；这在某些情况下很明显——但在其他情况下就不那么明显了。你可以在最后一行看到一个序列，像素。

这也很有趣，因为我们可以用它作为真实世界的背景来检查同一代码的不同版本的性能，包括并发版本。比较和对比它们的易读性、正确性和速度。说话速度，我会用`CFAbsoluteTimeGetCurrent`来衡量看到的性能。

现在，我从定义一个包含所有我需要分析的数据的结构开始，这个结构看起来像这样。四个字典将总结在图像中发现的数据。

```
struct Ana {
  var rgbRed:[UInt32?:UInt32]
  var rgbBlue:[UInt32?:UInt32]
  var rgbGreen:[UInt32?:UInt32]
  var rgbColor:[UInt32?:UInt32]
}
```

我要看的图像是自画像。在最初的分析中，我得到的数据是这样的。我找到了 **120'434 种颜色**。一个分析，用这个代码看起来 **11.2798** 秒**秒**。

```
let begin = CFAbsoluteTimeGetCurrent()
for p in pixels.indices {
  if ana.rgbColor[pixels[p]] == nil {
    ana.rgbColor[pixels[p]] = 1
  } else {
    ana.rgbColor[pixels[p]] = ana.rgbColor[pixels[p]]! + 1
  }
}
print("timeTaken ",CFAbsoluteTimeGetCurrent() - begin)
print("ana ",ana.rgbColor.count)
```

我认为一段代码是清晰和正确的，但它运行得太慢了，会通过代码审查 101，因为它本质上是在主线程上做后台工作。

为了解决这个问题，我修改了代码，使用了一个较低层次的 API，即 map 类似这样的代码。

```
let begin = CFAbsoluteTimeGetCurrent()
let _ = pixels.indices.map { value in if ana.rgbColor[pixels[value]] == nil {
  ana.rgbColor[pixels[value]] = 1
  } else {
  ana.rgbColor[pixels[value]] = ana.rgbColor[pixels[value]]! + 1
}}
print("timeTaken ",CFAbsoluteTimeGetCurrent() - begin)
print("ana ",ana.rgbColor.count)
```

这是一个正确的代码片段，但可悲的是，我觉得它不太清晰，也没有更快，花费了 **11.0946 秒**并且仍然在主线程上运行，也没有通过 101 代码审查。

![](img/1fec1a2f1dead19431604f32103c28b9.png)

应用于自画像的卡通滤镜。

接下来，我想我会尝试使用一些并发代码来实现它，我是通过用这些代码将我的数据结构移动到一个 actor 中来实现的。

我随后用这段代码调用的代码。

```
let begin = CFAbsoluteTimeGetCurrent()
Task.detached(priority: .userInitiated) {
  let pna = Pna()
  for value in pixels.indices {
    await pna.proPna(p: pixels[value])
  }
  let foo = await pna.outPna()
  print("timeTaken ",CFAbsoluteTimeGetCurrent() - begin)
  await print("png ",pna.rgbColor.count
}
```

一个正确易读的片段——即使它有点长。它还将通过 101 代码审查，因为它使用了新的异步任务指令，该指令不在主线程上运行。但是令人震惊的是，完成这项工作需要更长的时间，有些 **12.8886 秒**。资源的额外锁定正在造成损失，而我们似乎在异步代码上几乎什么也没有得到。

现在，我确实想像以前一样简单地将它添加到地图函数中。但是从 Swift 5.5 开始，map 不支持在其中调用异步代码。我通过使用我在这篇关于这个主题的[优秀文章](https://www.swiftbysundell.com/articles/async-and-concurrent-forEach-and-map/)中找到的扩展来解决这个问题。

```
extension Sequence {
  func asyncMap<T>(
    _ transform: (Element) async throws -> T
  ) async rethrows -> [T] {   var values = [T]()
    for element in self {
      try await values.append(transform(element))
    }
  return values
  }
}
```

更改我的代码来调用它——但令人尴尬的是，这是迄今为止最慢的版本；以惊人的 **14.5760 秒**完成。显然我需要休息。

```
let begin = CFAbsoluteTimeGetCurrent()
Task.detached(priority: .userInitiated) {
   let pna = Pna()
   let _ = await pixels.indices.asyncMap( { value in await   pna.proPna(p: pixels[value]) } )
  let foo = await pna.outPna()
  print("timeTaken ",CFAbsoluteTimeGetCurrent() - begin)
  await print("ana ",pna.rgbColor.count)
}
```

我睡了一觉，第二天早上回来重新考虑我的代码。我向前迈了一步，然后又后退了两步。

尤里卡，我想到我可以用器械来完成这项工作；字典是错误的数据类型。我用我的演员重新定义了结构，并重新运行了测试；重新定义的代码如下所示。

我用这个调用的代码。

```
let begin = CFAbsoluteTimeGetCurrent()
Task.detached(priority: .userInitiated) {
  let sna = Sna()
  for value in 0..<pixels.count {
    await sna.countSna(p: pixels[value])
  }
let foo = await sna.confirmMembers()
print("timeTaken ",CFAbsoluteTimeGetCurrent() - begin)
```

它是正确的；易读将通过代码审查 101。而且——快；跑合时间 **4.5465 秒**。是的，这比字典版快三倍——但是我等着；这不是并行运行的。我重读了我的笔记，重新考虑了我的代码。我需要使用一个任务组。

我重新编码了一遍，在我给你代码之前，我先给你结果，我得到了一个可观的 2.3590 秒的处理时间。完美的结果。我把图像分成两部分，并在不同的部分进行分析，完成后将它们合并。显然，它在我忠实的 iPhone 8 上使用了 2 个高性能内核。

所以，当然，你知道我接下来做了什么——我查了一下我的 iPhone 8 有几个核心，六个。所以我修改了代码，再试了一次。

我在三个内核的情况下获得了 1.9852 秒(T1)，这是对最初的 11.09 秒(T2)的惊人改进，异步版本现在的运行速度几乎快了六倍。尽管除此之外，我怀疑布景的合并开始减慢速度。我也开始失去准确性，因为分区不像它应该的那样干净。

所有这些将我带到本文的结尾，尽管我还没有使用我的新分析代码看完图像滤波器，所以请关注这个空间，我会回来的。

最后一个奖励——这里是我在这篇文章中使用的所有代码的[位存储桶](https://bitbucket.org/wizard1066/concurrentcode/commits/0cbf867498efc9fd7964bfb7ba2accb2c395ce86),包括我承认对我提到的、但没有讨论的那些过滤器的研究。我承认这并不是非常简洁，更像是代码的思维导图。