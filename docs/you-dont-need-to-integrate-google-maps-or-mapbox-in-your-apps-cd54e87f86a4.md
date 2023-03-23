# 你不需要在你的应用程序中集成谷歌地图或者地图盒子

> 原文：<https://betterprogramming.pub/you-dont-need-to-integrate-google-maps-or-mapbox-in-your-apps-cd54e87f86a4>

## 创建和托管您自己的可设置样式的矢量切片，使地图脱颖而出

![](img/91c5db77e935c5794a5b5e09001b4c9c.png)

谢栋豪在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在本文中，我将向您介绍创建矢量切片的工具集和过程，并向您展示如何在不需要服务器的情况下静态且廉价地托管地图数据。最终，您将能够在基于网络的产品和移动应用程序中使用地图切片。

地图很便宜，而且大部分时间是免费的。不完全是。有些情况下，你可以免费或者至少以合理的价格使用谷歌地图或地图盒子。

然而，在某些情况下，地图会变得非常昂贵，因此不适合小型企业。

此外，尤其是如果你的产品或网站是针对欧盟客户，GDPR 总是一个障碍，你必须克服。自己托管瓦片使得上述所有问题变得不那么严重。

地图已经成为我们日常生活中必不可少的一部分。你很有可能遇到过由谷歌地图、苹果地图或 Mapbox 支持的地图。

如今，大多数地图(如果不是全部的话)都是 OpenStreetMap 社区收集的数据的某种衍生物。

当我开始在[航空公司](https://www.aerwork.app)工作时，我已经知道我需要地图来可视化飞行路线和显示机场位置。

*TL；如果你不关心我是如何得到我的最终结果的，你可以直接跳到“* [*要走的路*](#2312) *”:关于如何转换、提供和显示你自己的地图的逐步指南。*

# 方法 1。使用任何商业地图平台，如 Mapbox 或谷歌地图

作为一个具有强烈视觉焦点的开发人员，前往 Mapbox，安装他们的 JavaScript SDK，在 Mapbox Studio 中设计一些漂亮的地图，这是显而易见的，我就完成了(或多或少，还没有飞行路径，但基本的设置已经完成)。

当我看着他们的许可页面时，我最初的兴奋很快就消失了。对于每个 web 席位(如果我向认证用户提供地图)，我将不得不支付 4 美元。对于一个没有一个付费客户、起价仅为 8 美元的 Plus 层的 SaaS 来说，这似乎是一个障碍。所有其他地图提供商都提供类似价格范围的产品，因此也没有纳入产品中。

# 方法二。我们自己托管地图切片

接下来，我考虑自己提供地图拼图。我是说这不会很难。原始的 OSM 数据在一个 *ODbL* 许可下是免费的，因此被允许在商业产品中使用。

事情发展得很快。原始的 OSM 数据非常详细，因此非常庞大。垃圾桶、树木、建筑、机场，应有尽有。它在那里。向所有 OSM 的贡献者致敬。

同时，如果您想要处理数据，这也是一个问题。事实证明，这是一个相当雄心勃勃的计划，因为没有“只加水”的解决方案。在做了一些研究后，我发现我需要一个 MBTiles 文件，其中包含我感兴趣的世界特定区域(或整个星球)的地图切片。

MBTiles 文件是一种在单个文件中存储地图切片的格式。从技术上讲，它只是一个 SQLite 数据库。通过使用 MBTiles 文件，你可以使用 [Mapbox GL 样式规范](https://docs.mapbox.com/mapbox-gl-js/style-spec/)来设置地图样式，这是一个巨大的好处，因为有一些编辑器可以直观地定义地图样式，例如 [maputnik](https://maputnik.github.io/) 。

所以我需要得到一些 MBTiles。Maptiler (另一个地图平台)提供已经准备好的特定区域甚至整个星球的 MBTiles。

您可以订阅并获得定期更新，或者支付一次性费用来下载所需区域的 MBTiles 文件。如上所述，Aerwork 是一个刚刚起步的 SaaS。我不想仅仅为了提供好看的地图而花费数百甚至数千美元。我很想做那件事，但是那是遥远的将来。

即使欧洲现在足够了，我也找不到任何免费的欧洲 MBTiles 文件(或者至少比`maptiler`提供的便宜一点)。

所以计划成立。我自己制作瓷砖。在偶然发现`maptiler`为将原始 osm 数据转换为可用的 MBTiles 文件这一具体任务提供的开源工具集后，我从`[openmaptiles](https://github.com/openmaptiles/openmaptiles)` repo 中提取了 docker 映像，跟随他们的文档，并开始用 Hamburg 测试它。

汉堡本身相当小，因此是一个很好的起点，看看结果是否可以用在我们的产品中。转换进行得很顺利；已经挺快的了(Macbook Pro，2.9Ghz 6 核 i7，32GB RAM)。如果汉堡很快，为什么不试试德国呢？嗯，很快就证明需要很长时间。我仍然对在我的机器上对欧洲做同样的事情持乐观态度。现在挫折开始了。

运行了 14 个小时后，它崩溃了，告诉我没有足够的空间了。700GB 的空闲空间不够用？原始源数据大约为 30GB。700 GBs 应该够了。很有可能我做错了什么。我彻底重读了这些文件，以确保第二次我把一切都做对了。这一次，它在大约 12 小时后崩溃，出现了一些非描述性错误。我执行了与上一次迭代相同的步骤。太好了。谢谢你什么都没做。

我想过采取不同的方法。因为我只想显示飞行路线和机场，所以我想去掉建筑物和其他细节。建筑物是 OSM 数据中最大的一块，因此占用了大量的时间和计算能力。

幸运的是，有一些工具可以帮助你编辑原始的 OSM 数据。首先，我开始使用[渗透](https://github.com/openstreetmap/osmosis/releases/tag/0.48.3)。最新发布的是 2020 年 9 月。尽管它不是最新的，但我还是试了一下。

在试图找出如何正确过滤原始数据时，我能够缩小原始 OSM 数据的大小。不是很广泛，但至少有一点。然而，即使对汉堡来说，这也需要相当长的时间。

我重新运行了 openmaptiles 工具，但仍然遇到了许多错误。好吧，也许有另一种工具来过滤不想要的数据？向[锇](https://github.com/osmcode/osmium-tool)问好。它是用 C++写的，应该很快。我试了一下。

我还设法缩小了 OSM 数据的规模。它相当快。渗透作用远不及锇，但对欧洲来说，它仍然需要永远，在某些时候，我决定杀死这个任务。所以，基本上，我又回到了起点。

在做了进一步的研究后，我发现 [tilemaker](https://github.com/systemed/tilemaker) 是 openmaptiles 的替代产品。它也是基于 C++的，没有依赖性。哦，孩子，祈祷吧。我又从汉堡开始。很顺利。这就是我们的 MBTiles 文件。

我将它放入我们的 tileserver-gl(它读取 MBTiles 文件并为客户需要的特定区域提供请求的切片)中，瞧，我有了一个包含自转换和自托管切片的工作地图。诶哈！我简直不敢相信。在心理上为转变欧洲做好准备后，我重读了 tilemaker doc，只为了第一次就把它弄对。不幸的是，医生说 tilemaker 应该只用于小块提取物，而不是整个大陆。

早上好。今天是土拨鼠日。向比利·穆雷问好。WTF。我做了一些研究，发现了马塞尔·诺曼的这篇博文(德语)。

根据他的发现，tilemaker 使用了相当多的 RAM。他在做了一些基准测试后发现，原始数据的文件大小需要 12 个 RAM。

因此，欧洲将需要大约 256GB 的内存。这种 RAM 连什么都外包给 AWS 的时候都买不起，所以我差点放弃了。在不依赖第三方公司的情况下，展示一幅风格精美的地图并不难。

在深呼吸和重新思考所有选项之后，我回到了最初的方法，使用开源的 openmaptiles 工具，只增加了一点资源。

幸运的是，AWS 提供了一些强大的 EC2 实例来简化这个过程。这种方式不会整天阻塞我的机器，因为 AWS 以合理的价格提供计算时间。

所以在下面，你会找到最终证明非常有效的解决方案——前提是你有足够的计算资源。

最后，我学到了很多关于 OSM 数据本身，如何处理原始 osm 数据，以及如何相信自己的直觉。大多数时候你的直觉是对的，第一选择有时候是最好的。这次我不得不吃了苦头。

# 要走的路

1.  启动 AWS EC2 实例(我使用 c5.4xlarge，它针对计算任务进行了优化。它有 16 个 vCPUs、32GB RAM 和 500GB EBS gp2 SSD 存储卷)
2.  安装 docker 和 docker-compose

3.克隆处理原始 OSM 数据所需的工具，并将其转换为*。mbtiles 文件

这需要相当长的时间。我们最后一次去欧洲花了 48 小时。

为了向您的前端或移动应用程序提供图块，有两个选项:

选项 1:运行服务器(例如，基于 PHP 的 tileserver 或 tileserver-gl )，它负责从 MBTiles 文件中提取所需的 tiles。

选项 2:提取图块并静态托管它们。

我选择了后一种方法，因为我总是希望将维护保持在最低限度，拥有易于掌握的基础设施，并利用 AWS Cloudfront 的缓存机制。

使用`[mbutil](https://github.com/mapbox/mbutil)`是正确的方法。很简单。请注意 t*。由`mbutil`提取的 pbf 文件被压缩。所以，如果你使用 S3，确保在复制文件时包含了标志`—-content-encoding gzip`。

这为存储在 S3 中的对象添加了额外的元数据，并帮助 Cloudfront 正确地处理它。如果你想动态压缩文件或者根本不想使用压缩，你也可以解压文件。

注意:使用 EC2 5c.4xlarge 实例，500GB EBS gp2 卷，运行 Ubuntu Server 20.04，需要 48 小时才能获得欧洲的 MBTiles 文件。我一直处于紧张状态，希望这个过程不会在中间随机崩溃。

可选步骤—解压缩文件

*可选步骤——使用服务器托管和提供瓷砖*

出于某些原因，您可能希望将 tile 保存在一个文件中(例如，定期更新)，并利用 tileserver 为您完成所有繁重的工作。提供切片、缓存切片甚至提供预渲染的栅格切片都是自己运行 tileserver 的好处。实现起来非常简单:

*   下载/上传 T [ileServer PHP](https://github.com/maptiler/tileserver-php) 到你的服务器上
*   复制*。mbtiles 文件，位于根目录下，与其他 tileserver 文件相邻
*   打开你的浏览器，导航到你的目录，它就在那里。

TileServer PHP 还提供了一个代码片段，告诉你如何使用 [mapbox-gl-js](https://github.com/mapbox/mapbox-gl-js) 在浏览器中渲染地图(从 mapbox-gl-js 2.x 开始，他们已经调整了他们的许可，现在如果没有有效的订阅，你就不能使用 js 库了。因此你必须使用 1.x 或者 [maplibre-gl-js](https://github.com/maplibre/maplibre-gl-js) 。

# 技巧

1.为了加速这个过程，你可以用锇工具过滤掉建筑物来处理原始 OSM 数据(建筑物占据了很大的空间)。

2.防止使用超过 14 的缩放级别。相信我，你不会想去那里的。

3.如果你计划使用较小的提取，例如，单个国家，我强烈建议使用 [tilemaker](https://github.com/systemed/tilemaker/) 。与`openmaptiles`方法相比，它快得惊人。

# 资源、工具、原始数据和其他有用的链接

*   [AWS EC2 5c.4xlarge，500GB EBS gp2 卷，运行 Ubuntu Server 20.04](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/compute-optimized-instances.html)
*   [Openmaptiles 工具](https://github.com/openmaptiles/openmaptiles)(将原始 OSM 数据转换成 MBTiles)
*   [锇工具](https://osmcode.org/osmium-tool/)(处理原始 OSM 数据)
*   [TileServer PHP](https://github.com/maptiler/tileserver-php) (提供 MBTiles)
*   [OSM 地质调查局数据](https://download.geofabrik.de/europe.html)(下载原始 OSM 数据)
*   [Tilemaker am Limit](https://wheregroup.com/blog/tilemaker-am-limit/) (关于 Tilemaker am 使用的文章。*德语*
*   [预转换](https://openmaptiles.org/) [*。mbtiles](https://openmaptiles.org/) (虽然对于商业用途来说很贵，但取决于你的预算)