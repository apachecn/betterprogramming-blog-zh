# 带有 Swift uicollectionview cells 和 Objective-C AVFoundation 的 Tenor API GIFs

> 原文：<https://betterprogramming.pub/tenor-api-gifs-with-swifts-uicollectionviewcells-objective-c-avfoundation-736c6e14e423>

## 利用 Tenor 的 API 文档的简单性来创建 GIF 播放单元格

![](img/0f74331104d2aed403229762dc799658.png)

雅各布·欧文在 [Unsplash](https://unsplash.com/search/photos/image?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

[流行的 GIF 搜索引擎 Tenor](https://tenor.com/) 是一个很好的 API 端点，可以用来演示如何使用 Objective-C 中的原生功能`AVFoundation`实现 Swift 的水平和垂直滚动视图类！

凭借其丰富的文档、快速入门指南、最佳实践和详细的`curl`命令，Tenor 为 iOS 应用程序开发提供了充足的机会。

对于那些为了 SDK 的易用性而忘记了 API 集成的复杂性的人来说，这也是非常棒的！

[](https://tenor.com/gifapi/documentation#endpoints-registershare) [## GIF API —更好、更快、免费|期限文档

### Tenor Search 以 30 多种语言提供最相关的 gif。将 Tenor 的 GIF API 集成到您的应用程序中](https://tenor.com/gifapi/documentation#endpoints-registershare) 

# 你将完成什么

在这篇博客中，你将利用 Tenor 的 API 文档的简单性，用 Swift 的`UICollectionViewCells`类创建 GIF 播放单元格。

特别是，您将展示以下内容:

*   Swift 和 Objective-C 的知识，其中视图类在 Swift 中，视频播放器类在 Objective-C 中。
*   原生 iOS UIKit(故事板、自动布局和大小类)。
*   合适的第三方网络或图像缓存框架，如`Alamofire` / `SDWebImage`。

通过展示这些内容，您将实现以下目标:

*   获取端点数据，将其显示在集合视图中。
*   用户键入时更新收藏视图的搜索栏。
*   点击收藏视图单元格会导致应用程序开始播放视频。
*   在 iPhone 和 iPad 上以所有四个方向正确显示。
*   单元测试。

# 设置

在 Xcode 中打开一个单视图应用程序模板。通过命令行界面终端导航到根目录。

在根目录下，写`pod init`。在初始化一个 pod 之后，将`Alamofire`和`SDWebImage`添加到您的 podfile 中，并运行`pod install`。

就是这样！你都准备好了。

# **获取端点数据**

下一步是获取端点数据。当你根据模型视图，视图模型结构构建你的应用时，你将构建四个`structs`，每个都符合苹果全新的`codable`协议。

第一个结构是 GIF 结构。

## GIF `struct`

在你的 GIF `struct`中，声明以下常量:`tags: [String]?`、`url: URL?`、`media: [MediaCollection]?`、`created: Double?`、`shares: Int?`、`itemURL: URL?`、`hasAudio: Bool?`、`title: String?`、`id: String?`和编码键。

对于我们的编码键，您将使用一个`enum`和案例:

```
enum CodingKeys: String, CodingKey {
    case tags, url, media, created, shares
    case itemURL = "itemURL"
    case hasAudio = "has audio"
    case title, id
}
```

## **media collection**

你的下一个`struct`叫`MediaCollection`。它比其他结构简单得多，因为它只是将各种类型的媒体解码成一种快速类型。

在你的`MediaCollection` `struct`中，声明以下常量:`nanoMP4``nanoWebM``tinyGIF``tinyMP4``tinyWebM``webM``gif``mp4``loopedMP4``mediumGIF``nanoGIF`，作为类型可选`Media`。

对于我们的编码键，您将使用带有 cases 的`enum`，为除`gif`或`mp4`之外的所有键提供设置字符串值，这是显而易见的。

## **媒体**媒体`**struct**`

你的下一个结构叫做`Media`。

在您的`Media`结构中，声明以下常量:`url: URL?`、`dimension: [Int]?`、`duration: Double?`、`preview: URL?`、`size: Int64?`。

对于您的编码键，枚举所有没有值的事例，除了`dimension`，它的值将被设置为`dims`。

## **回应** `**struct**`

最后但同样重要的是你的`Response`结构。您将一般地声明这个结构，这样当且仅当`Response`符合可解码协议时，它才是可解码的。

为`webURL:URL?`、`results: T?`和`next: String?`声明常量。

对于您的编码键，枚举所有常量的情况，除了`webURL`，其值将设置为`weburl`。

厉害！您已经创建了自己的结构。这是获取端点数据的第一步。下一步是建立对 API 端点的调用。

# 端点

为将管理`convertItems`、`getURL`、`getAuthenticationParameters`、`getLimitingParameters`和`getAnonymousIdParameters`方法的`URLManager`创建一个`struct`。

作为私有常量的程序`convertItems`:

```
static private let convertItems: ((Parameters) -> [URLQueryItem]) = { 
parameters in return parameters.map { 
      return URLQueryItem(name: $0, value: $1) 
     }    
}
```

作为公共方法的 TK 程序`getURL`:

作为私有方法的程序`getAuthenticationParameters`:

```
static private func getAuthenticationParameters() -> Parameters {            
    return ["key"   : Configuration.key]    
}
```

作为私有方法的程序`getAnonymousIdParameters`:

```
static private func getAnonymousIdParameters() -> Parameters? {        
    guard let anonymoudId = UserDefaults.standard.string(forKey: kAnonymousIdKey) 
    else { return nil }        
    return ["anon_id"   : anonymoudId]
}
```

程序`getLimitingParameters`作为私有方法:

```
static private func getLimitingParameters() -> Parameters {          
    return ["limit" : "\(Configuration.pageLimit)"]    
}
```

最后，但同样重要的是，在这个列表中用几个例子来编程一个`enum`:

```
import Foundation 
enum EndPoint: String {        
case anonymousId    =   "/v1/anonid"        
case search         =   "/v1/search"
}
```

你需要再添加两个文件，一个叫`Alias.swift`，一个叫`Constants.swift`。首先，将 a `typealias`编程为:

```
typealias Parameters = [String:String]
```

其次，将常量`kAnonymousIdKey`编程为`AnonymousId`。将最后两个文件组合到一个名为 Helper 的文件夹中。

# 搜索栏

我们实现搜索栏的架构是双重的:首先，一个名为`SearchVC.swift`的视图控制器，其次，一个名为`SearchVC+CollectionView.swift`的类别扩展。

添加一个名为`SearchVC.swift`的文件。在文件内，导入:

```
import UIKit
import Alamofire
import AlamofireImage
```

创建网点:

```
@IBOutlet weak var searchBar: UISearchBar!        
@IBOutlet weak var resultCollectionView: UICollectionView!
```

为数据创建属性:

```
// MARK: - Data        
internal lazy var resultsArray: [GIF] = []        
internal let cellIdentifier = "PreviewCell"        
internal let cellHeight: CGFloat = 250        
private let anonIdViewModel = AnonIdViewModel()        
private let searchViewModel = SearchViewModel()
```

在`viewDidLoad()`中调用`fetchAnonymousId()`。

创建一个获取匿名 id 的函数:

```
private func fetchAnonymousId() {                    ActivityIndicator.startAnimating()                  
    anonIdViewModel.getAnonymousId { 
[weak self] success in                        
    ActivityIndicator.stopAnimating()                        
         if success {                
             self?.fetchResult()            
             } else {
                self?.showRetryAlert()
            }
        }
    }
```

如果我们的调用成功，您将在一个名为`fetchResult()`的函数中获取结果。

```
private func fetchResult(for keyword: String = "") { ActivityIndicator.startAnimating() searchViewModel.search(using: keyword) { [weak self] (data, error) in DispatchQueue.main.async {                
    if let error = error {
                    // if canceled, do not show alert
                    guard (error as NSError).code != -999 else { return }
                    self?.showErrorAlert(with: error.localizedDescription)
                }
                else if let data = data {                      self?.resultsArray = data                    self?.resultCollectionView.reloadData()
                }                                ActivityIndicator.stopAnimating()
            }
        }
    }
```

编程调用错误警报:

```
private func showErrorAlert(with message: String) { let alertController = UIAlertController(title: "Error",                                                message: message,                                            preferredStyle: .alert)
                let okayAction = UIAlertAction(title: "Okay", style: .default)
        alertController.addAction(okayAction)                present(alertController, animated: true, completion: nil)
    }}
```

如果调用失败，编程一个名为`showRetryAlert()`的函数:

```
private func showRetryAlert() {
                let alertController = UIAlertController(title: "Error",                                                message: "Unable to fetch anonymous id.",
                                                preferredStyle: .alert)
                let retryAction = UIAlertAction(title: "Retry", style: .default) { [weak self] _ in self?.fetchAnonymousId()        } alertController.addAction(retryAction)
                let withoutAction = UIAlertAction(title: "Search without anonymous id.", style: .default) { [weak self] _ in self?.fetchResult()        } alertController.addAction(withoutAction) present(alertController, animated: true, completion: nil)
    }
```

对方向变化进行编程:

```
@objc 
func orientationDidChange() { resultCollectionView.collectionViewLayout.invalidateLayout()
}
```

此外，您需要编程来更新特征集合:

```
override func traitCollectionDidChange(_ previousTraitCollection: UITraitCollection?) {        resultCollectionView.collectionViewLayout.invalidateLayout()    
}
```

# 定向观察员

如果用户改变方向，应用程序需要相应地更新集合视图的 UI。为此，应用程序必须通过观察器接收通知。

观察者观察状态的变化。如果方向状态发生变化，观察者会收到一个可以触发代码变化的通知。这两个文件都需要添加和删除关于方向变化的通知的观察者:

```
override func viewDidAppear(_ animated: Bool) {           
    super.viewDidAppear(animated)                  
    NotificationCenter.default.addObserver(self, selector: #selector(SearchVC.orientationDidChange), name: Notification.Name.UIDeviceOrientationDidChange, object: nil)    
} override func viewDidDisappear(_ animated: Bool) {          
    super.viewDidDisappear(animated)                
    NotificationCenter.default.removeObserver(self)    
}
```

# 对 UISearchBarDelegate 进行编程

前面，您添加了一个`UISearchBar`实例作为出口。

然而，`UISearchBar`的实例必须将它们的功能委托给声明它们的控制器。

以下是如何扩展搜索视图控制器的功能以处理搜索栏功能:

```
extension SearchVC: UISearchBarDelegate { func searchBar(_ searchBar: UISearchBar, textDidChange searchText: String) {      

fetchResult(for: searchText) 

   } 
```

这段代码被添加到你的搜索栏控制器的底部。

# 对 SearchVC+CollectionView.swift 进行编程

`SearchVC+CollectionView.swift`是`SearchVC`的扩展，但是在一个单独的文件中，以保持视图控制器的集合视图代码组织良好(即分离关注点)。

第一部分是采用`UICollectionViewDataSource`的协议，为每个部分中的项目数和这些部分中每个项目的单元格实现其所需的方法:

```
extension SearchVC: UICollectionViewDataSource { func collectionView(_ collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int { return resultsArray.count } func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell { let cell = collectionView.dequeueReusableCell(withReuseIdentifier: cellIdentifier, for: indexPath) as? PreviewCell if let url = resultsArray[indexPath.row].media?.first?.mp4?.preview { cell?.thumbImageView.af_setImage(withURL: url,
placeholderImage:  imageLiteral(resourceName: "placeholder"),imageTransition: .crossDissolve(0.1)) } return cell ?? UICollectionViewCell() }}
```

下一步是实现集合视图委托:

```
extension SearchVC: UICollectionViewDelegate {     
   func collectionView(_ collectionView: UICollectionView, didSelectItemAt indexPath: IndexPath) {   
             guard let url = resultsArray[indexPath.row].media?.first?.mp4?.url else { return }                let _ = VideoPlayer.playVideo(with: url)    
             }
}
```

最后一步是为集合视图的委托实现流布局。

```
extension SearchVC: UICollectionViewDelegateFlowLayout {
        func collectionView(_ collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, sizeForItemAt indexPath: IndexPath) -> CGSize {          
      switch UIDevice.current.userInterfaceIdiom {    
      case .carPlay, .tv, .unspecified: fallthrough        
      case .phone:            
          return CGSize(width: collectionView.frame.width, height: cellHeight)        
      case .pad: 
        return CGSize(width: collectionView.frame.width/2, height: cellHeight)        
      }     
    }
}
```

# 播放视频

你将根据语言来实现视频播放器。

`AVFoundation`是苹果用于视频播放的原生 SDK，但它是用 Objective-C 编写的，不是用 Swift 编写的。

虽然我们可以在 Swift 中实现它，但是在 Objective-C 中实现它并没有什么优势，因为它比 Swift 更接近于`AVFoundation`的核心。

创建两个 Objective-C 文件，一个`VideoPlayer.h`，另一个`VideoPlayer.m`。

在头文件中，添加以下内容:

```
////  VideoPlayer.h
//  Tenorize
////  Created by Eric Giannini on 8/29/18.
//  Copyright © 2018 Eric Giannini. All rights reserved.
// 
#import <AVKit/AVKit.h>
#import <Foundation/Foundation.h> 
@interface VideoPlayer : NSObject
 +(nonnull AVPlayerViewController *)playVideoWithURL:(NSURL *_Nonnull)url; 
@end
```

您在头文件中声明该方法，但实际上是在实现文件中编写该方法。

这是:

```
#import "VideoPlayer.h"
#import <Foundation/Foundation.h> 
@implementation VideoPlayer+(AVPlayerViewController *)playVideoWithURL:(NSURL *)url {
   AVPlayer *player = [[AVPlayer alloc] initWithURL:url];
   AVPlayerViewController *playerVC = [[AVPlayerViewController alloc] init];    
   [playerVC setPlayer:player];
   UIWindow *window = [[[UIApplication sharedApplication] delegate] window];
   [[window rootViewController] presentViewController:playerVC animated:YES completion:^{        
   [player play];    
}];
    return playerVC;
}@end
```

你的方法是用一个 URL 调用的(也就是 Tenor API 的 GIF URL)。

`AVPlayer`的实例既是初始化的又是分配的资源。初始化`AVPlayerViewController`的实例并分配资源。

接下来，`AVPlayer`的实例被设置为`AVPlayerViewController`实例的播放器。视图控制器被设置为播放器。

# 摘要

用`Alamofire`从 API 端点获取数据，用`SDWebImage`将数据显示在集合视图中。

您可以创建一个搜索栏，当用户键入新的单元格时，它会更新集合视图。您可以点击收藏视图单元格，以便播放 gif。

您创建应用程序，以便它在 iPhone 和 iPad 上以所有四个方向正确呈现，本机使用 iOS UIKit(即故事板、自动布局和大小类)。您创建单元测试。

*   用户键入时更新收藏视图的搜索栏。
*   点击收藏视图单元格会导致应用程序开始播放视频。
*   在 iPhone 和 iPad 上四个方向都能正确显示。
*   单元测试测试功能

# 代码库

如果你想查看全功能应用程序的[源代码，请随意下载。](https://github.com/ericgiannini/tenorize)