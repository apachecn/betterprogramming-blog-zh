# 如何实现自定义活动记录验证

> 原文：<https://betterprogramming.pub/how-to-implement-custom-activerecord-validations-235543f5dd8c>

## 不要让坏数据进入你的数据库

![](img/6ec5cc462e0bbf75c1a7d8ae85cce4b6.png)

斯科特·韦伯在 [Unsplash](https://unsplash.com/s/photos/validation?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

验证对于我们应用的安全性和稳定性非常重要。我们绝对不希望任何类型的数据进入我们的数据库。

幸运的是，Rails 为我们提供了一些活跃的记录验证助手。然而，有时我们需要的不仅仅是 Rails 所提供的——我们需要设置定制的验证器。

在这篇短文中，我们将探索实现定制活动记录验证的三个选项。我们的示例模型是一个简单的`Table`模型，具有`legs (integer)`、`top (integer)`和`material (string)`属性。

对于一个有效的`table`记录来说，它必须有四条腿，一个顶，材料必须是“木头”或“铁”。我们将使用三个验证选项中的一个来验证每个属性。我们走吧。

# **1。自定义方法**

这里，我们将为`Table`类定义一个私有方法来检查`legs`的值，如果它存在的话。如果值不等于 4，我们向记录的`errors`集合添加一条错误消息。

接下来，为了实施验证，我们调用`validate`方法并传入上面定义的验证方法的符号。

现在，从 Rails 控制台，我们得到以下结果:

```
Table.new(legs: 4, top: 1, material: “wood”).valid? => trueTable.new(legs: 3, top: 1, material: “wood”).valid? => falseTable.new(legs: 3, top: 1, material: “wood”).errors.full_messages => [“Legs must be 4”]
```

注意:您可能需要重新启动控制台，并将记录保存在一个变量中，以获得正确的结果。

# **2。自定义验证器(用于记录)**

我们可以定义一个验证器，根据我们的自定义条件来检查记录的有效性。这个验证器将是一个继承自`ActiveModel::Validator.`的类

现在，我们将定义一个定制的验证器来检查一个`table`记录的`top`属性。请记住，我们的表格必须只有一个顶部才有效。

首先我们创建文件`app/models/concerns/top_validator.rb` ，并在里面定义我们的自定义验证器类。

在我们的`TopValidator`类中，我们定义了一个 validate 方法，该方法将记录作为参数，并检查记录的验证条件。如果条件不满足，我们将向记录的错误集合中添加一条错误消息。

为了实施这种验证，我们在模型中调用了`validates_with`方法，并向它传递了我们的验证器类名(在本例中，不是一个符号)。

现在，我们从 Rails 控制台得到以下结果:

```
Table.new(legs: 4, top:1, material: “wood”).valid?
=> trueTable.new(legs: 4, top:2, material: “wood”).valid?
=> false Table.new(legs: 4, top:2, material: “wood”).errors.full_messages
=> [“Top must be equal to 1”]
```

# **3。自定义验证器(针对一个属性)**

这里，我们定义了一个继承自`ActiveModel::EachValidator`的验证器类来验证一个特定的属性是否满足我们的定制条件。如果没有，我们将向记录的错误集合中添加一条错误消息。

现在让我们定义一个定制的`type`验证器，我们可以用它来检查`app/models/concerns/type_validator.rb` *中表格的`material`属性。* 记住，必须是“木”或“弦”。

在该类中，我们定义了一个`validate_each`方法，它将记录、属性和值作为参数。我们检查我们的条件的属性值，如果它不满足条件，我们向记录的 errors 集合添加一条错误消息。

为了实施这种验证，我们调用`validates`并将属性名作为一个符号和一个元素的散列传入，该元素的键设置为验证器名，值设置为 true。

现在，我们有了以下结果:

```
Table.new(legs: 4, top:1, material: “wood”).valid?
=> trueTable.new(legs: 4, top:1, material: “clay”).valid?
=> falseTable.new(legs: 4, top:1, material: “clay”).errors.full_messages
=> [“Material must be either wood or iron”] 
```

我希望这能对你有所帮助。谢谢你的时间。

# 资源

*   [Ruby on Rails —活动记录验证](https://edgeguides.rubyonrails.org/active_record_validations.html)