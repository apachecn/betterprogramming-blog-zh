# 如何在 Django 中有效使用 DRF 序列化器

> 原文：<https://betterprogramming.pub/how-to-use-drf-serializers-effectively-dc58edc73998>

## 使用源参数、SerializerMethodField 和 to_representation

![](img/cc9ebe8a7fb564d6ef23c6a2db32a32b.png)

Django REST 框架序列化程序

这篇文章假设读者对 Django REST 框架有基本的了解。

我们将讨论如何在读操作期间有效地使用序列化程序。我们将看看三个强大的特性，它们将帮助我们用更少的代码获得想要的结果。

我们将讨论:

*   使用序列化程序的`source`参数的多种方式。
*   如何以及何时使用`SerializerMethodField`。
*   如何以及何时使用`to_representation`。

# 如何使用 Source 关键字参数

DRF 序列化器提供了一个名为`source`的关键字参数，它非常聪明，可以帮助避免许多常见的模式。

让我们编写一个序列化程序，它可以创建一个`User`的序列化表示。

让我们使用这个序列化程序来序列化一个用户:

```
In [1]: from accounts.serializers import UserSerializerIn [2]: from django.contrib.auth.models import UserIn [3]: user = User.objects.latest('pk')In [4]: serializer = UserSerializer(user)In [5]: serializer.data
Out[5]: {'username': 'akshar', 'email': '[akshar@agiliq.com](mailto:akshar@agiliq.com)', 'first_name': '', 'last_name': ''}
```

假设使用这个 API 的前端或移动应用程序希望序列化表示中的键是`user_name`而不是`username`。

我们可以在带有`source`属性的序列化器上添加一个`CharField`来实现这一点。

确保用`Meta.fields`中的`user_name`替换`username`。重新启动 shell 并实例化序列化程序。

```
In [6]: serializer = UserSerializer(user)In [7]: serializer.data
Out[7]: {'user_name': 'akshar', 'email': '[akshar@agiliq.com](mailto:akshar@agiliq.com)', 'first_name': '', 'last_name': ''}
```

在上一个例子中，我们看到了`source`如何处理`User`字段。`source`也可以使用`User`上的方法。

`User`有一个方法叫做`get_full_name`。让我们设置`first_name`和`last_name`。

```
In [8]: user.first_name = 'akshar'In [9]: user.last_name = 'raaj'In [10]: user.save()In [11]: user.get_full_name()
Out[11]: 'akshar raaj'
```

让我们向序列化程序添加一个名为`full_name`的字段。设置其`source`使用`User.get_full_name`。

让我们重启 shell 并获取用户的序列化表示。

```
In [3]: user = User.objects.latest('pk')In [4]: serializer = UserSerializer(user)In [5]: serializer.data
Out[5]: {'user_name': 'akshar', 'email': '[akshar@agiliq.com](mailto:akshar@agiliq.com)', 'first_name': 'akshar', 'last_name': 'raaj', 'full_name': 'akshar raaj'}
```

注意`full_name`如何给出期望的结果。在引擎盖下，DRF 使用`get_full_name`来填充`full_name`。

`source`也可以无缝地处理关系，例如`ForeignKey`、`OneToOneField`和`ManyToMany`。

假设有一个与`User`有`OneToOne`关系的`Profile`模型。

概要文件模型如下所示:

假设我们希望以序列化表示发送街道和城市。我们可以在序列化器上添加一个街道和城市字段，并设置适当的`source`。

让我们重启 shell 并序列化用户。

```
In [4]: user = User.objects.latest('pk')In [5]: serializer = UserSerializer(user)In [6]: serializer.data
Out[6]: {'email': '[akshar@agiliq.com](mailto:akshar@agiliq.com)', 'first_name': 'akshar', 'last_name': 'raaj', 'street': 'Pennsylvania Avenue', 'city': 'Washington'}
```

`source`也可以无缝地处理相关对象的方法，就像它处理对象的方法一样。

我们希望获得用户的完整地址，可以使用`user.profile.get_full_address()`访问。

在这种情况下，我们可以将 source 设置为`profile.get_full_address`。

再次序列化用户:

```
In [3]: user = User.objects.latest('pk')In [4]: serializer = UserSerializer(user)In [5]: serializer.data
Out[5]: {'email': '[akshar@agiliq.com](mailto:akshar@agiliq.com)', 'first_name': 'akshar', 'last_name': 'raaj', 'full_address': 'Pennsylvania Avenue, Washington'}
```

让我们看看`source`和`ManyToManyField`是如何毫不费力地配合工作的。

我们还希望在序列化表示中获得相关的用户组。让我们先给用户添加几个组。

```
In [12]: g1 = Group.objects.create(name='BBC')In [13]: g2 = Group.objects.create(name='Sony')In [15]: user.groups.add(*[g1, g2])
```

我们想要与用户相关联的每个组的`id`和`name`。

我们需要编写一个`GroupSerializer`来序列化一个组实例。

它看起来像这样:

向序列化数据添加组的一个简单方法是定义一个`SerializerMethodField`并在方法中添加`user.groups.all()`。

一种 DRFish 方法是在序列化器上添加一个`all_groups`字段，并将其设置为`GroupSerializer`。

让我们序列化一个用户，并验证相关组的信息是否存在于序列化数据中。

```
In [1]: from accounts.serializers import UserSerializerIn [2]: from django.contrib.auth.models import UserIn [3]: user = User.objects.latest('pk')In [5]: serializer = UserSerializer(user)In [6]: serializer.data
Out[6]: {'email': '[akshar@agiliq.com](mailto:akshar@agiliq.com)', 'first_name': 'akshar', 'last_name': 'raaj', 'groups': [OrderedDict([('id', 2), ('name', 'BBC')]), OrderedDict([('id', 3), ('name', 'Sony')])]}
```

DRF 很聪明地调用了`user.groups.all()`，尽管我们刚刚设置了`source=groups`。DRF 推断出`groups`是一个`ManyRelatedManager`，因此调用管理器上的`.all()`来获取所有相关的组。

如果我们不想在`POST`通话期间提供群组信息，我们必须在`GroupSerializer`上添加关键字参数`read_only=True`。

假设有一个型号叫`Article`，`Article`有一个`ForeignKey`到`User`。我们可以通过以下方式在序列化表示中添加用户的文章:

```
articles = ArticleSerializer(source='article_set', many=True)
```

# 如何使用 SerializerMethodField

在特定字段的序列化过程中，有时需要运行一些自定义代码。

一些例子可能是:

*   在序列化期间将`first_name`转换为 titlecase。
*   将`full_name`转换成大写。
*   如果没有组与用户相关联，则将`groups`设置为`None`而不是空列表。

让我们考虑第一种情况。我们想在序列化期间将用户的`first_name`改为标题大小写。

直到现在，我们都不想为`first_name`运行任何自定义代码，所以在`Meta.fields`中有`first_name`就足够了。我们现在想运行一些自定义代码，所以我们必须显式地将`first_name`设置为`SerializerMethodField`。

当一个字段被设置为`SerializerMethodField`时，DRF 在计算该字段的值时调用一个名为`get_<field_name>`的方法。这里的`obj`指的是一个`user`实例。

重启 shell 并序列化用户。

```
In [4]: user = User.objects.latest('pk')In [5]: serializer = UserSerializer(user)In [6]: serializer.data
    Out[6]: {'email': '[akshar@agiliq.com](mailto:akshar@agiliq.com)', 'first_name': 'Akshar', 'last_name': 'raaj'}
```

请注意`first_name`现在是如何序列化为标题案例的。

如果我们想将`full_name`改为大写，我们必须将序列化程序改为如下形式:

让我们再次序列化用户:

```
In [3]: user = User.objects.latest('pk')In [4]: serializer = UserSerializer(user)In [5]: serializer.data
Out[5]: {'email': '[akshar@agiliq.com](mailto:akshar@agiliq.com)', 'first_name': 'akshar', 'last_name': 'raaj', 'full_name': 'AKSHAR RAAJ'}
```

如果我们想将`groups`作为`None`发送，而不是一个空列表，我们的序列化程序应该是这样的:

# 如何使用 to_representation

序列化器提供了一个挂钩点，称为`to_representation`。

假设我们想在序列化数据中添加一个名为`admin`的键，只有当用户是超级用户时。对于非超级用户，键`admin`不应该出现在序列化数据中。

我们的序列化程序应该是这样的:

`instance`指用户实例序列化。

让我们序列化一个超级用户。

```
In [2]: user = User.objects.latest('pk')In [5]: serializer = UserSerializer(user)In [6]: serializer.data
Out[6]: {'email': '[akshar@agiliq.com](mailto:akshar@agiliq.com)', 'first_name': 'akshar', 'last_name': 'raaj', 'admin': True}
```

让我们将用户标记为非超级用户并再次序列化。

```
In [7]: user.is_superuser = FalseIn [8]: user.save()In [9]: serializer = UserSerializer(user)In [10]: serializer.data
Out[10]: {'email': '[akshar@agiliq.com](mailto:akshar@agiliq.com)', 'first_name': 'akshar', 'last_name': 'raaj'}
```

注意，非超级用户的序列化数据中缺少`admin`键。

# 结论

这篇文章讨论了序列化器的读取行为。如果您想了解如何在写操作期间有效地使用序列化器，[请参阅本文](https://medium.com/@raaj.akshar/how-to-effectively-use-django-rest-framework-serializers-during-write-operations-dd73b62c26b5)。

在 [Twitter](https://twitter.com/_akshar) 上与我联系，我[在 Twitter](https://twitter.com/_akshar)上发布信息丰富且有价值的编程文章和建议。