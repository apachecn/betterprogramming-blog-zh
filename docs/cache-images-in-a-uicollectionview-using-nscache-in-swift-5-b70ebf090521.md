# 在 Swift 5 中使用 NSCache 缓存 UICollectionView 中的图像

> 原文：<https://betterprogramming.pub/cache-images-in-a-uicollectionview-using-nscache-in-swift-5-b70ebf090521>

## 利用内置的缓存机制

![](img/7923db8a75d26f6b1db1ad70b25172b2.png)

斯科特·韦伯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

今天，我们将学习如何在 Swift 中使用`NSCache`来缓存`UICollectionView`中的图像。

简而言之，这是您将在本教程中掌握的内容:

*   了解什么是`NSCache`以及如何使用它。
*   创建居中的`UICollectionView`布局。
*   在后台线程上执行图像加载任务。
*   理解为什么当我们想要缓存重对象时，使用`NSCache`比普通的`Dictionary`更好。

这是我们将在本文结尾看到的内容:

![](img/337cc6392fbbe5ef8e3720eecf9c35e8.png)

该项目的完整源代码可以在文章的底部找到。

事不宜迟，我们开始吧。

# 我们开始吧

首先，让我们创建一个仅包含一个`UIImageView`的`PhotoCollectionViewCell`:

一旦一个单元格准备出现在屏幕上，我们覆盖`prepareForReuse()`方法来清除`UIImageView`。

现在，让我们添加一个`UICollectionViewController`子类`PhotosViewController`，并实现它的`UICollectionView`数据源方法:

正如我们所看到的，我们在这里使用了之前定义的`PhotoCollectionViewCell`。我们还告诉集合视图总共有 40 个条目。

现在我们需要指定集合视图单元格的大小和位置。我们需要创建一个`UICollectionViewFlowLayout`，如下所示:

我们希望我们的项目是正方形的，大约是屏幕宽度的一半。我们还希望它们居中，以便在它们之间有一个小插图。为此，我们创建了`inset`属性，并将其作为参数传递给`sectionInset`属性。

因此，我们有一个居中的`UICollectionView`布局。

完成了 UI 部分，现在让我们进入本文的核心部分:使用`NSCache`加载、缓存和显示`UIImages`。

# 加载和缓存图像

首先，让我们在`PhotosViewController`中定义两个属性——一个`NSCache`和一个`DispatchQueue`:

我们将`NSNumber`指定为键的类型(`NSCache`是一个类似于字典的概念)，将`UIImage`指定为对象的类型。我选择了使用`NSNumber`,因为我们希望将单元格的索引存储为键。这将便于在特定的`IndexPath`项目中搜索图像。

需要使用`utilityQueue`在后台线程上执行图像加载任务。

现在我们应该添加`loadImage(completion:)`方法，它将在`URL`处加载一个`UIImage`，然后将它传递到完成处理程序中:

最后，我们可以在`willDisplayCell()`委托方法中使用这个方法，如下所示:

以下是这些步骤的细目分类:

1.  确保显示的单元格是`PhotoCollectionViewCell`。
2.  获取单元格的项目编号。
3.  如果在项目编号处找到缓存图像，检索并将其分配给`UIImageView`。
4.  如果在项目编号处没有缓存的图像，则启动图像加载任务。图像检索后，将图像分配给`UIImageView`。
5.  将加载的图像存储在`NSCache`中，以备将来再次使用。

太好了！现在我们有了一个方便的缓存机制，一旦特定的单元格再次出现在屏幕上，我们就不再需要重新下载图像。

# NSCache 优势

以下是使用`NSCache`而不是普通`Dictionary`的好处:

*   如果应用程序的内存不足，某些项目会自动从`NSCache`中移除。你不需要自己处理这个逻辑。
*   `NSCache`是线程安全的，这意味着当从多个线程访问它时，你不应该担心竞争情况。
*   我们可以指定所需的高速缓存最大大小(以字节为单位)以及放入其中的对象数量:

```
cache.countLimit = 75 (75 images)cache.totalCostLimit = 50 * 1024 * 1024 (50 MB)
```

如果放入高速缓存的对象的总数或大小超过期望的限制，操作系统*可以*开始移除对象，直到总值低于期望的最大值。

# 资源

该项目的源代码可以在 GitHub 上找到:

[](https://github.com/zafarivaev/NSCache) [## zafarivaev/NSCache

### 展示使用 NSCache 在 UICollectionView 中缓存图像的示例项目。为中等教程写的…

github.com](https://github.com/zafarivaev/NSCache) 

# 包扎

要了解更多关于`NSCache`及其对象移除行为的信息，请参见[苹果官方文档](https://developer.apple.com/documentation/foundation/nscache)。

我希望这个教程对你有用。感谢阅读！