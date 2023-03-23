# 将谷歌地图与 Rails 应用集成

> 原文：<https://betterprogramming.pub/integrate-google-maps-with-a-rails-app-5e5012e8cf16>

## 操作指南

![](img/cb53dc4292f28d3d3fd4e71b76b84022.png)

照片由 [GeoJango Maps](https://unsplash.com/@geojango_maps?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我最近在我的一个 Rails 项目中添加了一个特性，允许用户输入一个位置，然后用一个交互式地图返回一个啤酒厂列表。Google Maps API 显然是一个受欢迎的选择，但是我惊讶地发现网上并没有很多资源可以将它整合到 Rails 应用中。为了解决这个问题，让我们来看看如何使用这个指南来制作自定义地图。

## 设置

首先，让我们设置地图将要去的空间。在视图中，文件为地图创建一个部分。给它一个类似“地图”的简单 id。

注册[谷歌地图 JavaScript API](https://developers.google.com/maps/documentation/javascript) 。一旦你有了 API 密匙，就把它放在`.env`文件中。确保这个文件是在`.gitignore`中声明的，这样你就不会把你的密钥推给 Github 了！

您需要将每个位置的纬度和经度输入到 Google Maps API 中。为此我推荐使用[地理编码器](https://github.com/alexreisner/geocoder)宝石。

它可以将街道地址转换成坐标，然后保存到您的模型中。安装的最后一部分是创建一个 JavaScript 文件。

如果使用轨道 6，它应该进入`app/javascript/packs`。对于 Rails 7，它应该进入的目录是`app/javascript`。将文件命名为`map.js`。

## 传递坐标

纬度和经度数据需要从我们的 Rails 页面传递到 JavaScript 文件，以便在其中创建地图和标记。因为我们正在创建一个根据用户输入而变化的地图，所以我们必须想办法将位置数据发送到我们的 JavaScript 代码，然后将这些数据发送到 Google Maps API 来生成地图。为此，在视图页面上显示搜索结果，并为每个带有坐标的项目添加数据标签。

列表中显示的每个啤酒厂在它们的`<li>`标签内都有坐标，分别为`data-latitude`和`data-longitude`。啤酒厂所在城市的坐标在`<div>`中，id 为“breweries”。

这样，信息就是 DOM 的一部分，可以被我们的 JavaScript 代码访问。

## JavaScript 代码

在`map.js`文件中，我们将有一个创建地图的函数，从页面中获取坐标，并用这些坐标向地图添加标记。地图中心的位置(在这种情况下是一个城市)应该被指定，并由您决定如何获取这些坐标(同样，[地理编码器](https://github.com/alexreisner/geocoder)对此很有用)。

为了简洁起见，我们假设您已经有了这些信息。

该函数将获取城市的坐标，并在创建地图时将其用作中心位置。然后，它将获取每个啤酒厂列表项，并对它们进行迭代，检索它们的坐标，并创建标记放置在地图上，以指定每个啤酒厂的位置。有了这些，我们现在可以在视图中调用 Google Maps API 了！

## 创建地图

现在唯一要做的就是设置我们的视图，以便调用 Google Maps API 并使用我们的 JavaScript 代码来构建地图。我们的`map.js`文件可以通过 Rails 助手获得。这取决于您使用的是 Rails 6 还是 Rails 7。

Rails 6: `javascript_pack_tag 'map'`

轨道 7: `javascript_import_module_tag 'map'`

我们将把它放在视图中。对谷歌地图 API 的调用是使用一个`<script>`标签完成的。这也有一个 Rails 助手:

有了这两个助手，您的视图应该看起来像这样:

Google Maps API 被调用，并将 JavaScript 函数`initMap`作为回调函数，为其提供创建地图和标记所需的信息。注意我们正在使用`defer`属性。

因为我们依赖页面上的数据集来获取坐标，所以我们必须确保在调用函数之前加载 DOM 内容。在调用 API 和使用`initMap`函数之前，`defer`属性会一直等到 HTML 被组装好。现在地图将被构建并放置在`<div id="map">`中。

现在，Rails 应用程序上有了一个完全动态的地图。希望本指南对您有所帮助。在外面保持安全和健康！