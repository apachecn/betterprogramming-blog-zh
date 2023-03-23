# 如何有效地在 UITableViews 和 UICollectionViews 中加载图像

> 原文：<https://betterprogramming.pub/how-to-efficiently-load-images-in-uitableviews-and-uicollectionviews-a39afd608b1a>

## 实现一个您现在可以在项目中使用的方便的 API

![](img/16873df0d05f75a90793064479d2e7e4.png)

雅各布·欧文斯在 [Unsplash](https://unsplash.com/s/photos/pictures?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

当您的应用程序在表格视图或集合视图中显示来自网络的图像时，您需要异步加载图像以确保您的列表平滑滚动。更重要的是，您需要以某种方式将您正在加载的图像连接到列表中的正确单元格(从现在开始，我将说*列表*而不是*表格视图*或*集合视图*)。如果单元格不在视图中，并被重新用于显示带有新图像的新数据，您需要取消正在进行的图像加载，以确保新图像被立即加载。此外，为了确保我们不会过于频繁地访问网络，我们需要某种方式将图像缓存在内存或磁盘上，这样，如果我们过去已经获取了图像，就可以使用图像的本地版本。

基于此，我们可以确定在为我们的单元异步加载图像时需要解决的三个核心问题:

1.  将加载的图像设置在正确的单元格上。
2.  重用单元时取消正在进行的加载。
3.  缓存加载的图像以避免不必要的网络调用。

在本文中，我将向您展示如何构建一个简单的图像加载器类，并编写一个表格视图单元格来帮助我们解决所有这些问题。我还将向您展示如何使用相同的图像加载器，通过一些奇特的助手来增强`UIImage`。

# 构建一个简单的图像加载器

当使用`URLSession`发出 GET 请求时，通常会通过数据任务来完成。通常情况下，你不会保留那个数据任务，因为你不需要它。但是如果您保留了对数据任务的引用，您可以在以后取消它。

我将在我们正在构建的图像加载器中使用`[UUID: URLSessionDataTask]`的字典，因为这将允许我跟踪正在运行的下载，并在以后取消它们。我还将使用一个字典`[URL: UIImage]`作为加载图像的简单内存缓存。

基于此，我们可以开始编写图像加载器了:

```
class ImageLoader { 
  private var loadedImages = [URL: UIImage]() 
  private var runningRequests = [UUID: URLSessionDataTask]() 
}
```

我们还可以实现一个`loadImage(_:completion:)`方法。这个方法将接受一个 URL 和一个完成处理程序，它将返回一个 UUID，用于在以后唯一地标识每个数据任务。实现如下所示:

```
func loadImage(_ url: URL, _ completion: @escaping (Result<UIImage, Error>) -> Void) -> UUID? { 
  // 1 
  if let image = loadedImages[url] { 
    completion(.success(image)) 
    return nil 
  }   // 2 
  let uuid = UUID()   let task = URLSession.shared.dataTask(with: url) { data, response, error in     // 3 
    defer {self.runningRequests.removeValue(forKey: uuid) }     // 4 
    if let data = data, let image = UIImage(data: data) { 
      self.loadedImages[url] = image 
      completion(.success(image)) 
      return 
    }     // 5 
    guard let error = error else { 
      // without an image or an error, we'll just ignore this for now 
      // you could add your own special error cases for this scenario 
      return 
    }     guard (error as NSError).code == NSURLErrorCancelled else { 
      completion(.failure(error)) 
      return 
    }     // the request was cancelled, no need to call the callback 
  }   task.resume()   // 6 
  runningRequests[uuid] = task 
  return uuid 
}
```

让我们按照编号的注释一步一步地检查前面的代码。

1.  如果 URL 已经作为一个键存在于内存缓存中，我们可以立即调用完成处理程序。因为没有活动的任务，也没有要取消的任务，所以我们可以返回`nil`而不是`UUID`实例。
2.  我们创建一个`UUID`实例，用于标识我们将要创建的数据任务。
3.  当数据任务完成时，它应该从正在运行的请求字典中删除。在我们离开数据任务的完成处理程序的范围之前，我们在这里使用一个`defer`语句来删除正在运行的任务。
4.  当数据任务完成时，我们可以从数据任务的结果中提取一个图像，它被缓存在内存缓存中，并使用加载的图像调用完成处理程序。在此之后，我们可以从数据任务的完成处理程序返回。
5.  如果我们收到一个错误，我们检查错误是否是由于任务被取消。如果错误不是取消任务，我们会将其转发给`loadImage(_:completion:)`的调用者。
6.  使用在步骤 2 中创建的`UUID`将数据任务存储在运行请求字典中。然后将这个`UUID`返回给调用者。

请注意步骤 3。通过 5。所有这些都发生在数据任务的完成处理程序中。这意味着所列步骤的执行顺序不是线性的。第一步。第二。首先执行，然后执行步骤 6。，然后是 3。通过 5。

现在我们有了加载图像的逻辑，让我们看看允许我们取消正在进行的图像下载的一些逻辑:

```
func cancelLoad(_ uuid: UUID) { 
  runningRequests[uuid]?.cancel() 
  runningRequests.removeValue(forKey: uuid) 
}
```

该方法接收一个`UUID`，用它来查找一个正在运行的数据任务，并取消该任务。如果任务存在，它还会从正在运行的任务字典中删除任务。相当简单，对吧？

让我们看看如何在表格视图的`cellForRowAtIndexPath`方法中使用这个加载器:

```
// 1 
let token = loader.loadImage(imageUrl) { result in 
  do { 
    // 2 
    let image = try result.get() // 3 
    DispatchQueue.main.async { 
      cell.cellImageView.image = image 
    } 
  } catch { 
    // 4 
    print(error) 
  } 
} // 5 
cell.onReuse = { 
  if let token = token { 
    self.loader.cancelLoad(token) 
  } 
}
```

让我们一步一步地重复前面的代码:

1.  调用图像加载器的`loadImage(_:completion:)`方法，加载器返回的 UUID 存储在一个常量中。
2.  在`loadImage(_:completion:)`的完成处理程序中，我们从完成的结果参数中提取结果。
3.  如果我们成功提取了一个图像，我们将分派到主队列，并在单元格的`imageView`属性上设置提取的图像。不确定什么是主队列调度？在[这篇文章](https://www.donnywals.com/appropriately-using-dispatchqueue-main/)中阅读更多内容。
4.  如果出错了，打印错误。你可能需要在你的应用中做些别的事情。
5.  我很快会给你们看一个细胞子类的例子。重要的一点是，我们使用从`loadImage(_:completion:)`收到的 UUID 来取消加载程序对该 UUID 的加载操作。

注意，我们在`cellForRowAt`方法中这样做。这意味着每当我们被要求在列表中显示一个单元格时，这个方法就会被调用。因此，加载和取消与细胞的生命周期紧密相关，这正是我们在这种情况下想要的。让我们看看样本单元格中的`onReuse`是什么:

```
class ImageCell: UITableViewCell { @IBOutlet var cellImageView: UIImageView! 
  var onReuse: () -> Void = {}  override func prepareForReuse() { 
    super.prepareForReuse() 
    onReuse() 
    cellImageView.image = nil 
  } 
}
```

当单元格的`prepareForReuse`方法被调用时，`onReuse`属性是一个闭包。我们还从`prepareForReuse`的单元格中移除了当前图像，这样在加载新图像时就不会显示旧图像。单元格经常被重用，所以在`prepareForReuse`中进行适当的清理对于防止单元格中的旧数据在您不希望的时候出现是至关重要的。

如果你在你的应用程序中实现了所有这些，你就有了一个不错的加载图片的策略。您可能希望添加一个监听器来监听应用程序通知中心发出的内存警告，也可能希望将图像缓存到磁盘和内存中，但我认为这不太适合本文的讨论范围。但是，如果您想实现自己的图像加载器，请记住这两个特性。监听内存警告尤其重要，因为如果你的应用程序在内存缓存中存储图像，消耗了太多内存，它可能会被操作系统杀死。

# 增强 UIImageView 以创建漂亮的图像加载 API

在我们实现花哨的助手之前，让我们重构我们的单元和`cellForRowAt`方法，这样它们已经包含了我们想要编写的代码。`prepareForReuse`方法将如下所示:

```
override func prepareForReuse() {
  cellImageView.image = nil 
  cellImageView.cancelImageLoad() 
}
```

这会将当前图像设置为`nil`，并告诉图像视图停止加载它正在加载的图像。`cellForRowAt`中的所有图像加载代码应替换为以下内容:

```
cell.cellImageView.loadImage(at: imageUrl)
```

是的，我们以前所有的代码现在都是一行。

为了让这种新的加载和取消方式发挥作用，我们将实现一个名为`UIImageLoader`的特殊图像加载器类。它将是一个单一对象，管理应用程序中所有`UIImageView`实例的加载，这意味着您最终将为整个应用程序使用一个缓存。通常你可能不希望这样，但在这种情况下，我认为这是有意义的。以下代码概述了`UIImageLoader`的框架:

```
class UIImageLoader { 
  static let loader = UIImageLoader() 
  private let imageLoader = ImageLoader()  
  private var uuidMap = [UIImageView: UUID]()   private init() {}   func load(_ url: URL, for imageView: UIImageView) {   } 

  func cancel(for imageView: UIImageView) {   } 
}
```

加载器本身是一个静态实例，它使用前一节中的`ImageLoader`来实际加载图像并缓存它们。我们还有一个`[UIImageView: UUID]`字典来跟踪当前活动的图像加载任务。我们基于`UIImageView`映射这些，这样我们可以将单个任务标识符连接到`UIImageView`实例。

`load(_:for:)`方法的实现如下所示:

```
func load(_ url: URL, for imageView: UIImageView) { 
  // 1 
  let token = imageLoader.loadImage(url) { result in 
    // 2 
    defer { self.uuidMap.removeValue(forKey: imageView) } 

    do { 
      // 3 
      let image = try result.get() 
      DispatchQueue.main.async { 
        imageView.image = image 
      } 
    } catch { 
      // handle the error 
    } 
  } 

  // 4 
  if let token = token { 
    uuidMap[imageView] = token 
  } 
}
```

该代码逐步执行以下操作:

1.  我们使用传递的 URL 启动图像加载`load(_:for:)`。
2.  当加载完成时，我们需要通过移除从字典中加载图像的`UIImageView`来清理`uuidMap`。
3.  这个和之前在`cellForRowAt`里做的差不多。图像从结果中提取出来，并设置在图像视图本身上。
4.  最后，如果我们从图像加载器接收到一个令牌，我们将它保存在`[UIImageView: UUID]`字典中，这样我们可以在以后需要取消加载时引用它。

`cancel(for:)`方法有以下实现:

```
func cancel(for imageView: UIImageView) { 
  if let uuid = uuidMap[imageView] { 
    imageLoader.cancelLoad(uuid) 
    uuidMap.removeValue(forKey: imageView) 
  } 
}
```

如果我们对传递的图像视图有一个活动的下载，它将被取消并从`uuidMap`中删除。和你之前看到的非常相似。

我们现在需要做的就是为`UIImageView`添加一个扩展，以添加您之前看到的`loadImage(at:)`和`cancelImageLoad()`方法:

```
extension UIImageView { 
  func loadImage(at url: URL) { 
    UIImageLoader.loader.load(url, for: self) 
  }   func cancelImageLoad() { 
    UIImageLoader.loader.cancel(for: self) 
  } 
}
```

两种方法都将`self`传递给图像加载器。由于扩展方法被添加到了`UIImageView`的实例中，这有助于图像加载器将它加载的 URL 连接到我们想要显示图像的`UIImageView`实例，从而为我们留下了一个非常简单易用的 API！很酷的东西，对吧？

更好的是，这种新策略还可以用于不在表格视图单元格或集合视图单元格中的图像。它可以用于你的应用程序中的任何图像视图！

# 概括起来

异步加载数据本身就是一个棘手的问题。当与表格视图(和集合视图)单元格转瞬即逝的特性相结合时，您会遇到一系列全新的问题。在这篇文章中，您看到了如何使用`URLSession`和一个非常简单的内存缓存实现一个智能机制来启动、完成和取消图像下载。

在创建了一个简单的机制之后，您看到了如何创建一个额外的 loader 对象和一些对`UIImageView`的扩展来创建一个非常简单易用的 API，将图像从 URL 直接加载到您的图像视图中。

请记住，我在这里向您展示的实现还不能用于生产。您需要在内存管理方面做一些工作，并可能添加一个磁盘缓存来使这些对象为黄金时间做好准备。