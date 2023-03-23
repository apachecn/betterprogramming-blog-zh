# 如何在 Rails 6 中使用枚举

> 原文：<https://betterprogramming.pub/how-to-use-enums-in-rails-6-87600e292476>

## 枚举是由一组命名值组成的数据类型。让我们利用它们

![](img/6d76dbb37c6f58d90db962adf4256dd9.png)

如何在 Ruby on Rails 中使用枚举？作者照片。

在 Ruby on Rails 中，enum 是一个属性，其中的值映射到数据库中的整数，并且可以通过名称进行查询。

例如，我们可以为`status`属性定义一个枚举，其中可能的值是`pending`、`active`或`archived`。

Ruby on Rails 在 Rails 4.1 中增加了对枚举的支持。

# Rails 中枚举的基本用法

向现有模型中添加一个枚举就像向该表中添加一个整数列一样简单。您可以使用以下 bash 命令生成新的迁移:

```
$ bundle exec rails g migration AddStatusToUsers status:integers
```

这将生成以下迁移:

```
class AddStatusToUsers < ActiveRecord::Migration[6.0]
  def change
    add_column :users, :status, :integer, default: 0
  end
end
```

使用`bundle exec rails db:migrate`运行迁移。在您的`User` 模型中，您需要将枚举定义为:

如果您想知道模型文件顶部的*模式信息*注释是怎么回事，那就是`annotate` gem。看看这篇关于如何将`annotate`与你的 Rails 应用集成的文章[。](https://medium.com/the-side-hustler/how-to-use-the-annotate-gem-c44bfec97d03)

现在，您可以使用所有提供的枚举助手了。让我们来看看其中的一些。

## 平等检查

我们可以通过使用`user.status == 'active'`或 enum 助手来检查用户是否“活跃”:

```
user.active? # Returns true if user.status == 'active'
```

## 更新枚举

Ruby on Rails 还提供了一个助手来更新枚举值。代替`user.update(status: :archived)`，我们可以使用:

```
user.archived! # same as user.update(status: :archived)
```

## 领域

我想谈的最后一个枚举助手是作用域的自动生成。常规的方法是使用一个`where` 查询，就像`User.where(status: 'active')`一样。使用 enum 助手，可以将该查询转换为:

```
User.active
```

# 逮到你了

## 1.定义枚举时使用哈希而不是数组

如果您决定使用数组定义(`enum status: [:pending, :active, :archived]`)，那么更改枚举值的顺序将会破坏映射，因为您可能已经在`user.status is 0`处存储了数据，这些数据曾经被映射到`pending` ，但是现在可能映射到其他地方。

为了避免这个缺点，请将枚举声明为哈希。

## 2.在枚举定义中使用前缀或后缀

默认的枚举行为可能导致不直观的范围(考虑`User.active`)。建议定义`_prefix`选项或`_suffix`。我发现以下设置是最直观的:

```
enum status: {
    pending: 0,
    active: 1,
    archived: 2
}, _prefix: true
```

这意味着所有的助手都将带有前缀`status`:

```
user.status_pending? # status == 'pending'user.status_active! # update(status: :active)User.status_archived # User.where(status: :archived)
```

Catalin ( [@cionescu1](https://twitter.com/cionescu1) )是 Ruby on Rails 的顾问，是免费约会管理软件 organisally[的创始人，也是](https://www.organisely.app/)[Modern Rails——在 Ruby on Rails](https://medium.com/modern-rails/building-a-modern-crm-in-ruby-on-rails-part-1-3b62f7b4dc7d) 中构建 CRM 的作者。