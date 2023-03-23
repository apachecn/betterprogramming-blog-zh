# 如何在 Rails 中实现分页

> 原文：<https://betterprogramming.pub/pagination-in-rails-b3a9ba25b3c3>

## 使用 K `aminari Gem`

![](img/8a654059edf895bf2655e9a2335829a9.png)

一个应用程序可以有很多资源。当一个模型拥有完整的 CRUD 功能时，它就在不断地成长。这使得浏览该资源的索引页面成为一项繁重的工作，而且对用户不太友好。为了避免无休止的滚动和清理，分页是一个好主意。

就 web 应用程序而言，分页是指将一大组资源分成多个页面，并在每个页面上平均分配一定数量的资源。通常在底部会有一个导航索引，标明页数和用户当前正在查看的页面，以及一些到资源列表开头或结尾的快捷方式。

![](img/9d94fa1d60df91ab88915da5744b0d8f.png)

分页的基本示例

在 Rails 应用程序中，分页器(如果不使用阿诺德·施瓦辛格的声音，就不可能大声说出来)使这一点变得非常容易。它与 ActiveRecord 一起在控制器调用中划分资源，并为每页分配指定的资源量。有许多分页宝石可供选择，如 [kaminari](https://github.com/kaminari/kaminari) 、 [pagy](https://github.com/ddnexus/pagy) 或 [will_paginate](https://github.com/mislav/will_paginate) 。对于这篇文章，我将使用`kaminari`来展示如何清理那些永无止境的索引页。

首先，将`gem 'kaminari'`放在项目目录的`Gemfile.rb`中安装 gem，然后运行`bundle`。安装 gem 后，您现在可以使用`model.rb`文件中的`paginates_per`方法指定每页显示的数量:

上面的例子显示，每页将有 25 篇文章。接下来要做的是确保我们设置了一些用户友好的网址。在`config/routes.rb`中，页面可以嵌套在 Post 的索引路径中，如下所示:

如果在帖子的第四页:`/posts/page/4`，路线现在看起来会像这样。没有必要这样做，但是要知道，如果你不这样做，路线将会变成这样:`/posts?page=4`。最好是一切都很好，休息，对不对？

继续看控制器，在`posts#index`中:

Kaminari 不会组织资源，所以如果我们想让每个页面上的文章按字母顺序排列，我们可以在文章模型上调用`order`方法。使用`kaminari` gem 提供的`page`方法，将根据`params[:page]`中包含的数字来选择帖子。

最后，在视图文件`post.html.erb`中，我们可以调用同样由 kaminari 提供的帮助方法:

```
<%= paginate @posts %>
```

这将呈现一个`<nav>`标签，其中包含所有需要的页面链接。就是这样！我们的资源被分成页面，每个页面最多有 25 个帖子，并且它们有一些不错的 RESTful 路径。

有了像`kaminari`这样的分页器，人们可以快速组织他们的大量资源，以获得更快的导航、更清晰的视图和更好的用户体验。