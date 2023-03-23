# 为什么 Ruby on Rails 不会很快消亡

> 原文：<https://betterprogramming.pub/why-ruby-on-rails-will-not-die-anytime-soon-c636e3343b8f>

## 流行的 web 框架仍然吸引工程师的 3 个原因

![](img/3f57e88df7f2e7a733914a194a5d1977.png)

由[大卫·赫伦](https://unsplash.com/@dj_david?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/rails?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

我花了将近十年的时间研究静态类型编程语言。我热爱 C++，也是 C#的忠实粉丝。我受到启发，开始学习 Kotlin 并每天使用它。它们很棒，我从来不明白用动态类型语言怎么可能构建软件。直到我尝试了 Ruby。然后我的世界变了。

现在我每天都在使用 Ruby。但更重要的是框架。归根结底，语言只是一种工具。当一门伟大的语言被用来构建一个糟糕的框架时，情况可能就是这样。当框架和语言都很棒的时候，事情就完全不同了。软件开发人员乐于构建每一个功能。其中一个框架是 Ruby on Rails。

有些人说 Ruby on Rails 已经死了，我们必须继续前进，选择一个新的框架或者像 Java 这样已经被证明了几十年的东西。让我们来看一下摧毁这种错误认知的要点。

# 迅速发展

快速开发是 Rails 框架的主要特点。在有限的时间内建立一个工作原型是可能的。这是通过多种因素实现的:每个人都遵循的强约定，Ruby 编程语言的极简主义，在其他应用程序中广泛使用的简单架构。

作为一个例子，我们可以看看典型的基于 REST API 的应用程序。我们需要构建端点来执行 CRUD 操作。

```
class UsersController < ApplicationController def create
    # create new user
  end def show
    # Get specific user by parameter (e.g. ID)
  end def destroy
    # delete specific user by parameter
  endend
```

对于一个非常简单的应用程序，上面的代码几乎是完整的。我们必须添加一些逻辑来获取必要的数据，就这样。

但是实现这些方法的逻辑也是一项相对简单的任务。活动记录是 Rails 中的对象关系映射(ORM)系统。它帮助工程师避免编写复杂的 SQL 查询。开发人员必须编写常规的 Ruby 代码来操作数据。例如，代码`User.where(id: 1)`将被转换为`SELECT * FROM Users WHERE ID = 1`。阅读和管理代码比维护 SQL 查询更简单。尤其是当涉及到复杂的查询时。

这些只是解释为什么构建解决方案可以很快完成的几个关键因素。Rails 提供了很多东西。

# 坚实的基础

Ruby on Rails 在 2004 年发布了初始版本。在过去的 17 年里，它只是在改进和变得成熟。不是大家一年就忘了的“另一个花式”框架。

Ruby on Rails 最棒的地方在于，它从一开始就专注于模型-视图-控制器(MVC)模式，并且一直坚持下去。在每个主要版本中都有一些小的修改。但是主要思想和方法保持不变。

十年前实现的架构仍然有效并且易于维护。开发人员正在使用已建立的模式和架构来构建新的功能。没有人试图像在其他一些框架中那样发明轮子，在这些框架中，架构根据社区中最近的趋势而变化。

Ruby on Rails 遵循许多约定，它迫使开发人员遵守这些约定。例如，如果您为一个类选择了错误的名称，应用程序的某些功能可能无法工作。这使得工程师在项目之间切换更加容易。当每个人都继续使用它们时，它节省了时间。节省下来的时间可以用于构建新产品，而不是开发新的架构。

# 伟大的社区

因为 Ruby on Rails 是一个开源项目，所以它是社区驱动的也就不足为奇了。该框架是活的，并且改进不断出现。随着每一个新版本的推出，更多令人兴奋的特性出现了。这是至关重要的，并保持增长。

社区支持每一个贡献者。这就是为什么有如此大量的可用库的原因。从字面上看，你可以找到任何东西的宝石。对于增强的安全性和身份验证、更简单的分页、更快的单元测试运行速度等等，都有很好的解决方案。如果您开始自己的 Rails 开源项目，获得活跃用户和贡献者将不成问题。

在 Rails 中跟踪更新和新闻是很容易的，因为很少有时事通讯。[乘坐 Rails](https://weblog.rubyonrails.org/) 和[本周在 Rails](https://rails-weekly.ongoodbits.com/) 只证明 Rails 没有死。定期阅读它们是令人兴奋的，并且有助于与框架保持同步。

全球各地的当地社区定期聚集在各自的城市，分享知识和经验。 [RailsConf](https://railsconf.org/) 是 Ruby on Rails 爱好者最大的会议，每个开发人员都想去参加。在线社区随时准备帮助解决问题，并解释事情是如何运作的。

# 最后的想法

怀疑者说 Ruby on Rails 性能不好，慢慢失去人气。然而，当我们检查使用该框架的公司数量时，它似乎并不真实。然而，性能并不是最好的。但是以公司得到的便利和发展速度，这是可以商量的。软件工程师已经想出了避免常见陷阱和充分利用 Rails 的最佳实践。

因为整个项目是由社区支持的，所以不可能让它停止，直到社区允许为止。当你每周阅读一篇新的时事通讯时，你会意识到 Ruby on Rails 将会存在很长一段时间。