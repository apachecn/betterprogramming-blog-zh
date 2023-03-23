# 将 UUID 作为默认主键来支持 Rails 应用程序

> 原文：<https://betterprogramming.pub/empowering-a-rails-application-with-uuid-as-default-primary-key-44cd740828e8>

## 对 UUID 额外津贴的简要概述，以及如何在 Rails 应用程序中将它用作主键的快速入门指南

![](img/1fff5957739956f7fc0d6ce0ef6c4fa3.png)

由[杰西卡·鲁斯切洛](https://unsplash.com/@jruscello?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

我想你们大多数人都熟悉什么是 UUID，它看起来像什么。所以，我就跳过介绍，直奔主题了。

# 动机

## 混淆 URL 中的信息

如果我们使用一个递增的整数作为 ID，我们将在 URL 中公开可以从外部推断的信息。记录的总数可能是我们想要保密的事情之一。

我们需要实施和维持额外的机制来实现这一目标。另一方面，通过使用 UUID，我们不需要担心这个问题，因为它是开箱即用的。

```
# incrementing integer VS universally unique identifierlocalhost:3000/articles/36
localhost:3000/articles/fc9b45f6–94eb-47b9-b029–3bc55f810a35
```

## 附加安全层

使用 UUID 作为主键也可以作为附加的安全层。它可以防止恶意攻击者通过猜测 URL 中的`id`试图访问数据。与递增整数相反，UUID 极难猜测。

然而，我强烈支持首先实现基于权限的授权。简单地拥有特定的 UUID 根本不意味着被授予访问权。

## 世界上独一无二的

我知道这听起来很可怕，但是使用 UUID 方法，您可以在客户端甚至在其他系统中分配对象 ID。因此，可以消除 API 调用的开销。这在批处理操作中尤其有用。

当根据标准方法生成时，出于实用目的，UUIDs 是唯一的。

与大多数其他编号方案不同，它们的唯一性不依赖于中央注册机构或生成它们的各方之间的协调。虽然 UUID 被复制的概率不为零，但它已接近于零，可以忽略不计。

# 使用指南

## 介绍

完成这项工作的过程和困难取决于您将使用什么样的数据库。我个人的首选是 [PostgreSQL](https://www.postgresql.org/) ，因为我认为这是最好的选择。如果你也在使用 PostgreSQL，那么这个简短的指南就是为你准备的。

这是一个简单的过程，几乎没有性能成本。对于其他数据库来说，设置这个可能是一个完全不同的游戏。我相信网上有很多资源可以让你学到更多。这可能是一个复杂的过程，不值得奖励。无论如何祝你好运！

也就是说，下面是将 UUID 设置为 PostgreSQL 数据库主键的步骤。

## 创建迁移

要在 PostgreSQL 中启用 UUID，您需要创建并运行以下迁移。

```
# frozen_string_literal: trueclass EnableUuid < ActiveRecord::Migration[6.0]
  def change
    enable_extension 'pgcrypto'
  end
end
```

## 实现初始值设定项

此时，我们需要配置新的表来使用 UUID 作为它们的主键，因为默认情况下不处理它。

为了避免重复的工作，我们将创建并运行下面的初始化器。这样，在生成迁移时，UUID 将默认用作主键。

`config/initializers/generators.rb`

```
# frozen_string_literal: trueRails.application.config.generators do |g|
  g.orm :active_record, primary_key_type: :uuid
end
```

## 设置模型关系

我们应该记住的一件事是，在数据库级别定义模型关系时，我们需要明确。因为 UUID 被用作主键，所以我们需要外键也是相同的类型。让我们看看下面的例子。

假设我们想要有`Article`和`Author`模型，并且它们应该是一对多的关系。

对于`Article`模型，我们希望它属于一个`Author`。因此，我们需要在迁移中定义一个引用。额外的步骤通常是我们需要明确定义 UUID 是外键的类型。

我们可以通过将`type: :uuid`部分添加到参考定义中来实现。创建`articles`表的迁移应该如下所示。

## 处理陷阱

除了它的众多优点之外，UUID 作为主键的方法也有它的缺陷，社区也解决了这些问题。其中一个无可争议的是`.first`和`.last` `ActiveRecord::Relation`将不再像预期的那样工作。

我们已经知道，这些方法使用`id`列进行排序。在该列中使用 UUID 值而不是通常的递增整数会导致令人惊讶的行为。

然而，Rails 6 引入了[覆盖用于隐式排序的列](https://github.com/rails/rails/pull/34480)的可能性。

我们可以在我们的基本抽象模型中使用这种能力，并利用`created_at`字段。因此，`.first`和`.last`将再次为继承它的所有模型正常工作。这是它应该看起来的样子。

```
# frozen_string_literal: trueclass ApplicationRecord < ActiveRecord::Base
  self.abstract_class = true
  self.implicit_order_column = 'created_at'
end
```

这应该是设置应用程序使用 UUID 作为主键的最后一步。如果您有任何问题或顾虑，请随时联系我们。干杯！

# *免责声明*

在一个全新的项目中使用 UUID 作为主键是一个好主意。由你决定。

然而，我建议避免活动项目的迁移，除非真的有必要。这可能是一项具有挑战性的任务，风险很高。所以要特别小心对待。