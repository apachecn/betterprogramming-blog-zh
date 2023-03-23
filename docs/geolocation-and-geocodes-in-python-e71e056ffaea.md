# Python 中的地理定位和地理编码

> 原文：<https://betterprogramming.pub/geolocation-and-geocodes-in-python-e71e056ffaea>

## 找到你需要的地址

![](img/71f0ec5244a7ecf883eb0bcf4d806a45.png)

照片由[марьянблан| @ marjanblan](https://unsplash.com/@marjan_blan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

找到一个不需要或不使用地理定位或不处理用户地址的应用程序可能是一个挑战。这确实是一个有用的特性，希望将它添加到您自己的服务中是可以理解的。

因此，让我介绍几个来自一对 Python 包的命令，它们使得与地理数据的交互更加容易。

# 1.获取当前位置

我们将使用[地理编码器包](https://github.com/DenisCarriere/geocoder)来获取当前位置，因为它有一个简单的 IP 地址处理接口。

我们有靠近我的位置的城市区域的坐标、我的家乡城市名称和国家代码。到目前为止，所有的信息都是正确的。但是，在`g.geojson`属性中可以看到更多。

# 2.按地理编码定位

对我来说，Geocoder 对于处理地址有点混乱。相反，我们将使用 [GeoPy 库](https://geopy.readthedocs.io/en/stable/#)来试试运气，它拥有数量惊人的地理编码服务和 API。

让我们试试默认的一个，名为 Nominatim，再次检索坐标。

我们看到了靠近市中心的位置。

# 3.按位置进行地理编码

你也可以反过来尝试这项服务——通过位置获取绑定的地理标签。幸运的是，GeoPy 正好提供了这样一个功能。

更好的是，我们获得了当前地区语言的地址(对我来说很好)。

# 4.测量距离

如果你的服务是面向旅行的，那么仅仅知道位置坐标是不够的。我们忽略了他们之间的距离。

幸运的是，GeoPy 内置了距离计算功能。比方说，我想去看埃菲尔铁塔:

如果我从我的家乡开始:

```
loc_dnipro = geolocator.geocode("Dnipro, Dnipropetrovsk, UA")
```

我应该走多远？

或者以英里为单位，如果你喜欢的话:

```
Out[7]: 1487.520100400978
```

对我来说太远了，但我们有相当精确的测量。并且库有[更多的算法](https://geopy.readthedocs.io/en/stable/#module-geopy.distance)用于计算。

# 结论

这是一个简短的介绍，但是您可以深入到这两个包的文档中并找到更多信息。您可以在我的 GitHub 中找到整个工作笔记本:

[](https://github.com/Midvel/medium_jupyter_notes/blob/master/geocode_and_distance/geocode-and-distance.ipynb) [## 中级/中等 _jupyter_notes

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/Midvel/medium_jupyter_notes/blob/master/geocode_and_distance/geocode-and-distance.ipynb) 

你使用了哪些有用的地理编码和位置技巧？