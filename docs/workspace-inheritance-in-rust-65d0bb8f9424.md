# Rust 中的工作空间继承

> 原文：<https://betterprogramming.pub/workspace-inheritance-in-rust-65d0bb8f9424>

## 看看 Rust 1.64 的新特性

![](img/b535ade3c80827af4cbddb290a50850d.png)

照片由[杆长](https://unsplash.com/@rodlong?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Rust 1.64 今天已经发布，这个版本将会影响数百个各种类型的网络项目。

这是因为它有一个关键特征，任何项目都可以分成许多不同的箱子。这个特性是**工作空间继承，**它将允许我们在板条箱之间共享像版本号或元数据字段这样的字段。`Cargo.toml`文件终于开始遵循干燥原则了。

# 告别复制粘贴许可证和作者

当他们创建一个新的板条箱时，首先要做的事情之一就是打开上一个板条箱的清单，并复制两者之间的所有公共部分——即使它们在同一个工作区中。

例如，在我用来展示代码示例的[库](https://github.com/kriogenia/medium/tree/9fb321343146aa9c67cf9309565f39c6ab55d77f)中，你可以看到我曾经在每个箱子中都有相同的`authors`键。现在我可以去掉所有的重复。怎么会？第一，把根`Cargo.toml`做成这样:

Cargo.toml 设置工作区的默认包

在这个新版本中，在`[workspace.package]`下，您可以定义[包部分的一些键](https://doc.rust-lang.org/cargo/reference/workspaces.html#the-package-table)作为工作区默认值。

这将允许工作空间板条箱选择加入并继承那些相同的部分，否则将被复制(在这个例子中，就是您在`workspace.members`中看到的存储库的所有板条箱)。

现在，为了利用这些缺省值，我们只需要将它们调用到其他清单中。比如现在这是`add_trait/Cargo.toml`:

新的 add_trait/Cargo.toml

现在,`add_trait`的清单将继承我们在根中为键设置的值，这些键是我们用`{key}.workspace`带来的，比如`edition`或`authors`。也看看我们如何忽略那些我们不想带的东西，比如`readme`，因为这个箱子里不会有；还是`description`，不一样的，我们是压倒性的。

有了这个新的清单，所有的项目将总是使用相同的版本，像改变许可证这样的更新将不必在每个项目中手动更新。

# 保持版本同步

但是，我们可以用这种新的继承做另一件事，这将是商业项目中的关键。这是使用继承来授予依赖性之间的同步性。为此，我们可以遵循与使用`[package]`相同的方法，但是使用`[workspace.dependencies]`和典型的依赖声明(*但是我们不能在这里使用*`*optional*`*)。*

*例如，将它添加到根`Cargo.toml`会将工作区默认版本和特性设置为这三个依赖项:*

*在根 Cargo.toml 中定义依赖关系*

*现在，在`generify_with_compiler_errors`中，我们可以将这些依赖项与工作区版本和特性一起使用，并在声明中声明它们——如下例所示:*

*继承工作区依赖项的版本和功能*

*看看我们是如何在工作区中以同样的方式声明`vector2d`的，但是我们是如何覆盖`num`依赖关系的，因为这个机箱需要使用默认特性，我们只是使用了相同的版本。这个机箱没有使用`rand`，但是我添加它是为了展示你如何在那些已声明的特性之上声明特性，并且不仅在`[dependencies]`中，而且在`[dev-dependencies]`和`[build-dependencies]`中带来依赖关系。*

*今天到此为止，如果你能检查整个新的 Rust 版本，你可以在这里[做](https://blog.rust-lang.org/2022/09/22/Rust-1.64.0.html)，并且看到实际工作中的代码，你可以在下面链接的库中找到它。*

*[](https://github.com/kriogenia/medium/) [## GitHub - kriogenia/medium:存放我在 medium 帖子上使用的示例的存储库

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/kriogenia/medium/) 

Rust 1.65 再见，我们将会看到那些期待已久的 **GATs** 。*